---
title: "VS Code的golang开发配置 之 代码提示"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["from-csdn"]
comment: true
mathjax: false
---

之前用VS Code的时候，发现自己的代码的提示一直不好，换用JetBrain的Goland的代码提示是好了，但是比较占用资源。在网上找了一些资料，发现很多人也是遇到第三方或者自己的代码无法提示的情况，但是都没有下文了。后来发现官方文档有介绍，链接如下 

https://github.com/Microsoft/vscode-go/wiki/GOPATH-in-the-VS-Code-Go-extension


总的来说就是需要对设置配置一下。
现在分享一下我的配置

配置到workspace Setting里面。

```
{
    "window.zoomLevel": 1,
    "go.autocompleteUnimportedPackages": true,
    "go.gocodePackageLookupMode": "go",
    "go.gotoSymbol.includeImports": true,
    "go.useCodeSnippetsOnFunctionSuggest": true,
    "go.inferGopath": true,
    "go.gopath":"/usr/local/bin/golang19/gopath/",
    "go.useCodeSnippetsOnFunctionSuggestWithoutType": true,
}
```

其中重要的选项是 
1. **"go.inferGopath"** 要设置为 **true** .
2. **"go.gopath"** 的路径要设置为 你自己的gopath的路径。 如果有多个要用 “;”(windows)或者":"(其他)隔开。

之后就可以愉快的写代码了。


动画演示如下:
![](http://images2017.cnblogs.com/blog/551396/201801/551396-20180113211401988-58417149.gif)