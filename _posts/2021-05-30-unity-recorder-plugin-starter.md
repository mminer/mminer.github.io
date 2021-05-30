---
title: "Unity Recorder Plugin Starter"
---

Last week I wrote about [Big Image Recorder](/2021/05/24/big-image-recorder-for-unity), a plugin for [Unity Recorder](https://docs.unity3d.com/Manual/com.unity.recorder.html) that captures massive renders of your scene. Though Recorder's docs make no mention of it, creating a plugin is straightforward.[^1]

If you want to make your own, I knocked together a [starter project](https://github.com/mminer/recorder-plugin-starter) with a minimal implementation. It takes a camera and saves the frames as an image sequence. Recorder already outputs image sequences, and with more options, but you can use this bare-bones package as a jumping-off point to extend its functionality.

Go ham.

---

[^1]: It helps that you can view Recorder's source code. There's no public repository, but you can peruse the C# after adding the package to your project.
