---
title: SillyTavern酒馆教程(一) - 快速开始
cover: https://oydqrbee757owwwj.public.blob.vercel-storage.com/Snipaste_2024-09-07_13-48-05-SQWMYnATqu78zzoqVytrFZof5T21WC.png
categories: 
  - AI
tags: 
  - SillyTavern酒馆
  - Claude
---
**本篇教程适用于Windows设备**

SillyTavern 是一个可以安装在电脑（和安卓手机）上的用户界面，让您可以与文本生成的人工智能互动，并与您或社区创建的角色聊天/玩角色扮演游戏。

移动设备界面友好，多种人工智能服务或模型支持（KoboldAI/CPP, Horde, NovelAI, Ooba, OpenAI, OpenRouter, Claude, Scale），类似 Galgame 的 老 婆 模 式，Horde SD，文本系统语音生成，世界信息（Lorebooks），可定制的界面，自动翻译，和比你所需要的更多的 Prompt。附带扩展服务，支持文本绘画生成与语音生成和基于向量数据库 的聊天信息总结。

由于 SillyTavern 只是一个用户聊天界面，它对硬件性能的要求很低，可以在任何电脑上运行。需要强大性能的是人工智能系统。

![](/images//post/ai/image.png)
![](/images//post/ai/image-1.png)

## 环境准备

### 1.安装Git
[git下载地址](https://git-scm.com/downloads)

非本篇重点内容，请参照网络上其他的教程

### 2.安装NodeJS
[nodejs下载地址](https://nodejs.org/)

非本篇重点内容，请参照网络上其他的教程

### 3.科学上网

## 部署SillyTavern
### 下载SillyTavern
进入[SillyTavern github](https://github.com/SillyTavern/SillyTavern?tab=readme-ov-file)中将代码拉到本地不包含中文的路径下

``` bash
git clone https://github.com/SillyTavern/SillyTavern.git
```

拉下来以后双击运行文件夹下的**Start.bat**，此时会弹出一个黑色运行框(不要关闭此黑色运行框！)，稍等一会浏览器会弹出``http://127.0.0.1:8000/``。这就是酒馆的界面了。
![酒馆初始界面](https://oydqrbee757owwwj.public.blob.vercel-storage.com/Snipaste_2024-09-07_15-02-27-kWgoYFhLCt5riFgevG5c9N7U0UPU6F.png)

语言可以选择简体中文，然后无特殊需求就点“保存”即可，这里的信息后续也是可以修改的。

### Claude
现在的酒馆已经部署好了，但是他并不具备ai的能力，也就是现在它还没有大脑，如果想要在这里能够正常聊天需要给他把大脑装上。

现在一般大家公认的进行角色扮演最优秀的大语言模型是Claude，所以接下来就以Claude进行演示。想要深入了解不同大语言LLM之间的区别可以看看这个：[主流大语言模型对比](https://github.com/wsxqaza12/Comparison-of-LLM-Specifications?tab=readme-ov-file)。
![LLM对比](/images/post/ai/1280X1280.PNG)

想要将Claude的服务接进酒馆里一般有两种方案，一种是使用cookie，一种是使用api。我推荐的是直接淘宝购买api来使用，虽然贵一点但是稳定方便。

#### 使用api

拿到api以后参照下图在酒馆中进行配置

这里也列一下大家购买api时看到的中转B和C的区别
|| 中转B     | 中转C|
| ----------- | ----------- | ----------- |
| 使用      | 直接使用，无需梯子       | 激活后使用，大部分地区需要梯子       |
| 计时   | 第一次使用开始计时        |激活后开始计时|
|上下文大小| 100k|100k|
|最大回复|4096|4096|

#### 使用cookie
目前的情况是这样的，普通每一个cookie一般进行十几次二十次对话以后它就会进入3、4小时的冷却，所以一般如果要使用cookie的话需要一次性注册很多个账户可能才够用。

想要获取cookie可以自行进行Claude注册来获取，或者淘宝购买两种方式。

论坛里现在很多人使用的是claude pro退款流，据说不会那么快进入冷却，且性价比较高，我还没有尝试过，感兴趣的同学可以试试，教程放在这里：
https://rentry.org/8t4a5toi
https://rentry.co/zbfn8te4

但是我不太推荐自行进行Claude注册来进行获取方式，国内用户注册起来比较麻烦，而且很容易被封号。我这里放一个教程，想尝试的可以看看[注册/抓取曲奇](https://sqivg8d05rm.feishu.cn/wiki/HST5wAAxeibfzEkrCvHcIU8dnge)

个人真的很不推荐使用cookie哦，如果你执意要使用cookie还需要再下载一个[clew](https://github.com/teralomaniac/clewd?tab=readme-ov-file)搭配使用。

clew的使用方法是下载下来以后在config.js里以``["Cookie1","Cookie2","Cookie3"]``的格式把cookie填好，然后双击运行start.bat。

按照下图在酒馆中进行配置：
![img](https://oydqrbee757owwwj.public.blob.vercel-storage.com/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20240907153238-e2xC1aHbAkkb8z0I4CbVugOV2wLFh5.png)


至此，实现了可以在酒馆中和ai对话的功能。但是酒馆可以做的不仅仅只有这么点，下一篇将告诉大家如何进行破限和导入角色。遇到问题可以在下方进行留言。

more info：[宝宝教程](https://sqivg8d05rm.feishu.cn/wiki/BBocw85QTiA8EXkNcUZcT2pCnIe)
https://docs.sillytavern.app/
https://www.bilibili.com/read/cv28322654/