# Suggestions for VSCode

## Plugins list
- One Dark Pro
- Python
- C/C++
- vscode-clangd
- LeetCode
- Brackets Pair Colorizer 2
- Markdown Preview Enhanced
- Path IntelliSense
- TabNine


## VSCode Settings JSON
```json
{
    "workbench.settings.useSplitJSON": true,

    "editor.fontSize": 15,
    "editor.fontFamily": "Monaco",
    "terminal.integrated.fontSize": 14,
    "workbench.colorTheme": "One Dark Pro",
}
```


## C++ Settings

### c_cpp_properties.json
```json
// c_cpp_properties.json
{
    "configurations": [
        {
            // Maybe need some changes here for include other code files.
            "name": "macOS",
            "includePath": [
                "${workspaceFolder}/**" 
            ], 
            "defines": [],
            "macFrameworkPath": [
                "/System/Library/Frameworks",
                "/Library/Frameworks"
            ],
            "compilerPath": "/usr/bin/clang",
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "clang-x64"
        }
    ],
    "version": 4
}
```

### tasks.json
```json
// tasks.json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Compile",
            "type": "process",      // process是vsc把预定义变量和转义解析后直接全部传给command；
                                    //shell相当于先打开shell再输入命令，所以args还会经过shell再解析一遍
            "command": "clang++",   // 要使用的编译器，C++用clang++
            "args": [       // 编译命令参数
                "-std=c++17",
                "-stdlib=libc++",
                "${file}",
                "-o",       // 指定输出文件名，不加该参数则默认输出a.exe，Linux下默认a.out
                "${workspaceFolder}/.build/${fileBasenameNoExtension}.out",
                "-g",       // 生成和调试有关的信息
                "-Wall",    // 开启额外警告
                // "-static-libgcc", // 静态链接libgcc，一般都会加上
            ],
            "group": {
                "kind": "build",
                "isDefault": true   // 不为true时ctrl shift B就要手动选择了
            },
            "presentation": {
                "echo": true,
                "reveal": "always", // 执行任务时是否跳转到终端面板，可以为always，silent，never。具体参见VSC的文档
                "focus": false,     // 设为true后可以使执行task时焦点聚集在终端，但对编译C/C++来说，设为true没有意义
                "panel": "shared"   // 不同的文件的编译信息共享一个终端面板
            },
            // "problemMatcher":"$gcc" // 此选项可以捕捉编译时终端里的报错信息；本文用的是clang，开了可能会出现双重报错信息；只用cpptools可以考虑启用
        }
    ]
}
```

### launch.json
```json
// launch.json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(lldb) Launcher",
            "type": "cppdbg",
            "request": "launch",  // 请求配置类型，可以为launch（启动）或attach（附加）
            // 将要调试的程序的路径
            "program": "${workspaceFolder}/.build/${fileBasenameNoExtension}.out",
            "args": [], // 程序调试时传递给程序的命令行参数，一般设为空即可
            "stopAtEntry": true, // 设为true时程序将暂停在程序入口处，相当于在main上打断点
            "cwd": "${workspaceFolder}", // 调试程序时的工作目录，此为工作区文件夹；改成${fileDirname}可变为文件所在目录
            "environment": [],
            "externalConsole": false,
            "MIMode": "lldb", // 指定连接的调试器，可以为gdb或lldb
            "miDebuggerPath": "/usr/bin/lldb", // 调试器路径，Windows下后缀不能省略，Linux下则不要
            "setupCommands": [
                { // 模板自带，好像可以更好地显示STL容器的内容，具体作用自行Google
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": false
                }
            ],
            "preLaunchTask": "Compile" // 调试会话开始前执行的任务，一般为编译程序。与tasks.json的label相对应
        }
    ]
}
```
