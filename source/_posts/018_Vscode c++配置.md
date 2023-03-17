---
title: Vscode c++配置
description: Vscode c++配置
date: 2023-3-16
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_11_19_7_image-20230317111902760.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_11_19_7_image-20230317111902760.png
categories: 
- c++
tag: 
- c++
- vscode
---



#  Vscode c++配置

详细记录安装 vscode 和调试 c++ 配置，以及中文控制台乱码的问题

#  1. 下载安装vscode

> 这个应该不用多讲，大家自行下载安装
>
> 有些时候好像下载下来挺慢的，可以使用一个下载器进行下载

**插件安装**

![image-20230317091446806](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_9_15_46_image-20230317091446806.png)





**我用的主题：**

![image-20230317091520571](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_9_15_36_image-20230317091520571.png)

插件现在是可以同步的，所以大家可以登录微软账号或者GitHub账号进行同步。

#  2.安装MinGw

大家可以从[sourceforge的mingw项目](https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/)上下载64位的编译器，直接打开进行安装，下图的笔者所选的选项。其中版本选最新版本，对语言的新特性有较好的支持；构架是32位和64位的选择，32位请选择x86；线程部分选择win32，如果是Linux请选择posix；异常模型部分选择默认的seh就好；最后一项只能选0。选好之后点击下一步

![image-20230317091701352](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_9_17_7_image-20230317091701352.png)

然后将其安装到路径中不包含**空格**和**中文**字符。

# 3.配置环境变量

环境变量 》系统变量 》Path 》添加自己mingw下的bin路径

![image-20230317092012214](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_11_23_32_image-20230317092012214.png)

最后，打开cmd，输入*gcc -v*验证是否成功即可。

# 4.配置编译器

接下来配置编译器路径，按快捷键Ctrl+Shift+P调出命令面板，输入C/C++，选择“Edit Configurations(UI)”进入配置。

- 配置集  win32
- 编译路径 选择为 {mingw路径}/ g++.exe
- 编译器参数  gcc-64(legacy)

![image-20230317092237934](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_9_22_40_image-20230317092237934.png)

配置完成之后，侧边栏会多一个`.vscode`的文件夹，并且里面有一个`c_cpp_properties.json`文件，文件内容如下

```json
{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            "compilerPath": "D:/Program Files/mingw64/bin/g++.exe",
            "cStandard": "c17",
            "cppStandard": "gnu++17",
            "intelliSenseMode": "gcc-x64"
        }
    ],
    "version": 4
}
```

# 5.配置构建任务

接下来，创建一个tasks.json文件来告诉VS Code如何构建（编译）程序。该任务将调用g++编译器基于源代码创建可执行文件。 按快捷键Ctrl+Shift+P调出命令面板，输入tasks，选择“Tasks:Configure Default Build Task”：

![image-20230317092852884](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_11_18_30_image-20230317092852884.png)

再选择“C/C++: g++.exe build active file”：

> 因为我装了中文插件 所以为中文。
>
> 这里有个坑，如果生成的`tasks.json`里的tasks > label 的值与 launch.json > preLaunchTask 的值不一样，会报错不能调试。 但不影响编译

![image-20230317092931586](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_11_18_28_image-20230317092931586.png)

此时会出现一个名为tasks.json的配置文件，内容如下：

> 在里面我添加了两行注释，是处理控制台输出中文编码的问题

```json
{
	"version": "2.0.0",
	"tasks": [
		{
			"type": "cppbuild",
			"label": "g++.exe build active file",
			"command": "D:/Program Files/mingw64/bin/g++.exe",
			"args": [
				"-fdiagnostics-color=always",
				"-g",
				"-fexec-charset=GBK",   // 处理mingw中文编码问题
            	"-finput-charset=UTF-8",// 处理mingw中文编码问题
				"${file}",
				"-o",
				"${fileDirname}\\${fileBasenameNoExtension}.exe"
			],
			"options": {
				"cwd": "D:/Program Files/mingw64/bin"
			},
			"problemMatcher": [
				"$gcc"
			],
			"group": {
				"kind": "build",
				"isDefault": true
			},
			"detail": "编译器: \"D:/Program Files/mingw64/bin/g++.exe\""
		}
	]
}
```

# 6.配置调试设置

点击菜单栏的*Run*-->*Start Debugging*：

选择C++(GDB/LLDB)

紧接着会产生一个launch.json的文件：

![image-20230317111902760](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/17_11_19_7_image-20230317111902760.png)

> 这是我已经添加过的配置

读者可以手动点击Add Configuration进行添加配置，新手建议直接复制

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [

        {
        "name": "(gdb) Launch",
            "preLaunchTask": "g++.exe build active file",//调试前执行的任务，就是之前配置的tasks.json中的label字段
            "type": "cppdbg",//配置类型，只能为cppdbg
            "request": "launch",//请求配置类型，可以为launch（启动）或attach（附加）
            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",//调试程序的路径名称
            "args": [],//调试传递参数
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,//true显示外置的控制台窗口，false显示内置终端
            "MIMode": "gdb",
            "miDebuggerPath": "D:\\Program Files\\mingw64\\bin\\gdb.exe",
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

# 7.完结撒花

现在已经可以通过F5进行编译运行了

如果只是编译的话，快捷键为 `ctrl+Shift+B`,会生成一个exe文件

#  参考文章

[VSCode配置C/C++环境](https://zhuanlan.zhihu.com/p/87864677)

[mingw控制台中文乱码](https://www.cnblogs.com/chouxianyu/p/11249810.html)
