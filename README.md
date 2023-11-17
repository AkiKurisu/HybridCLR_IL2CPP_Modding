# HybridCLR IL2CPP Plus Modding Support

Modify engine internal assemblies table deserialized from `ScriptingAssemblies.json` by replacing dummy assembly name, so you can load other assembly dynamically without need to add them in json before build. 

## Else you should do
Since support C# modding for IL2CPP is not HybridCLR designed for, you should do extra work to make it come true:
1. Manually add MethodBridge for different platform (you can copy it from `HybridCLRData\il2cpp_plus_repo\libil2cpp\hybridclr\generated`)
2. Modify HybridCLR unity package `Editor\BuildProcessors\PatchScriptingAssemblyList.cs` to add dummy assembly to `ScriptingAssemblies.json`