---
title: "Run Sourceless Plugins in a Custom Unreal Build"
---

If you compile Unreal Engine from source and open a project, it might complain that some plugin modules "are missing or built with a difference engine version," despite being built with the same semantic version.

![Unreal Engine missing modules dialog](/images/unreal-missing-modules.png)

If your plugin includes its source, rebuild and carry on friend. But if not --- if it's a binaries-only distribution --- you need to edit a file to make it work.[^1]

Unreal determines if a plugin is compatible with the engine in [`FModuleManager::IsModuleUpToDate`](https://docs.unrealengine.com/en-US/API/Runtime/Core/Modules/FModuleManager/IsModuleUpToDate/index.html). Step through the code and you see that it does so by comparing the plugin build ID with the engine's.[^2] Make these build IDs match and your project will open without complaint.

The engine build ID is stored in *Engine/Binaries/Win64/UE4Editor.modules*. This value is 13144385 in Epic's 4.25 build. In a version compiled from source it's a UUID, e.g. 5f645f3f-d8d1-4fbf-a1e6-866a7c8bb809. A plugin's build ID is likewise stored in *PLUGIN_NAME/Binaries/Win64/UE4Editor.modules*.[^3]

All you need to do is update the plugin's *UE4Editor.modules* file with the engine build ID. Easy peasy.

---

[^1]: This scenario --- building the engine yourself but lacking the source to plugins you use --- is rare. [Epic's marketplace guidelines](https://www.unrealengine.com/en-US/marketplace-guidelines#262a) require that plugins include their source to avoid this problem.

    > Customers should have the plugin source code that's dependent on Unreal Engine source code so that they may attempt to compile it against source-built versions of the engine.

[^2]: Here's [the line](https://github.com/EpicGames/UnrealEngine/blob/df84cb430f38ad08ad831f31267d8702b2fefc3e/Engine/Source/Runtime/Core/Private/Modules/ModuleManager.cpp#L1083) that compares the build IDs.

[^3]: Replace *Win64* with the platform you run Unreal on.

*[UUID]: Universally Unique Identifier
