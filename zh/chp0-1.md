---
layout: post
title: 源码开放学ARM - 前言
---

# 前言 
## 学习目标 
ARM 阶段的学习，构成了嵌入式软件开发工程师知识体系中不可缺少的一个内容。在这个阶段我们努力培养学员具备以下的素质和能力：  

        1) 掌握ARM体系结构和汇编语言。
        2) 能够初步学会阅读硬件原理图和芯片数据手册。
        3) 具备为SoC芯片常见外设如 UART，NandFlash，Timer 等编写驱动程序的能力。
        4) 能够完成 Bootloader 项目的程序编写和移植工作。
        
## 适合对象 
本阶段对于学员学习基础的要求如下：  

        1) 掌握C语言，熟悉指针的用法。
        2) 学过计算机组成原理和数字电路等课程。
        3) 具备一定的英文阅读能力。
        4) 对计算机底层的运行机制和软硬件协同工作具有浓厚的兴趣。


## 如何写作本书的 
对于有志于参与本书编写的学员，可以通过学习以下内容来进一步了解关于如何写书的相关知识。

### 如何安装 GIT  
- <http://progit.org/book/zh/ch1-4.html>

### 如何使用 GitHub  
- <http://www.worldhello.net/gotgithub/index.html>

### 如何用 markdown 写书  
- <http://www.slideshare.net/larrycai/write-book-in-markdown>

### 如何生成 pdf 版本  
- <http://github.com/larrycai/kaiyuanbook/blob/master/BUILD.md>

### 简介轻量级标记语言 Markdown  
- <http://www.worldhello.net/gotgithub/appendix/markups.html>

### Github 偏爱的 Markdown  
- <http://github.github.com/github-flavored-markdown/>

### 在线的 Markdown 编辑浏览器  
- <http://dillinger.io/>


## 致谢
在本书的编写过程中，得到了很多热心人士，同时也是技术高手的帮助。

[Peter Wang](http://weibo.com/happycasts)  (@happycasts) --  [开源电子书 LGCB](http://happypeter.github.com/LGCB/book) 的作者，从他这儿我学会了用 github 搭建这本书的写作方法，Peter 还有很多精心录制的[学习开源技术的视频](http://happycasts.net)，大家一定不要错过。  

[chunzi](https://github.com/chunzi/progit/tree/master/zh) -- [Pro Git](http://github.com/progit/progit/) 的中文译者，正是这本书教会了我如何使用Git，启发了我想要通过协作迭代来写书，后来我发现协作本身比写作更有乐趣。虽然后来没有采用Pro Git的框架，但还是非常感谢chunzi及时回复了我的邮件，相信这本书还会帮助到更多的人。

[Larry Cai](http://weibo.com/larrycaiyu) (@larrycaiyu) -- 上海爱立信研发中心的软件开发高级专家，从微博上加入["中文开源技术书"](http://q.weibo.com/1538118?source=weibo_tab)之后，身为管理员的larry就一直默默主动帮我解决从 markdown 到生成 pdf 格式的各种细节问题，他所维护的 [mkbok](https://github.com/larrycai/mkbok) 这里强烈推荐噢，以后一定会成为 GitHub 上写书必备之利器。

[tonghuix](http://weibo.com/tonghuix) -- 倡导自由开源生活方式的 [@爱开源未来](http://tonghuix.blog.com/)，正是他的提醒，我最后将本书的名字定为《源码开放学ARM》，以此表示对开源社区文化的尊重和支持，等有机会我会争取在开源硬件上写一本真正开源学习的书。

所有来自这些朋友的热情帮助和技术支持，让我常有如拨云雾而见青天之顿悟，虽然你们中的大多数未有机会得以见面，但对技术传播和分享的共同热爱使我们心灵相通。在此希望能够一并致谢。

## 参与
你可以通过关注新浪微博 [@亚嵌李明老师](http://weibo.com/limingth) 联系作者或者发邮件给 limingth AT gmail.com 告知你希望出现在书中的内容和想要解决的问题。

如果你愿意参与本书的编写，可以通过 fork [《源码开放学ARM》](http://github.com/limingth/LASO) 作出贡献。
    
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/2.5/cn/"><img alt="知识共享许可协议" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/2.5/cn/88x31.png" /></a><br />本<span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" rel="dct:type">作品</span>采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/2.5/cn/">知识共享署名-非商业性使用-相同方式共享 2.5 中国大陆许可协议</a>进行许可。



[上一节](../index.html)  |  [目录索引](../index.html)  |  [下一节](chp1-1.html)
