---
title: "Web Page Click Prevention"
---

I'm working on a project where momentarily preventing the user from following links is desirable. There are form controls on the page that trigger AJAX requests, after which a notification appears displaying the response from the server. The contents of the page change after every call; buttons that were previously disabled may become enabled, and vice versa. Though this happens quickly, there's a short amount of time during which the user can click a button and trigger another call to the server before the last one has returned and modified the page accordingly.

One way to prevent eager users from clicking too fast is to set a JavaScript variable to false when the AJAX request is being made, and only allow new calls to be executed if that variable is true. The problem with doing this is that the user still sees visual confirmation of their click and expects something to happen as a result. Another solution is to disable all the buttons on the page while the request is being processed. The issue here is that the user sees a flash of disabled buttons, which isn't exactly aesthetically pleasing.

Like all things worthy of a pay raise, this conundrum's solution is a simple one. A property of HTML divs is that they capture mouse clicks, even when transparent. By overlaying an absolutely positioned div above the form controls, they become inaccessible. The user can try to click them, but they will not depress and generate a server request. By default, the div's CSS is set to `display: none`. When the request is made, the JavaScript switches it to `display: block`. Only if the user tries to click a button while a request is being processed will they be aware that something is blocking them from doing so.

The code looks something like this:

```javascript
$('button').click(function() {
    $('#overlay').show();

    $.getJSON('/doSomething.php', function(data) {
        // Modify page
        ...
        $('#overlay').hide();
    });
});
```

And the HTML:

```html
<style type="text/css">
    #wrapper { position: relative; }

    #overlay {
        height: 100%;
        left: 0;
        position: absolute;
        top: 0;
        width: 100%;
        z-index: 100
    }
</style>

<div id="wrapper">
    <div id="overlay"></div>

    <button type="button">Click Here</button>
    ...
</div>
```

A simple solution to an obscure problem. Pass the A1.
