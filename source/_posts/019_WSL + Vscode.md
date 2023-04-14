---
title: WSL+Vscode
description: 安装WSL+Vscode
date: 2023-4-2
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/3_9_6_4_image-20230403090559374.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/3_9_6_4_image-20230403090559374.png
categories: 
- WSL
tag: 
- Ubuntu
- c++
- vscode

---

# WSL是什么？

WSL 2 是适用于 Linux 的 Windows 子系统体系结构的一个新版本，它支持适用于 Linux 的 Windows 子系统在 Windows 上运行 ELF64 Linux 二进制文件。 它的主要目标是**提高文件系统性能**，以及添加**完全的系统调用兼容性**。

> https://learn.microsoft.com/zh-cn/windows/wsl/about

#  安装WSL+Vscode

1. 先安装WSL
   - [使用 WSL 在 Windows 上安装 Linux](https://learn.microsoft.com/zh-cn/windows/wsl/install)

2. 安装vscode

   - 访问 [VS Code 安装页](https://code.visualstudio.com/download)，选择 32 位或 64 位安装程序。 在 Windows 上（不是在 WSL 文件系统中）安装 Visual Studio Code。

3. 安装拓展

   - [入门](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/wsl-vscode)
   - [c++插件](https://xljzt.top/2023/03/16/018_vscode%20c++%E9%85%8D%E7%BD%AE/)

4. 安装c++

   > sudo apt update
   > sudo apt install g++ gdb make ninja-build rsync zip

5. 现在我们已经能够找到自己的wsl，并连接上了

![image-20230403090559374](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/3_9_6_4_image-20230403090559374.png)



#  配置

配置.vscode 文件

**1. c_cpp_properties.json**

按快捷键Ctrl+Shift+P调出命令面板，输入C/C++，选择“Edit Configurations(UI)”进入配置。

```c++
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c17",
            "cppStandard": "gnu++14",
            "intelliSenseMode": "linux-gcc-x64"
        }
    ],
    "version": 4
}
```

**2. launch.json**

直接创建文件配置

```c++
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "C/C++",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}/${fileBasenameNoExtension}",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "preLaunchTask": "compile",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```

**3. tasks.json**

直接创建文件配置

```c++
{
    "version": "2.0.0",
    "tasks": [{
            "label": "compile",
            "command": "g++",
            "args": [
                "-g",
                "${file}",
                "-o",
                "${fileDirname}/${fileBasenameNoExtension}"
            ],
            "problemMatcher": {
                "owner": "cpp",
                "fileLocation": [
                    "relative",
                    "${workspaceRoot}"
                ],
                "pattern": {
                    "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",
                    "file": 1,
                    "line": 2,
                    "column": 3,
                    "severity": 4,
                    "message": 5
                }
            },
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}

```

#  最终效果

![image-20230403090941680](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/3_9_9_45_image-20230403090941680.png)

F5运行调试之后 

![image-20230403091006016](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/3_9_10_7_image-20230403091006016.png)

#  END

建议多看wsl官方教程，真的好用！
