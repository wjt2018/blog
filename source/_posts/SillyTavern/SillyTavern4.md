---
title: SillyTavern酒馆教程(四) - 使用本地模型
date: 2024-9-18
cover: https://oydqrbee757owwwj.public.blob.vercel-storage.com/Snipaste_2024-09-07_13-48-05-SQWMYnATqu78zzoqVytrFZof5T21WC.png
categories: 
  - AI
tags: 
  - SillyTavern酒馆
---
**本节属于选修课程**

引言：我不想要我的老婆放在别人的服务器上？我担心我的隐私被泄露？我想要使用某个经过特殊训练的模型？那么本篇教程会解决上述的疑虑。本篇会告诉你如何在本地部署大语言模型，不再依赖服务商的服务，创造完完全全独立于互联网的赛博老婆。**但是值得注意的是，本地部署模型会依赖于本机的显卡性能。**

## 下载koboldcpp

第一步，打开koboldcpp github：https://github.com/LostRuins/koboldcpp

点击Releases
![alt text](images/post/ai/release.png)

找到最新的版本，然后根据自己硬件设备进行下载
![](images/post/ai/Snipaste_2024-09-18_13-02-41.png)

## 下载模型
推荐在huggingface里进行下载，另外TheBloke这里的模型是在出生时就被去除了思想钢印，免除了破限这一步的操作：https://huggingface.co/TheBloke

注意区分模型后缀，GGUF指运行时使用内存或内存显存混合；GPTQ是显存专用。

还要注意区分模型名里的几B，一般来说B数越高模型越聪明同时也越吃显存。一般32g显存推荐使用13b或20b模型；64G可以跑70B。
![](images/post/ai/Snipaste_2024-09-18_13-23-50.png)

量化尺寸是几Q，一般推荐Q5_K_M的，参数越大内存占用越多，质量会稍强一些。不建议使用低于Q4的，质量损失较大。
![](images/post/ai/Snipaste_2024-09-18_13-21-34.png)

## 运行koboldcpp

双击前面下载好的exe文件，然后出现下面这个对话框。选择模型然后点击launch。可以在弹出的localhost:5001里发送对话进行测试，如果正常回复则代表成功了。
![](images/post/ai/Snipaste_2024-09-18_13-28-44.png)

## 接入SillyTavern
按照下图进行配置
![](images/post/ai/Snipaste_2024-09-18_13-32-05.png)

连接成功后就可以在酒馆里选择一个角色发送对话进行测试了，如果本地硬件不是很好，可能需要等一小会才会给回复，进度可以查看koboldcpp的运行框。下图就代表模型已经正常回复了。
![](images/post/ai/Snipaste_2024-09-18_13-46-45.png)


more info:
- https://www.bilibili.com/video/BV14J4m1e77f/?spm_id_from=333.999.0.0&vd_source=da0a07b4180cd03f2f90d798401e9b42
- https://www.bilibili.com/video/BV1vQ4y1s7KQ/?spm_id_from=333.880.my_history.page.click&vd_source=da0a07b4180cd03f2f90d798401e9b42
- Webui:一个类似于koboldcpp的启动器，但是貌似只支持N卡。 https://github.com/oobabooga/text-generation-webui
- Discord群（自由AI阵线） ：https://discord.gg/5YXAeFSqPr 