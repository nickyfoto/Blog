---
layout: post
title:  "命令行翻墙"
date:   2016-02-22
author: "Huang Qiang"
tags: [osx, proxychains-ng, gfw, shell]
comments: true
---

在mac上用[cow][1]的好处是可以自动加手动的对网站有没有被墙进行判断，坏处是命令行用不了。最近homebrew改进了很多，但是gem还是经常连接失败。之前别人推荐了[proxychains-ng][2]，但是我开始用的时候因为10.11 El Capitan加入了[SIP][3]之后，proxychains-ng就不好用了。

原因是[SIP][3]对

- /System
- /usr
- /bin
- /sbin

等目录进行了限制，即使用root权限也不能调用某些Unix Executable。有几种办法绕开这个限制。

1. 在recovery mode键入

    ```bash
    csrutil enable --without debug
    ```

2. 把要用的binary复制到无限制的目录一份例如/usr/local/XXX/，并且改变优先级
3. 用homebrew下载的binary，而非系统自带的。这是目前比较可行的[办法][4]。


[1]:https://github.com/cyfdecyf/cow
[2]:https://github.com/rofl0r/proxychains-ng
[3]:https://support.apple.com/en-us/HT204899
[4]:https://github.com/rofl0r/proxychains-ng/issues/78