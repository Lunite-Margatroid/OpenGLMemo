# LoadLibrary & dll

```c++
// windows
#include <windows.h>
#include <stdio.h>

int main(){
    typedef int(*FuncIntAdd)(int, int);
    
    HMODULE hDll0 = LoadLibiray("Math.dll");
    HMODULE hDll1 = LoadLibiray("Math.dll");
    
    FuncIntAdd funcAdd0 = GetProcAddress(hDll0, "int_add");
    FreeLibrary(hDll0);
    
    FuncIntAdd funcAdd1 = GetProcAddress(hDll1, "int_add");
    
    // funcAdd0 == funcAdd1
    // 两者的地址是一样的
    printf("Func Address0 = %llX\n", funcAdd0);
    printf("Func Address1 = %llX\n", funcAdd1);
    
    FreeLibrary(hDll1);
    
}
```

