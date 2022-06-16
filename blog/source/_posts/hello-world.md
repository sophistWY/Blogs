---
title: vsCode 添加浏览器调试和js调试的方法总结

tags:
  - vsCode
  - 方法总结

categories:
  - HTML

---

vsCode 添加浏览器调试和js调试的方法总结

<!--more-->

### 1、直接按F5可以调试的方法或者点击运行按钮（可以直接运行html文件或者js文件）

在launch.json文件中的配置如下：

```Javascript
{

    "version": "0.2.0",
    "configurations": [{
            "name": "谷歌浏览器", //运行html文件，用谷歌浏览器打开
            "type": "chrome",
            "request": "launch",
            "url": "${file}",
            "sourceMaps": true,
            "webRoot": "${workspaceRoot}"
        },
        {
            "name": "nodeLauch", //单独调试js，即可以直接运行js
            "type": "node",
            "request": "launch",
            "program": "${file}", //这个配置成你要调试的文件、${file}当前打开的文件
            "stopOnEntry": false,
            "args": [],
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": null,
            "runtimeArgs": [
                "--nolazy"
            ],
            "env": {
                "NODE_ENV": "development"
            },
            "console": "internalConsole",
            "preLaunchTask": "",
            "sourceMaps": false,
            "outDir": null
        }
    ]
}
```



### 2、第二种方法 Ctrl+Shift+B 快捷键运行html文件，在Tasks.json中配置如下：

``` Javascript
{
    "version": "0.1.0",
    "command": "",
    "isShellCommand": false,
    "args": ["${file}"],
    "showOutput": "always",
    "windows": {
        "command": "C:/Users/shannonliang/AppData/Local/Google/Chrome/Application/chrome.exe"
    },
    "tasks": [{
        "taskName": "webserver",
        "isBuildCommand": true,
        "showOutput": "always"
    }]
}
```



### 3、如果第二种方法，不想每次都按这个快捷键，请参考npm配置node服务方法：

[http://stackoverflow.com/questions/30039512/how-to-view-my-html-code-in-browser-with-visual-studio-code ](http://stackoverflow.com/questions/30039512/how-to-view-my-html-code-in-browser-with-visual-studio-code )

引用: [https://www.cnblogs.com/sxz2008/p/6690384.html](https://www.cnblogs.com/sxz2008/p/6690384.html)

