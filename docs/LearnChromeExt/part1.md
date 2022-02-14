
### Chrome 扩展程序
[参考链接-掘金](https://www.zhihu.com/question/20179805)

一个 Chrome 扩展其实就是一个配置（入口）文件 mainifesr.json 和一系列的 html，css, js、图片文件的集合。

一manifest文件的编写
1. 基本属性：
扩展的名称（name）
版本（version）
描述（description）
图标位置（icons）
manifest版本（manifest_version）等信息

```json
{
    ...
    "manifest_version": 2,
    "name": "Weather",
    "description": "a currently clone",
    "version": "0.1",
    ...
}
```

2. browser_action
browser_action 指定扩展的图标放在 Chrome 工具栏中，它定义了扩展图标文件位置（default_icon）、悬浮提示（default_title）和点击扩展图标所显示的页面位置（default_popup）

```json
{
    ...
    "browser_action": {
        "default_icon": {
            "19": "images/icon19.png",
            "38": "images/icon38.png"
        },
        "default_title": "stock helper",
        "default_popup": "popup.html"
    },
    ...
}
```