---
title: "Scene View Bookmarks Available via Unity Package Manager"
---

[Years ago](/2018/01/25/unity-scene-view-bookmarks) I wrote a Unity editor extension that adds scene view bookmarks, allowing you to quickly return the scene camera to preset positions. Recently I revisited this script and converted it to a package. Now instead of importing it to your Assets folder, add it to your project with Unity's package manager ([installation instructions here](https://github.com/mminer/scene-view-bookmarks#installing)).

I also took the opportunity to make the *Set Bookmark* and *Move to Bookmark* options available via a scene overlay. Introduced in Unity 2021.2, [overlays](https://docs.unity3d.com/2021.2/Documentation/Manual/overlays.html) are a nifty way to add custom widgets to the Scene window. The dropdown shows the same options as the *Window > Scene View Bookmarks* menu, but now it's closer to the action.

<video autoplay height="366" loop src="/videos/scene-view-bookmarks-overlay.mp4" width="660"></video>
