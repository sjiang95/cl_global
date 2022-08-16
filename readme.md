# Use `cl` as global compiler

This gist records the process to configure global development environment base on `cl.exe` compiler provided by `Build Tools for Visual Studio`.

## Motivation

`Visual Studio` is a powerful but cumbersome IDE, for which it is lack of attraction personally. One solution is to use only the `Build Tools for Visual Studio` which provide complete development experience (compiler, build system, libs, head files, etc.) on Windows independently.

## Tutorial

Please follow the instructions below to configure the development environment properly.

### Install `Build Tools for Visual Studio`

Download and launch [Visual Studio Tools - Install Free for Windows, Mac, Linux](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2022). Select and install the following two components.
![installer components](./imgs/vsInstaller.png)

### Set environment variables

Although not recommend by the official document [Use the Microsoft C++ toolset from the command line | Microsoft Docs](https://docs.microsoft.com/en-us/cpp/build/building-on-the-command-line?view=msvc-160#path_and_environment),
>We don't recommend you set these variables in the Windows environment yourself.

we have to set a batch of environment variables manually in order to use compiler, build system, libs, head files, etc. globally. In the same section of the official document, it says
>The MSVC command-line tools use the `PATH`, `TMP`, `INCLUDE`, `LIB`, and `LIBPATH` environment variables, and also use other environment variables specific to your installed tools, platforms, and SDKs.

In practice, although I ignored the setting of env variable `TMP`, everything works safe and sound so far. Find and start `x64 Native Tools Command Prompt for VS 2022` (or any other command prompt you want to use) in your start menu. Run

```shell
set
```

This command prints all environment variables would be used by the build tools. Find the entry of `INCLUDE`, `LIB`, and `LIBPATH`. And set them to system environment variable manually. For example, I got the following `INCLUDE` entry

```shell
INCLUDE=d:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.33.31629\include;d:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Auxiliary\VS\include;C:\Program Files (x86)\Windows Kits\10\include\10.0.22621.0\ucrt;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\um;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\shared;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\winrt;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\cppwinrt;C:\Program Files (x86)\Windows Kits\NETFXSDK\4.8\include\um;d:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.33.31629\include;d:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Auxiliary\VS\include;C:\Program Files (x86)\Windows Kits\10\include\10.0.22621.0\ucrt;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\um;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\shared;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\winrt;C:\Program Files (x86)\Windows Kits\10\\include\10.0.22621.0\\cppwinrt;C:\Program Files (x86)\Windows Kits\NETFXSDK\4.8\include\um
```

Copy and paste variable name & value relatively to create an environment variable.

![INCLUDE](imgs/INCLUDE.png)

Repeat the process to create env variables for `LIB` and `LIBPATH`. As for `PATH`, command `set` prints complete but tedious directories, which is unnecessary for me. So I add directories of `MSBuild.exe`, `cl.exe` and `rc.exe` to system `Path` manually. In my case, they are

```shell
D:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin # MSBuild.exe
D:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC\Tools\MSVC\14.33.31629\bin\Hostx64\x64 # cl.exe
D:\Program Files (x86)\Microsoft Visual Studio\Shared\NuGetPackages\microsoft.windows.sdk.buildtools\10.0.22621.1\bin\10.0.22621.0\x64 # rc.exe
```

Save the changes and open a new terminal (powershell, cmd, etc.).

```shell
$ MSBuild.exe  --version
MSBuild version 17.3.0+f67e3d35e for .NET Framework
17.3.0.37102

$ cl.exe
用于 x64 的 Microsoft (R) C/C++ 优化编译器 19.33.31629 版
版权所有(C) Microsoft Corporation。保留所有权利。

用法: cl [ 选项... ] 文件名... [ /link 链接选项... ]

$ rc.exe /?

Microsoft (R) Windows (R) Resource Compiler Version 10.0.10011.16384
Copyright (C) Microsoft Corporation.  All rights reserved.

Usage:  rc [options] .RC input file
<rest outputs>
```

You are now all set to call these tools world-wide.

### Test

```shell
mkdir helloworld
cd helloworld
```

Create `helloworld.cpp` with the following code

```c++
#include <iostream>

void main() {
  std::cout << "Hello World!" << std::endl;
  return 0;
}
```

and corresponding `CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.20)

project(hello)
set(CMAKE_CXX_STANDARD 20) 

add_executable(hello helloworld.cpp)

target_link_libraries(hello)
```
