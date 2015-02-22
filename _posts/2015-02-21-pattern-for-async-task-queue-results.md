---
title: "A Pattern for Async Task Queue Results"
---

Suppose we have a web app in which the user triggers a slow, expensive task, like transcoding a video or generating a report. We've wisely chosen to offload the job to a background worker, leaving the web server free to handle other requests (and if not, get started with [this helpful overview of the pattern](https://devcenter.heroku.com/articles/background-jobs-queueing) by our comrades at Heroku). Our app scales but now we have a new problem: how do we notify the user when the task finishes?

There's a few solutions available. One is to use a non-blocking server like [Tornado](http://www.tornadoweb.org/) or [Node.js](http://nodejs.org) that can keep a connection to the client alive until the task completes while concurrently serving other requests. If we're using one of these (and some like Tornado are excellent web frameworks regardless of their async aptitude), then this is a valid approach. An alternative solution is required though if we're rolling with Django or Ruby on Rails or another blocking web framework.

Another idea is to have the client periodically ask the server if the task is finished. When done poorly this results in wasted requests and fails to provide results immediately as they're available, but it leads us in the right direction. Instead of polling, the back end can efficiently push results to the client via WebSockets. In true [microservices fashion](http://martinfowler.com/articles/microservices.html), this component --- a service which exists for the sole purpose of pushing messages to the client --- can be independent from the rest of our stack using whatever technology is most suitable for the job.

In addition to our two services --- a web server and a background worker --- we'll run a third "notifier" service tasked with maintaining a connection to the client and telling them when a task completes. When the web server receives a request to run a task, it offloads it to the background worker, which upon completing the task passes the result to the notifier service, which sends the result back to the client.

```
                       +-------------------+
                       |                   |
          +----------> | Web Server        +------------+
          |            |                   |            |
          |            +-------------------+            |
          |                                             v
          |
+---------+---------+                         +-------------------+
|                   |                         |                   |
| Browser           |                         | Background Worker |
|                   |                         |                   |
+-------------------+                         +---------+---------+
                                                        |
          ^                                             |
          |            +-------------------+            |
          |            |                   |            |
          +------------+ Notifier Service  | <----------+
                       |                   |
                       +-------------------+
```

Let's demonstrate this pattern by creating a simple web app that does what we described above.[^1] We'll build our demo app using Python and Node.js because YOLO, but the technique transcends specific languages and frameworks.

## The Web Server

We'll start with a minimal app using [Flask](http://flask.pocoo.org), a Python web framework. It has two endpoints. Visiting the root renders HTML. Sending a POST request to `/runtask` triggers a computationally expensive task (which we'll define later).

```python
# server.py
from flask import Flask, render_template
from worker import mytask

app = Flask(__name__, template_folder='.')

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/runtask', methods=['POST'])
def runtask():
    mytask.delay()
    return 'running task...', 202
```

Our *index.html* page contains two elements: a button that sends an AJAX request to `/runtask`, and a div that displays the result of the task.

```html
<!-- index.html -->
<!doctype html>
<html>
<head>
	<meta charset="utf-8">
	<title>Task Example</title>
</head>
<body>

    <button type="button">Run Task</button>
    <br>
    Result: <span id="result"></span>

    <script>
        var resultElement = document.getElementById('result');

        document.querySelector('button').onclick = function() {
            var request = new XMLHttpRequest();
            request.open('POST', '/runtask', true);
            request.onload = function() {
                resultElement.textContent = request.responseText;
            };
            request.send()
        };
    </script>

</body>
</html>
```

Run the server using [Gunicorn](http://gunicorn.org) after installing the required packages.

```bash
pip install flask gunicorn
gunicorn --bind=0.0.0.0:5000 server:app
```

## The Background Worker

For our background worker we'll use the excellent [Celery](http://www.celeryproject.org). It defines a single computationally expensive task, which we'll simulate using `time.sleep`.

```python
# worker.py
import time
from celery import Celery

broker = 'amqp://localhost:5672'
app = Celery(__name__, broker=broker)

@app.task
def mytask():
    """Simulates a slow computation."""
    time.sleep(5)
    return 42
```

I said that we'd have three separate services running, but we need four. We require a message broker to transport jobs between our web server and our background worker. [RabbitMQ](http://www.rabbitmq.com) fits the bill. We'll use Docker to fire it up on its default port.

```bash
docker run --publish=5672:5672 --detach rabbitmq:3.4
```

Install Celery then start the background worker.

```bash
pip install celery
celery --app=worker:app worker
```

## The Notifier Service

At this point we have a barebones web server that offloads a task to a background worker, freeing itself up as soon as possible to handle the next request. View our handiwork by visiting http://localhost:5000. Now onto the juicy assignment of communicating the result to the client.

As mentioned earlier, WebSockets is well suited for maintaining the long-lived connection between client and server that we need. Support among modern browsers is [respectable](http://caniuse.com/#feat=websockets), with packages like [Socket.IO](http://socket.io/) providing fallback for antiques like Internet Explorer 9. Let's use Socket.IO for this demo. In addition to ensuring our app works in ancient clients, it provides convenient features like automatic reconnection and libraries that make setup a breeze.

Create a file named *notifier.js*. This is a minimal Node.js + [Express](http://expressjs.com) + Socket.IO app with a single POST endpoint `/notify`.

```javascript
// notifier.js
var app = require('express')(),
    server = require('http').Server(app),
    io = require('socket.io')(server),
    bodyParser = require('body-parser');

// Accept URL-encoded body in POST request.
app.use(bodyParser.urlencoded({ extended: true }));

// Echo the client's ID back to them when they connect.
io.on('connection', function(client) {
	client.emit('register', client.id);
});

// Forward task results to the clients who initiated them.
app.post('/notify', function(request, response) {
    var client = io.sockets.connected[request.body.clientid];
    client.emit('notify', request.body.result);
	response.type('text/plain');
    response.send('Result broadcast to client.');
});

server.listen(3000);
```

The first interesting bits are lines 7 -- 9. When the client first connects to the notifier service, we echo their Socket.IO identifier back to them. The client later passes this string along with requests it makes to the server, which forwards it along to the background worker. When the background worker completes a task, it calls the `/notify` endpoint (lines 15 -- 20) with the client ID as a POST parameter. The notifier service thens find the appropriate client to send the task result back to.

In *index.html*, include the *socket.io.js* library (served by our Node.js app) and modify the existing JavaScript to look like the following. We subscribe to two events, `register` and `notify`, and include the client ID in the POST request that the button click triggers.

```html
<!-- index.html -->
<script src="http://localhost:3000/socket.io/socket.io.js"></script>
<script>
    var resultElement = document.getElementById('result'),
        client = io('http://localhost:3000'),
        clientid = null;

    client.on('register', function(id) {
        clientid = id;
    });

    client.on('notify', function(result) {
        resultElement.textContent = result;
    });

    document.querySelector('button').onclick = function() {
        var request = new XMLHttpRequest();
        request.open('POST', '/runtask', true);
        request.setRequestHeader(
            'Content-Type',
            'application/x-www-form-urlencoded; charset=utf-8');
        request.onload = function() {
            resultElement.textContent = request.responseText;
        };
        request.send('clientid=' + clientid);
    };
</script>
```

In *server.py* we grab the client ID from the POST request and pass it along to the worker.

```python
# server.py
from flask import Flask, render_template, request

...

@app.route('/runtask', methods=['POST'])
def runtask():
    clientid = request.form.get('clientid')
    mytask.delay(clientid=clientid)
    return 'running task...', 202
```

In *worker.py* we modify our task to accept a `clientid` argument. We also subclass Celery's `Task` class so that it calls our notifier service when the task completes. To make life easy let the [Requests](http://docs.python-requests.org) library execute the POST request.

```python
# worker.py
import time
import requests
from celery import Celery, Task

import requests

class NotifierTask(Task):
    """Task that sends notification on completion."""
    abstract = True

    def after_return(self, status, retval, task_id, args, kwargs, einfo):
        url = 'http://localhost:3000/notify'
        data = {'clientid': kwargs['clientid'], 'result': retval}
        requests.post(url, data=data)

broker = 'amqp://localhost:5672'
app = Celery(__name__, broker=broker)

@app.task(base=NotifierTask)
def mytask(clientid=None):
    """Simulates a slow computation."""
    time.sleep(5)
    return 42
```

Install Socket.IO and the other dependencies then fire up *notifier.js*. We set it to run on port 3000.

```bash
npm install body-parser express socket.io
node notifier.js
```

If we put the pieces together correctly, our four services should now run in tandem and the client's browser should update to show the result after they trigger the task. Rock and roll.

## Dockerization

Now that all these services run smoothly together, let's package them up for easy deployment to a production environment. This step is optional but it improves life when it's time to go live. This is where Docker shines. First we'll immortalize the project's dependencies in *requirements.txt* (for Python / pip) and *package.json* (for Node.js / npm) files.

```
# requirements.txt
celery==3.1.17
flask==0.10.1
gunicorn==19.2.1
requests==2.5.1
```

```json
{
  "name": "notifier",
  "dependencies": {
    "body-parser": "^1.10.1",
    "express": "^4.10.7",
    "socket.io": "^1.3.4"
  }
}
```

With these in place our three Dockerfiles (one for each service we built) are minimal.

```
# Dockerfile.server
FROM python:3.4-onbuild
EXPOSE 5000
CMD ["gunicorn", "--bind=0.0.0.0:5000", "server:app"]
```

```
# Dockerfile.worker
FROM python:3.4-onbuild

# Create new user to keep Celery from complaining about being run as root.
RUN groupadd -r celery && useradd -r -g celery celery
USER celery

CMD ["celery", "-A", "worker:app", "worker"]
```

```
# Dockerfile.notifier
FROM node:0.12-onbuild
EXPOSE 3000
CMD ["node", "notifier.js"]
```

We also need to tweak our code so that the services talk to each other using [Docker linking](https://docs.docker.com/userguide/dockerlinks/), which provide the IP addresses and ports of the other services via environment variables (right now the host is always `localhost` and the ports are hardcoded). Instead of describing the necessary tweaks here, peruse the final code in the [GitHub repo](https://github.com/mminer/blog-code/tree/master/pattern-for-async-task-queue-results).

Now build the Docker containers.

```bash
docker build --tag=mminer/myserver --file=dockerfiles/Dockerfile.server .
docker build --tag=mminer/myworker --file=dockerfiles/Dockerfile.worker .
docker build --tag=mminer/mynotifier --file=dockerfiles/Dockerfile.notifier .
```

And finally let's use [Fig / Docker Compose](https://github.com/docker/fig) to save us from manually running `docker up`.

```yaml
# fig.yml
server:
    image: mminer/myserver
    ports:
    - "5000:5000"
    links:
    - notifier
    - rabbitmq

worker:
    image: mminer/myworker
    links:
    - notifier
    - rabbitmq

notifier:
    image: mminer/mynotifier
    ports:
    - "3000:3000"

rabbitmq:
    image: rabbitmq:3.4
```

Now a single `fig up` runs our four services in unison and we call it a day.

```bash
fig up
```

## Wrapping Up

Before deploying this app live, consider security and scalability. For one thing, the client can send any client ID they want along with their POST request, causing grief for other users. Also, keeping all information about connected clients in memory on a single Node.js app is hardly the definition of durability. With extra attention though, we can take this pattern and use it to great effect in our app, reducing strain on our web server while delivering results asynchronously to our users.

Task notifications. Asynchronous communication. All part of the American Dream.

---

[^1]: Code snippets [on GitHub](https://github.com/mminer/blog-code/tree/master/pattern-for-async-task-queue-results).

*[YOLO]: You Only Live Once
