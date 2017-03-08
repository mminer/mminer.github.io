---
title: "Consolation: In-Game Console for Unity"
---

After creating a fresh Unity project, the first script I add is [Consolation](https://github.com/mminer/consolation), a helpful tool I built that displays an in-game debug console. It uses a handy but little-known feature of Unity called --- I hope you're sitting down for this --- [`Application.logMessageReceived`](https://docs.unity3d.com/ScriptReference/Application-logMessageReceived.html). Attach a delegate to this event, call `Debug.Log`, and do whatever you please with the incoming messages. In Consolation's case it collects them in a window for your enthusiastic perusal.

![Consolation](/images/consolation.png)

An in-game debug console is mostly useful when running your game outside the Unity editor, e.g. when testing a beta build. Something wonky happens? Press <kbd>`</kbd> or shake your mobile device to see the warnings and errors your game threw. The adventurous among us might use this juicy info to fix the problem. But let's not get too crazy.

Open Unity's Asset Store and you find a plethora of in-game debug consoles. I bet they're terrific. This one is free. Drop the [self-contained script](https://raw.githubusercontent.com/mminer/consolation/master/Console.cs) into your project, attach it to a game object, and you're ready to rumble. It's also open source and welcoming of pull requests, so fork away you magnificent creature.
