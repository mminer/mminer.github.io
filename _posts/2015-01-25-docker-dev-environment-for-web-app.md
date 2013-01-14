---
title: "Docker Dev Environment for Web App"
---

The scenario: you're building a web app and want to hop on the Docker train (mixing metaphors like a champ), but fitting this hot new container tech into your development workflow has you flummoxed. Your dev environment should mirror production's, so running your app from a Docker container in both is a smart choice. Unfortunately, at least at first glance, this sacrifices the convenience of running the app from your local file system.

The good news is that with some initial setup, and not much at that, you can rock a dev environment identical to production without losing niceties like auto-reload and isolation from your host system.

In this tutorial we build a [Flask](http://flask.pocoo.org) web app using [Gunicorn](http://gunicorn.org) as our HTTP server.[^1] This makes these instructions a tad Python-centric, but the main ideas (and there's only a few of them, no biggie) are applicable to other languages and frameworks.

## The Web App

Our web app comprises five files.

```
.
├── Dockerfile
├── app.py
├── fig.yml
├── gunicorn.py
└── requirements.txt
```

### requirements.txt

This file specifies the Python packages to install. We need two: Flask and Gunicorn.

```
# requirements.txt
flask==0.10.1
gunicorn==19.1.1
```

### app.py

Nothing special here, just a bare bones web app that prints "Hello World" when we visit the root.

```python
# app.py
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello World!'
```

### gunicorn.py

Instead of passing the `gunicorn` binary a buttload of command line arguments, we load its configuration settings from a separate file. We can stick any logic we want in here, which will be useful later. For now it contains a single option telling Gunicorn the host and port to run on.

```python
# gunicorn.py
bind = '0.0.0.0:80'
```

### Dockerfile

And now the file that Docker uses to build an image.

```docker
# Dockerfile
FROM python:3.4-onbuild
EXPOSE 80
CMD ["gunicorn", "--config=gunicorn.py", "app:app"]
```

There's surprisingly little in this file. The first line indicates the base image to use. `python:3.4-onbuild` is an official repository from the [Docker Hub Registry](https://registry.hub.docker.com). The next line tells Docker to expose port 80, i.e. the port that Gunicorn runs the server on. The final line specifies the command to run when the container starts.

#### -onbuild

Several of the official Docker base images have a useful -onbuild variant. [The one we're using](https://github.com/docker-library/python/blob/e236058d5c3601af1d38ba27b4fe217c5d678c02/3.4/onbuild/Dockerfile), in addition to installing Python and Pip in the image, also copies our source code to the */usr/src/app/* directory and installs packages listed in *requirements.txt*. It's a minor convenience, but it saves boilerplate from our *Dockerfile*. There's -onbuild variants for [Node.js](https://registry.hub.docker.com/_/node/), [Ruby](https://registry.hub.docker.com/_/ruby/), and [Go](https://registry.hub.docker.com/_/golang/) also. Highly recommended.

## Fire It Up

All the pieces are in place. See the app in action by running the following commands then navigating to http://localhost:8080.

```bash
docker build --tag=mminer/myserver .
docker run -it --publish=8080:80 mminer/myserver
```

If all goes well your browser displays "Hello World!" as expected. Brilliant.

At this point we can push the image to the Docker Hub (or a private registry) and deploy it live. But we have a problem: when we update our code, our changes aren't reflected by the running server without another build + run. Fast though the build process is thanks to Docker's caching, re-running these steps becomes tedious quickly.

## Autoreload

Whenever we modify a file, the server should detect this and restart itself, showing our changes immediately. Flask's development server in debug mode (`app.run(debug=True)`) does this, as can many production-ready servers like Gunicorn.

So we need to tell Gunicorn whether we're in development or production mode. The easiest method is by using environment variables. Docker allows us to specify environment variables both in our *Dockerfile* or at runtime as arguments to `docker run`. The latter option is what we want.

```bash
docker run -it --publish=8080:80 --env="MODE=dev" mminer/myserver
```

Let's modify our *gunicorn.py* configuration file to turn on the reload option when the `MODE` environment variable equals "dev".

```python
# gunicorn.py
import os

if os.environ.get('MODE') == 'dev':
    reload = True

bind = '0.0.0.0:80'
```

Now whenever a file changes, Gunicorn relaunches its workers and loads our new code.

## Synced Folders

With one last piece of the puzzle we'll have everything working nicely. When we build the Docker image, our source files are copied to its */usr/src/app* directory. Luckily Docker offers a way to share directories between a host and a container. For those familiar with Vagrant, this is akin to their synced folders feature. If the directory that we're mapping already exists in the container, ours overwrites it. When we update a file the change is immediately reflected inside the container.

Specify the directory to share by providing `docker run` with a `--volume` argument.

```bash
docker run -it --publish=8080:80 --env="MODE=dev" --volume=/path/to/app:/usr/src/app:ro mminer/myserver
```

And voilà! Edit the source code on your host machine using your favourite editor and the server detects the changes and reloads itself. If you save syntax errors and the server shuts down when it can't decipher your typos, re-run the above command to be back in action lickety-split.

## Fig

Before we wrap up, allow me to tell you about [Fig](http://www.fig.sh) ([soon to be Docker Compose](https://github.com/docker/fig/issues/861)). The `docker run` command above is gnarly and worsens as your app grows in complexity. Specifying the command line arguments in a configuration file makes life more pleasant, which is what Fig allows you to do. Sure, you can chuck the command into a Bash script and call it a day, but as you start working with multiple containers (say, one for your app and another for a database), orchestration becomes painful. Fig makes this headache disappear.

Our configuration file looks like this.

```yaml
# fig.yml
myserver:
    build: .
    ports:
    - "8080:80"
    environment:
    - MODE=dev
    volumes:
    - .:/usr/src/app:ro
```

Instead of `docker run`, use `fig up` to start your container ([full list of commands here](http://www.fig.sh/cli.html)). Nice.

## Wrapping Up

There you have it, a lightweight, isolated, auto-reloading web server running inside a Docker container, the image of which can be deployed to production with no modification. Keep on rocking in the free world.

---

[^1]: Code snippets [on GitHub](https://github.com/mminer/blog-code/tree/master/docker-dev-environment-for-web-app). Thanks to [@ppawiggers](https://twitter.com/ppawiggers) for corrections.
