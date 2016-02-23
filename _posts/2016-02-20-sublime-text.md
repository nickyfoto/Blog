---
layout: post
title:  "Sublime Text"
date:   2016-02-20
author: "Huang Qiang"
tags: [Sublime Text, editor]
---

Edictor is coder's best friend。今天我们来看下为什么Sublime Text为什么这么出众。本文Sublime Text均指Sublime Text 3。

特点：

* Multiple Cursor
* Vintage Mode (for vim user)
* Lightning Fast
* Massive Support and Documentation
* Command Palette Integrated
* Massive Plug-in and Packege Control

个人设置在Preferences/Settings-User里面

为Sublime Text创建命令行symbolic link，可以在terminal下直接打开文件或者project。

```bash
ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" /usr/local/bin/sublime
```
这里我用的是名称"sublime"，具体使用时可以用tab自动补全。你也可以用"subl"。

## 选择

⌘ d 可以连续选中同样的词

^ ⌘ g 可以选择全文中所有的这个词

⌥ cursor 按住option键可以做column selection竖着建立选区

⇧ ⌘ L 可以把选中的内容变成column selection

⌘ i **输入你要定位的word，可以定位到下一个出现的word**

⌘ ⇧ f 可以调出Find in Files...选项，做整个项目中的查找替换

## Command Palette

⌘ ⇧ p 打开Command Palette

  可以快速切换syntax highlight

记不住的快捷键可以在Command Palette里面快速输入，例如minimap。

## Goto Anything...

⌘ p 在project中快速跳转文件。

⌘ r 快速定位到函数定义Goto Symbol, css文件也同样适用。

⌘ p可以和@连用，定位到你要找的文件的函数

## 其它快捷键

按住⌘再按k，再按b可以打开或关闭sidebar

## Userful Packages

* [AdvancedNewFile][1]

  ⌘ ⌥ n 快速创建一个new file
  
* [SideBarEnhancements][2]
* [SublimeLinter][3]
* [MarkdownEditing][4]

[1]:https://github.com/skuroda/Sublime-AdvancedNewFile
[2]:https://github.com/titoBouzout/SideBarEnhancements
[3]:http://www.sublimelinter.com/en/latest/
[4]:https://github.com/SublimeText-Markdown/MarkdownEditing





