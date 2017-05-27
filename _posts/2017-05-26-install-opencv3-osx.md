---
layout: post
title:  "在macOS Sierra上安装 OpenCV 3"
date:   2017-05-26
author: "Huang Qiang"
tags: [opencv]
comments: true
---

[OpenCV](http://opencv.org/) 是计算机视觉处理任务中的常用库，也因为复杂度高，对他的安装也不是那么一蹴而就。今天重新把它给本机Python2，Python3重新安装了一些，特此做个记录。

操作环境:

- macOS 10.12.5
- Python 2.7.13
- Python 3.6.1
- cv2 version 3.2.0

首先你需要安装 [Homebrew](http://brew.sh/)，这个不用说了。完成之后用它来安装两个python：

```sh
$ brew install python python3
```

这里再次强调一下，mac系统自带的python不是用来给你做开发的。其它常用的工具，ruby，git等，都用brew装一遍。安装完成之后link一下：

```sh
$ brew linkapps python
$ brew linkapps python3
```

查看是否是brew版本的两个python：

```sh
$ which python
/usr/local/bin/python
$ which python3
/usr/local/bin/python3
```

你可以在命令行下，使上述配置生效

```sh
source ~/.bash_profile
```

查看brew安装的两个python的版本号：

```sh
$ python --version
Python 2.7.13
$ python3 --version
Python 3.6.1
```

brew不能一键安装OpenCV，你需要加一个tap

```
brew tap homebrew/science
```

下面是复杂的地方，你以为配置完这个tap就可以搞定了。但是如果你直接安，可能会报错：

```
opencv3: Does not support building both Python 2 and 3 wrappers
```

解决办法有两个，我只写作者推荐的那个

原因是现有版本做了不必要的check。所以你需要编辑一下配置文件：

```
brew edit opencv3
```

如果你没设置Homebrew编辑器，默认会用vim打开

你需要在第187行找到这几行字。把它们用 `#` 号comment掉。

```
if build.with?("python3") && build.with?("python")
  # Opencv3 Does not support building both Python 2 and 3 versions
  odie "opencv3: Does not support building both Python 2 and 3 wrappers"
end
```

修改前：


![](http://www.pyimagesearch.com/wp-content/uploads/2017/05/resolving_homebrew_error_brew_edit_02.jpg)

修改后：

![](http://www.pyimagesearch.com/wp-content/uploads/2017/05/resolving_homebrew_error_brew_edit_01.jpg)

保存退出，然后开始正式安装。

```
brew install opencv3 --with-contrib --with-python3
```

要让python找到你刚刚安好的cv2，需要再敲一行：

```sh
$ echo /usr/local/opt/opencv3/lib/python2.7/site-packages >> /usr/local/lib/python2.7/site-packages/opencv3.pth
```

这一行创建了一个 `.pth` 文件告诉 Homebrew’s 的 Python 2.7 额外的包在 `/usr/local/opt/opencv3/lib/python2.7/site-packages` 找。 

这还没完，你会发现cv2在python3的路径下有一个搞笑的名字叫`cv2.cpython-36m-darwin.so`。

```
$ ls -l /usr/local/opt/opencv3/lib/python3.5/site-packages/
total 6952
-r--r--r--  1 admin  admin  3556384 Dec 15 09:28 cv2.cpython-36m-darwin.so
```

你需要把它重命名成 `cv2.so`

```sh
$ cd /usr/local/opt/opencv3/lib/python3.6/site-packages/
$ mv cv2.cpython-36m-darwin.so cv2.so
$ cd ~
```

还没完，你需要告诉 homebrew 的 python3，去哪里找这个库：

```sh
$ echo /usr/local/opt/opencv3/lib/python3.6/site-packages >> /usr/local/lib/python3.6/site-packages/opencv3.pth
```

验证：

```
$ python
Python 2.7.13 (default, Dec 18 2016, 07:03:39)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.42.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.2.0'
```

```
$ python3
Python 3.6.1 (default, Apr  4 2017, 09:40:21)
[GCC 4.2.1 Compatible Apple LLVM 8.1.0 (clang-802.0.38)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.2.0'
```

至此大功告成，可以玩耍了。详情还是可以仔细参考下面这两篇文章，讲的很好：

[Install OpenCV 3 on macOS with Homebrew (the easy way)](http://www.pyimagesearch.com/2016/12/19/install-opencv-3-on-macos-with-homebrew-the-easy-way/)

[Resolving macOS, OpenCV, and Homebrew install errors](http://www.pyimagesearch.com/2017/05/15/resolving-macos-opencv-homebrew-install-errors/)