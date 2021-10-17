---
title: "Unity Immediate Window"
---

Ages ago, when Unity 3 was still in beta, I [wrote about](/2010/11/14/unity-editor-macros) using `UnityEditor.Macros` to run ~~JavaScript~~ UnityScript snippets in the editor. Today you have a better option in the form of [Immediate Window](https://docs.unity3d.com/Packages/com.unity.immediate-window@0.0/manual/index.html).

Immediate Window provides a more fully fledged REPL --- you can inspect returned objects, import assemblies on the fly, and reference variables you previously defined. It's a useful tool for quick debugging or to experiment with an API. Maybe some editor automation? Oh baby oh baby.

![Unity Immediate Window](/images/immediate-window.png)

Under the hood it depends on Unity's [Code Analysis package](https://docs.unity3d.com/Packages/com.unity.code-analysis@0.1/manual/index.html), which includes Microsoft.CodeAnalysis DLLs. In particular, `Microsoft.CodeAnalysis.CSharp.Scripting.CSharpScript.EvaluateAsync` does the heavy lifting of evaluating arbitrary C# code.

**Caveat:** At present the Immediate Window package includes an unneeded `using` statement that causes a compilation error in Unity 2021.1. I created [a fork](https://github.com/mminer/com.unity.immediate-window) of the repository that removes the offending line. To use this package instead, add this URL in Unity's package manager: <https://github.com/mminer/com.unity.immediate-window.git>

*[REPL]: Read-Eval-Print Loop
