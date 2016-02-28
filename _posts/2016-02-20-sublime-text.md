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
* New view into file 一个文件打开在两个tab或者两个column ，编辑一个文件的一个位置，查看另一个位置

## 设置

个人设置在Preferences/Settings-User里面

针对特定文件的设置在Preferences/Settings-More/Syntax Specific - User

针对特定project也可以设置Settings。在your_name.sublime-project下添加。例如

```json
{
	"folders":
	[
		{
			"path": "."
		}
	],
    "settings":
    {
        "tab_size": 2,
        "translate_tabs_to_spaces": true
    }
}
```

为Sublime Text创建命令行symbolic link，可以在terminal下直接打开文件或者project。

```bash
ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" /usr/local/bin/sublime
```
这里我用的是名称"sublime"，具体使用时可以用tab自动补全。你也可以用"subl"。

## 选择

* ⌘ d 可以连续选中同样的词；（⌘ k）可以让你跳过当前不想选择的词，继续选下面的；（⌘ u）让你退回上一个选择
* ⌘ L 选中一行
* 通过菜单栏Selection/Expand Selection还可以选中一段，默认没有快捷键
* ⌘ ⇧ a 可以选择html tag
* ^ ⇧ M 可以扩展括号选择，scheme尤其有用
* ⌘ ⇧ j 按indentation来选中
* ^ ⌘ g 可以选择全文中所有的这个词
* ⌘ ⇧ space 可以选中scope，但是跟输入法切换冲突。改为f3
* ⌥ cursor 按住option键可以做column selection竖着建立选区
* 按住⌘ + cursor可以在任意位置添加多个鼠标
* ⇧ ⌘ L 可以把选中的内容变成column selection
* ⌘ i **输入你要定位的word，可以定位到下一个出现的word** 这是利用search做navigation 如果不想跳转，按esc，回到你cursor原来的位置

## 查找

* ⌘ f 按enter或者 ⌘ G 可以跳转搜索到的下一个
* 上下箭头可以跳转搜索历史
* ⌘ ⌥ f 查找与替换，还有Preserve Case功能
* ⌘ ⇧ f 可以调出Find in Files...选项，做整个项目中的查找替换
  * 关闭Show Text，可以在下方打开搜索结果
  * f4按顺序打开搜索到的文件到tab  

## 编辑

* ^ ⌘ 上下，上下行交换

## Command Palette

* ⌘ ⇧ p 打开Command Palette

  * 可以快速切换syntax highlight

* 记不住的快捷键可以在Command Palette里面快速输入，例如minimap。

## Goto Anything...

* ⌘ p 在project中快速跳转文件。

* ⌘ r 快速定位到函数定义Goto Symbol, css文件也同样适用。

* ⌘ p可以和@连用，定位到你要找的文件的函数。不同扩张有不同的定位内容。

* ⌘ p 在输入冒号加数字，可以跳转到特定行



## 其它快捷键

* 按住⌘再按k，再按b可以打开或关闭sidebar
* ⌘ ⇧ t 打开最后关闭的文件
* ⌘ + number 跳转到打开的第几个tab 
* ⌘ ⌥ + 左右arrow，切换tab
* ⌘ [ ] 做indent
* ctrl m 可以在matched bracket之间跳转
* ⌥ [ : left double quote
* ⌥ ⇧ [: right double quote
* ⌥ ]: left single quote
* ⌥ ⇧ ]: right single quote

## Userful Packages

* [AdvancedNewFile][1]
  * ⌘ ⌥ n 快速创建一个new file
* [SideBarEnhancements][2]
* [SublimeLinter][3]
* [MarkdownEditing][4]
* ReadmePlease 
  * quick open readme.md for all packages you installed
* LineEndings
* Git
* GitGutter
* PlainTasks

[1]:https://github.com/skuroda/Sublime-AdvancedNewFile
[2]:https://github.com/titoBouzout/SideBarEnhancements
[3]:http://www.sublimelinter.com/en/latest/
[4]:https://github.com/SublimeText-Markdown/MarkdownEditing





