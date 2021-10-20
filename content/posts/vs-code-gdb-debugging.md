---
title: "Visual Debugging on VS Code Using GDB"
date: 2021-10-19T20:13:45-07:00
draft: false
tags: ["vs code", "gdb", "c/c++", "development"]
---
If you're already developing a C/C++ project in VS Code, configuring the built-in debugger will allow you to fully use features already within the IDE with only a single configuration file. To those are used to debugging in IDEs like IntelliJ or Visual Studio, this is a much more familiar and pleasant experience. Additionally, this can be done both locally *and* over SSH via the [Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) extension. 

For Computer Science & Engineering courses at the UW, I typically open the folder containing my entire repo in VS Code (i.e. `~/code/cse3xx-21au-username`), which contains all of the course's projects. This makes configuring debugging easier since you don't have to copy this JSON file into every folder if you typically just open a project's folder directly (i.e. `~/code/cse3xx-21au-username/hw1` ), as the configuration is expected to be found in a folder at the highest level with the current workspace.

## Configuring Visual Debugging
To open the default configuration file, click the **Run and Debug** icon (or Ctrl+Shift+d), then click the **cog** icon at the top of the sidebar that opens. A file titled `launch.json` should be created and opened.

Here's an example debugging configuration for an executable called `test_suite` in a CentOS 8 based environment (as used in the course [CSE 333](https://courses.cs.washington.edu/courses/cse333/)):

### Simple launch.json Example
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Run & Debug test_suite",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}/test_suite",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "",
            "miDebuggerPath": "/opt/rh/gcc-toolset-9/root/usr/bin/gdb"
        }
		]
}
```

Save this file, and if everything is configured correctly, you should be ready to debug. 

Debugging is now as easy as clicking the **Run and Debug** icon (or Ctrl+Shift+d), selecting the configuration named `Run & Debug test_suite` in the dropdown box that appears at the top of the sidebar, then pressing the **Play/Run** button (or F5).

**Important note:** this setup requires that you have a file open in the correct hw folder before running the debugger. This configuration depends on the path of that open file to find the path of the *test_suite* executable. For example, if you want to debug the executable `test_suite` in a folder named `hw1`, you would simply have open a file within the directory containing that executable, such as `~/cse3xx-21au-username/hw1/LinkedList.c`, before starting a debugging session.

Creating breakpoints and adding a variable to the watchlist is similar other IDEs (see more: [https://code.visualstudio.com/Docs/editor/debugging](https://code.visualstudio.com/Docs/editor/debugging)).

You can add more configs to target different executables, and edit the *args* field to append arguments just like you would on the command line. Here's another `launch.json` configuration that runs only a specific set of test cases from the `test_suite`, and adds another configuration to debug an executable named `server`:

### Example launch.json with Multiple Configurations
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Run & Debug test_suite",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}/test_suite",
            "args": [
                "--gtest_filter=Test_SomeTestCase.*"

            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "",
            "miDebuggerPath": "/opt/rh/gcc-toolset-9/root/usr/bin/gdb"
        },
        {
            "name": "Run & Debug server",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}/server",
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "",
            "miDebuggerPath": "/opt/rh/gcc-toolset-9/root/usr/bin/gdb"
        }
    ]
}
```

## Additional Resources

Overview on Microsoft's website: [https://code.visualstudio.com/docs/cpp/config-linux#_debug-helloworldcpp](https://code.visualstudio.com/docs/cpp/config-linux#_debug-helloworldcpp)