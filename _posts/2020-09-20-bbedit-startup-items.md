---
title: "BBEdit Startup Items"
---

New discovery: BBEdit allows you to run commands on start by saving an AppleScript to *~/Library/Application Support/BBEdit/Startup Items*. Same deal with shutdown scripts in *Shutdown Items*. A niche feature, but I recently found it useful.

![BBEdit Startup Items menu and folder](/images/bbedit-startup-items.png)

I mainly use BBEdit for its multi-file find and replace.[^1] To save a few clicks, I want it to open the Multi-File Search window when I start the application. I first made a Keyboard Maestro macro, but a startup item is more elegant. Here’s the script:

```applescript
tell application "BBEdit"
    open the find multiple window
end tell
```

I wish more applications offered this feature, or better yet for macOS to support it in all applications. Tim Cook, you read my blog, right? You can make this happen?

---

[^1]: I seldom use BBEdit for its core function as a text editor, but its other tools like shell worksheets and pattern (regex) playgrounds are worth the price alone.

    Also great is BBEdit's support. Mere hours after I sent a feature request, Rich Siegel replied with this script and instructions on using startup items. Wonderful.
