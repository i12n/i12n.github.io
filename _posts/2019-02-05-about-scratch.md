---
layout: post
title: "Scratch 简介"
description: ""
category: scratch
tags: [ scratch ]
---
{% include JB/setup %}

[Scratch](https://scratch.mit.edu) 是一种**编程语言**，也是一个**在线社区**，孩子可以在这里编写**故事**、**游戏**、**动画**等互动媒体，与世界各地的人进行分享与交流。在学习 Scratch 的同时，可以间接培养孩子的**逻辑推理**、**创意思考**和**协同合作**的能力。

<!-- more -->

Scratch 为 **8** 到 **16岁**的孩子而设计，但几乎所有的年龄段的人都能使用它，**5** 到 **7岁**的孩子可以尝试使用 [scratchjr](https://www.scratchjr.org)。Scratch 提供了线上和线下两种开发方式，线上可以访问 [Editor](https://scratch.mit.edu/projects/editor/) ，线下可以使用[桌面版](https://scratch.mit.edu/download)进行开发，2019年1月2日发布 3.0 版本。

Scratch 以 **积木** 的形式组织代码，每一块积木都是具有特定功能的模块，通过拖拽的方式将积木组织到一起，实现不同的功能。另外，Scratch 提供了声音、角色的编辑功能。

![cat-run](/assets/images/scratch/cat-run.gif)

积木类型分为 **9种**：

1. **运动** 用于控制角色的位置、旋转、碰撞检测，多用于动画等场景。

2. **外观** 控制造型、背景和对话弹窗的显示。

3. **声音** 控制声音的播放、暂停和音量调节等。

4. **事件** 监听用户的事件，如角色被点击、按键被按下等，当（when）事件发生时执行后续操作，是程序执行的入口。

5. **控制** 循环、条件、等待等控制，当条件满足时触发操作（if）。

6. **侦测** 侦测舞台上的行为是否被触发。

7. **运算** 字符运算、算术运算、逻辑运算等。

8. **变量** 定义变量

9. **自制积木** 定义一个代码块，可重复使用。

---

下面将实现一个火箭飞行游戏，控制火箭飞行, 避开小星星。

- 效果如下:

    ![rocket-fly](/assets/images/scratch/rocket-fly.gif)

- 资源如下:

    <a href="/assets/resources/scratch-rocket.svg" download> rocket.svg </a>

    <a href="/assets/resources/scratch-star.svg" download> star.svg </a>

    <a href="/assets/resources/scratch-rocket-fly.sb3" download> scratch-rocket-fly.sb3 </a>

1. **设置背景和角色**

    背景选择 `Stars`, 角色有两个 `rocket` 和 `star`, 由本地上传的 SVG 文件（上面的 rocket.svg 和 star.svg）。

    ![rocket-fly-setting](/assets/images/scratch/rocket-fly-setting.png)

2. **设置星星运动轨迹**

    第一步: 将小星星克隆 8 次, 舞台上会出现 9 个小星星

    第二步: 设置小星星随机运动

    第三步: 设置克隆小星星随机运动

    ![rocket-fly-star-coding](/assets/images/scratch/rocket-fly-star-coding.png)

3. 设置火箭控制

    第一步: 上/下/左/右 按键控制

    第二步: 碰撞到小星星提示“GAME OVER”, 结束游戏

    ![rocket-fly-rocket-coding](/assets/images/scratch/rocket-fly-rocket-coding.png)

4. 分数显示

    以火箭飞行的时长为分数，当发生碰撞之后分数不再增加。

    ![rocket-fly-score-coding](/assets/images/scratch/rocket-fly-score-coding.png)
