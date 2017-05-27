---
layout: post
title:  "深度学习小玩意 Flappy Bird"
date:   2017-05-27
author: "Huang Qiang"
tags: [deep learning, reinforcement learning, flappy bird]
comments: true
---

![](https://github.com/yenchenlin/DeepLearningFlappyBird/raw/master/images/flappy_bird_demp.gif)

这个玩意最难的地方在于[装opencv3](https://nickyfoto.github.io/blog/entries/install-opencv3-osx)。

其它只要你这些库装好，就能玩起来了。当然主要是看。因为我们是用别人训练好的模型，有机会在一起学习如何训练自己的模型。

```sh
pip install menpo
pip install pygame
pip install tensorflow
git clone https://github.com/yenchenlin/DeepLearningFlappyBird.git
cd DeepLearningFlappyBird
python deep_q_network.py
```

[代码](https://github.com/yenchenlin/DeepLearningFlappyBird)来自 Yenchen Lin。