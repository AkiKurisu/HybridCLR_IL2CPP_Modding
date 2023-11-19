# HybridCLR IL2CPP Plus Modding Support

Modify engine internal assemblies table deserialized from `ScriptingAssemblies.json` by replacing dummy assembly name, so you can load other assembly dynamically without need to add them in json before build. 

## How to use
Since support C# modding for IL2CPP is not HybridCLR designed for, you should do some work to make it come true:
1. Replace `Editor\BuildProcessors\PatchScriptingAssemblyList.cs` of HybridCLR unity package to pre-add mod assemblies place holder
2. Hardcode external assemblies info path in `il2cpp-api.cpp` (eg. `{PersistentDataPath}/external.txt`)
3. Write your mod assemblies name to info file (eg. ``Kurisu.Mod.Test``, do not add suffix which should be ``.dll.bytes``)

## Support platform
Currently it can support modding for Window and Android.

## Else you should do

1. Do aot pre-process in your game source code for preventing generic stripping problem in mod assemblies (also providing mod interface access to game api is better than let modder use reflection since it may cause problem above)
2. Mod assembly name should smaller than placeholder name (currently maximum is 36, can be changed in `PatchScriptingAssemblyList.cs`)
3. Assembly not contained Monobehaviour, ScriptableObject and ManagedReference do not need to be added to ``external.txt``.
4. If new mod is added or removed, ``external.txt`` should be modified before game loading. You can make a re-launch dialog box after user loads mod for first time.

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
    //Since engine send {GamePath}/il2cpp_data path to il2cpp, you can use it directly in Windows.
    const char *filePath = hybridclr::ConcatNewString(data_path, "/external.txt");
    std::ifstream file(filePath);
    loadExternalFromStream(file);
}
```

## Safe Modding Method for MonoBehaviour
See https://github.com/AkiKurisu/MonoReference