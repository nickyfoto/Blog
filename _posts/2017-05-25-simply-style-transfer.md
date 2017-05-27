---
layout: post
title:  "深度学习小玩意 Style Transfer"
date:   2017-05-25
author: "Huang Qiang"
tags: [deep learning, style transfer]
comments: true
---

这里不是教你如何生成可以实现风格迁移的模型（我们以后可以教），这里是借用别人训练好的模型和代码，自己跑着玩。

![](../images/style-cat.png)

上图就是一只猫，加一幅著名浮世绘的话，生成一只浮世绘版猫咪。

我假设你已经有python环境（推荐py3），这个玩具需要下列库：

```sh
$ pip3 install scipy pillow tensorflow
```

克隆大神代码：

```sh
$ git clone https://github.com/lengstrom/fast-style-transfer.git

```

进入 `fast-style-transfer` 目录[下载](http://cn-static.udacity.com/mlnd/Fast_Style_Transfer_Models.zip)一个你喜欢的模型，例如 `rain_princess.ckpt`。就可以跑代码玩起来了：

```sh
python evaluate.py --checkpoint ./rain-princess.ckpt --in-path <path_to_input_file> --out-path ./output_image.jpg

```
切勿 transfer 太大的图像，你可能要等很久！

---

附录：几个style的样式：

- la muse

![](https://github.com/lengstrom/fast-style-transfer/raw/master/examples/style/la_muse.jpg)

- rain princess

![](https://github.com/lengstrom/fast-style-transfer/raw/master/examples/style/rain_princess.jpg)

- the scream

![](https://github.com/lengstrom/fast-style-transfer/raw/master/examples/style/the_scream.jpg)

- the shipwreck of the minotaur

![](https://github.com/lengstrom/fast-style-transfer/raw/master/examples/style/the_shipwreck_of_the_minotaur.jpg)

- udnie

![](https://github.com/lengstrom/fast-style-transfer/raw/master/examples/style/udnie.jpg)