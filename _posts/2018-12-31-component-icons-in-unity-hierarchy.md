---
title: "Component Icons in Unity Hierarchy"
---

Some time ago I built a small Unity editor extension that shows at a glance which components are attached to a game object via icons in the Hierarchy window. Lights display a light bulb icon, cameras a camera icon, and so forth.

![Unity Hierarchy window with icons](https://matthewminer.com/images/hierarchy-icons.png)

I promptly abandoned the project. Recently someone asked that I make it compatible with Unity 2018.3 --- it referenced long-obsolete components --- so I dusted off the repo and updated it to work with the latest and greatest. Compiler errors begone.

If this is something you might find useful, [download it from GitHub](https://github.com/mminer/hierarchy-icons) and add the scripts to an *Editor* folder. Then blow a kiss to your coworkers.[^1]

---

[^1]: Optional step.
