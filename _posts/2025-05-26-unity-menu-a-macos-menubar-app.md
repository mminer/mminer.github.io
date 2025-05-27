---
title: "Unity Menu: A macOS Menubar App"
---

*TL;DR: I built a macOS menubar app to quickly open running Unity projects. [Download here](https://github.com/mminer/unity-menu/releases).*

---

In my day job I juggle multiple Unity projects, regularly flipping between 3–5 of them. Neither the macOS Dock nor the app switcher make it clear which project I'm switching to.

![Unity Dock icons](/images/unity-dock-icons.png)

Will I choose the correct one? It's a fun little game.

Unity Hub isn't better. It can launch a project, but try to open one that's running and I get a "Project is already open" message.

![Unity Hub "Project is already open"](/images/unity-hub-project-already-open.png)

To take this guesswork out of switching between Unity instances, I put together a macOS app that lists open projects in the menubar. Click a project to bring it to the front.

![Unity Menu](/images/unity-menu.png)

With the help of a global shortcut that opens the menu, activating the Unity process I want is 42% faster. Scientifically measured, peer review unnecessary.

![Unity Menu global keyboard shortcut](/images/unity-menu-global-keyboard-shortcut.png)

I also built a `unity-menu` command line tool that does the same thing as the menubar app.

```
$ unity-menu
[1] Grand Theft Auto 6
[2] Huedini
[3] Orbert
[4] Time Squatch
Selection: 3
```

I rarely employ the CLI version, but I have a notion to write [Launchbar](https://www.obdev.at/products/launchbar/index.html) and [Alfred](https://www.alfredapp.com) actions using it. Work in progress.

I developed this to scratch my own itch, but if it's an itch you also wish to scratch, find the source code and app download in [this GitHub repo](https://github.com/mminer/unity-menu).

*[CLI]: Command Line Interface
*[TL;DR]: Too Long; Didn't Read
