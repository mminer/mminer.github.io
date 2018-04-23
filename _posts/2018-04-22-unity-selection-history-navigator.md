---
title: "Unity Selection History Navigator"
---

Sometimes in Unity I want to quickly re-select a game object I previously had selected. You too right? So I built an editor extension that adds web browser-style *Back* and *Forward* menus to cycle through previous selections. Click a few game objects in the Hierarchy or assets in the Project pane then press <kbd>command</kbd> <kbd>[</kbd> (macOS) or <kbd>ctrl</kbd> <kbd>[</kbd> (Windows) to return to the previous selection.

<video autoplay height="412" loop src="/videos/selection-history-navigator.mp4" width="660"></video>

After navigating backward, you can also navigate forward using <kbd>command</kbd> <kbd>]</kbd> (macOS) or <kbd>ctrl</kbd> <kbd>]</kbd> (Windows). The more mouse-inclined can find the corresponding menu items under the *Edit > Selection* menu.

![Selection History Navigator menu](/images/selection-history-navigator.png)

Sidebar: if you're unfamiliar with the Load / Save Selection options also under *Edit > Selection*, you owe it to yourself to become acquainted with them. Damn useful. Hunting around for the deeply nested game object you had selected moments ago can be a thing of the past and if that's not worth celebrating then nothing is.

To get rolling, grab the *SelectionHistoryNavigator.cs* file [from here](https://github.com/mminer/selection-history-navigator) and drop it into your project. That's it. Go bananas.
