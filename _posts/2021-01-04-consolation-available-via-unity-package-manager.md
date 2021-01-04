---
title: "Consolation Available via Unity Package Manager"
---

I updated [Consolation](https://github.com/mminer/consolation), a Unity component that displays debug logs directly in your game, to support installation via the [Unity Package Manager](https://docs.unity3d.com/Manual/upm-ui.html). Consolation was never difficult to install --- simply copy the self-contained *Console.cs* to your project --- but a UPM package makes it easy to update to the latest version without resorting to Git submodules.

To add Consolation to your project, click the **+** menu in the Package Manager window, choose "Add package from git URL...", then enter `https://github.com/mminer/consolation.git`. Alternatively, clone the repository and point UPM to your local copy with "Add package from disk..." Afterward the `Console` component will be available to attach to your game objects. What fun.

<video autoplay height="343" loop src="/videos/consolation-upm.mp4" width="660"></video>

*[UPM]: Unity Package Manager
