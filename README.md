# HybridCLR IL2CPP Plus Modding Support

Modify engine internal assemblies table deserialized from `ScriptingAssemblies.json` by replacing dummy assembly name, so you can load other assembly dynamically without need to add them in json before build. 

## What you should do
Since support C# modding for IL2CPP is not HybridCLR designed for, you should do some work to make it come true:
1. Manually add MethodBridge for different platform (you can copy it from `HybridCLRData\il2cpp_plus_repo\libil2cpp\hybridclr\generated`)
2. Replace `Editor\BuildProcessors\PatchScriptingAssemblyList.cs` of HybridCLR unity package to pre-add dummy assemblies
3. Hardcode external assembly info load path in `il2cpp-api.cpp`, especially for android (since engine side only sends a java vm pointer to il2cpp, i don't know how to get application persistent data path from it, if you know how to get it, please tell me)

```c++
#if IL2CPP_TARGET_ANDROID
void loadExternalAndroid()
{
    //It's your game's persistent data path in android: com.{CompanyName}.{ProjectName}.
    std::ifstream file("/storage/emulated/0/Android/data/com.AkiKurisu.IL2CPP_Mod/files/external.txt");
    loadExternalFromStream(file);
}
#endif // IL2CPP_TARGET_ANDROID

void loadExternalWin(const char *data_path)
{
    //Since engine send il2cpp_data path to il2cpp, you can easily get game path, streamingDataPath.
    const char *filePath = hybridclr::ConcatNewString(data_path, "/external.txt");
    std::ifstream file(filePath);
    loadExternalFromStream(file);
}
```