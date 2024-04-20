## project kind

```lua
kind "ConsoleApp"
```

| Value       | Description         |
| ----------- | ------------------- |
| ConsoleApp  | 控制台应该程序      |
| WindowedApp | Windows桌面应该程序 |
| SharedLib   | 共享链接库或是DLL   |
| StaticLib   | 静态链接库          |

## 宏 Macro

| Macro                | 含义                |
| -------------------- | ------------------- |
| %{cfg.buildcfg}      | debug, release,etc. |
| %{cfg.system}        | windows, etc.       |
| %{cfg.arcchitecture} | x64, etc.           |
| %{prj.name}          | project name        |

## runtime lib

premake的runtime选项。可以选择`Debug`或者`Release`.

visual stdio的runtime lib(运行库)有四个选项:

`Muti-threaded`, `Muti-threaded Debug`, `Muti-threaded DLL`, `Muti-threaded Debug DLL`

link的static lib要和项目的runtime lib相同.

premake`runtime "Release"`,`runtime "Debug"`,`staticruntime "off"`, `staticruntime "on"`影响这些选项

如果编译时出现“未解析外部符号”的异常，那么可能是这里出了问题。

## 启动项目

```lua
workspace "ParticleTest"
	architecture "x64"
	startproject "ParticleTest"	-- 这里
	configurations
	{
		"Debug",
		"Release",
		"Dist"
	}
```

## systemversion

```lua
systemversion "10.0.22000.0" 	-- 写一个版本号
or
systemversion "latest" 			-- 最新版本
```



## 示例1

```lus
workspace "GluttonousSnake"	--解决方案名称

architecture "x64"

configurations
{
	"Debug",
	"Release",
	"Dist"
}

-- 输出路径  只是一个字符串变量 后面使用
outputdir = "%{cfg.buildcfg}-%{cfg.architecture}"

project "GluttonousSnake"
	location "GluttonousSnake"
	kind "ConsoleApp"
	language "C++"
	staticruntime "off"
	
	targetdir ("bin/"..outputdir.."/%{prj.name}" )
	objdir ("bin-int/"..outputdir.."/%{prj.name}")
	
	pchheader "pch.h"
	pchsource "%{prj.name}/src/pch.cpp"
	
	files
	{
		"%{prj.name}/src/**.h",
		"%{prj.name}/src/**.cpp"
	}
	
	includedirs
	{
		"Dependence/include",
		"ImGui/src"
	}
	
	defines
	{
		"_CRT_SECURE_NO_WARNINGS"
	}
	
	links
	{
		"glad",
		"opengl32.lib",
		"Dependence/glfw3.lib",
		"ImGui"
		
	}
	
	filter "system:windows"	--过滤器 对于特定的设定
		cppdialect "c++14"
		staticruntime "On"
		systemversion "10.0.22000.0" 
		
		--postbuildcommands	-- 后构建指令  dll使用
		--{
		--	("{COPY} %{cfg.buildtarget.relpath} ../bin/" ..outputdir.. "/App")
		--}
	
	filter "configurations:Debug"
		defines "_Debug"
		runtime "Release"
		symbols "On"
		
	filter "configurations:Release"
		defines "_RELEASE"
		runtime "Release"
		optimize "On"
		
	filter "configurations:Dist"
		defines "_DIST"
		runtime "Release"
		optimize "On"
		
project "ImGui"
	location "ImGui"
	kind "StaticLib"
	language "c++"
	staticruntime "off"
	
	targetdir ("bin/" ..outputdir.. "/%{prj.name}")
	objdir ("bin-int/" ..outputdir.. "/%{prj.name}")
	
	files
	{
		"%{prj.name}/src/**.h",
		"%{prj.name}/src/**.cpp"
	}
	
	includedirs
	{
		"Dependence/include"
	}
	
	filter "system:windows"	--过滤器 对于特定的设定
		cppdialect "c++14"
		staticruntime "On"
		systemversion "10.0.22000.0" 
	
	filter "configurations:Debug"
		defines "_Debug"
		runtime "Release"
		symbols "On"
		
	filter "configurations:Release"
		defines "_RELEASE"
		runtime "Release"
		optimize "On"
		
	filter "configurations:Dist"
		defines "_DIST"
		runtime "Release"
		optimize "On"
		
project "glad"
	location "glad"
	kind "StaticLib"
	language "c++"
	staticruntime "off"
	
	targetdir ("bin/" ..outputdir.. "/%{prj.name}")
	objdir ("bin-int/" ..outputdir.. "/%{prj.name}")
	
	files
	{
		"%{prj.name}/src/**.h",
		"%{prj.name}/src/**.c"
	}
	
	includedirs
	{
		"Dependence/include"
	}
	
	filter "configurations:Debug"
		defines "_Debug"
		runtime "Release"
		symbols "On"
		
	filter "configurations:Release"
		defines "_RELEASE"
		runtime "Release"
		optimize "On"
		
	filter "configurations:Dist"
		defines "_DIST"
		runtime "Release"
		optimize "On"
```

