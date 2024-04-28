# ElementsSDK

ElementsSDK is a toolkit for integrating Elementscheat into your existing C++ client projects. It includes all necessary resources and a brief tutorial on how to use it. Integrate it at the start of your project and use it with the Elements Injector without compromising your Windows system!

## How to Use

Download and include all necessary files into your project. 
Follow the instructions in this link for setup guidance: [Basemod](https://github.com/ar-Kitty/EC-VRTools-Basemod/blob/main/ElementsCheatsBase/dllmain.cpp)

## Code Example

### dllmain.cpp:
Include the SDK and additional setup in your `dllmain.cpp` as shown below:

```cpp
// Include your own headers first
#include "path/to/sdk.h"

// Add this after including the SDK
HWND VRCWindow = nullptr;
static uintptr_t __cdecl I_beginthreadex(void* _Security, unsigned _StackSize, _beginthreadex_proc_type _StartAddress, void* _ArgList, unsigned _InitFlag, unsigned* _ThrdAddr) {
    return iat(_beginthreadex).get()(_Security, _StackSize, _StartAddress, _ArgList, _InitFlag, _ThrdAddr);
}

#define IATHookThread(mainThread, dllhandle) I_beginthreadex(0, 0, (_beginthreadex_proc_type)mainThread, dllhandle, 0, 0)
```


### Before DLL Entry Point:
Use the following code to ensure VRChat is running before injecting your modifications:

```cpp
void WaitForVRChat() {
    do
    {
        VRCWindow = FindWindowW(nullptr, L"VRChat");
        std::this_thread::sleep_for(std::chrono::milliseconds(1000));
    } while (!VRCWindow);

    // Place your initialization logic here (e.g., hooks, mods)
}
```

### DllMain or DLL Entry Point:
Configure the DLL entry point as follows:

```cpp
BOOL APIENTRY DllMain(HMODULE DllHandle, DWORD Reason, LPVOID lpReserved)
{
    if (Reason == DLL_PROCESS_ATTACH) { // Process Attach
        ElementsSDK elementsSDK; // Use the Elements SDK
        if (elementsSDK.RunMod(Reason)) {
            // Start hooks and other modifications
            DisableThreadLibraryCalls(DllHandle);

            // Launch a new thread for VRChat initialization
            IATHookThread(WaitForVRChat, DllHandle);
        } else {
            // Cleanup if initialization fails
            return TRUE;
        }
    }
    return TRUE;
}
```

This configuration will help ensure your project integrates smoothly with VRChat using the ElementsVRTools Injector.
