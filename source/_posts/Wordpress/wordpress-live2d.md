---
title: 如何在Wordpress里加载live2d看板娘模型（支持moc和moc3模型）
date: 2024-10-19
cover: /images/post/za/community-img-community-en.jpg
top_img: /images/post/za/undefined_image.png
keywords: wordpress, live2d, 看板娘, 看板君, 桌宠, 个人博客
categories:
  - 杂物
tags:
  - Wordpress
  - live2D
---

最近想要给乌鲁鲁星网站添加一个 live2d 的看板君，但是拿到的模型是 mod3 新格式的，找了一圈大多数教程都是 Cubism 2 版本的 Live2d 模型，其实官方早已经用 Cubism 4 版本的 Live2d 了，故写一篇教程来记录如何在 wordpress 里面添加 mod3 新格式的模型。

![](images/post/za/2024-10-19-21-38-43.gif)

## 第一步，确定你的 live2D 模型是什么旧模型还是新模型

拿到你的 live2d 模型后，先确认你的模型是 Cubism 2 制作的旧模型，还是 Cubism 3/4 制作的新模型

看一眼模型文件夹，如果有包含 css 文件、js 文件还有 module.moc 格式文件，那么就是 Cubism 2 版本的旧模型。

如果你的模型文件里面有 model.json 或 model3.json 文件，那么就是 Cubism 3/4 版本的新模型。

因为现在大多数都是使用 moc3 的新格式模型了，所以文本会侧重 moc3 模型进行阐述。

一般一个新格式live2d模型文件会有如下这些文件：
![](images/post/za/Snipaste_2024-10-19_21-21-34.png)

## wordpress 载入 live2d 方式一： wordpress 插件 [live2dweb](https://cn.wordpress.org/plugins/live-2d/)

这个插件支持 moc 和 moc3 模型，也就是同时支持新旧模型，但是 moc3 新模型需要付费使用，49 元购买永久使用权。并且它还支持连入 openAI，可以实现和看板君对话，可以说是非常香了！

在 wordpress 里加入这个插件（不会安装插件的朋友出门另寻教程），可以看到有非常多设置，可以非常方便的进行定义，可谓是真傻瓜式开箱即食。

![](images/post/za/Snipaste_2024-10-19_17-18-15.png)

## wordpress 载入 live2d 方式二：[oh my live2d](https://github.com/oh-my-live2d/oh-my-live2d)

oh my live2d 是一个应用于浏览器环境且开箱即用的 Live2D 组件, 它支持所有版本的 Live2D 模型, 支持 `CDN` 或 `ES6 Module` 两种导入方式，目前和 VitePress、VitePress2、VitePlugin、hexo 都有很好的适配性，文档写的非常好看懂，除此之外最重要的是它免费！！！

### 首先，把你的 live2d 文件放到你的服务器里

然后记住它的位置，我这是放在`/wp-content/live2d-model/`文件夹下了，下面代码里这部分请换成你自己放置模型的地址

### 接下来，在 wordpress 里添加插件[Insert Headers And Footers](https://cn.wordpress.org/plugins/wp-headers-and-footers/)

如果你的主题或其他插件也支持了可以在 theme/footer.php、header.php 添加自定义代码的功能，那么请跳过这一步。

### 在主题文件`<body></body>`标签里面添加如下代码：

通过 CDN 把 oh my live2d 引入`<script src="https://unpkg.com/oh-my-live2d@latest"></script>`

接着添加你的模型，我在我的 **/wp-content/live2d-model/** 文件夹下放了两个模型，分别是`yueliangxing`和`zhuochongxing`分别把他们的 model3.json/model.json 文件引入。

```javascript
<script src="https://unpkg.com/oh-my-live2d@latest"></script>
<script>
  OML2D.loadOml2d({
    dockedPosition: "right", // 默认值是left，我想要看板娘出现在右侧，请按需修改
    models: [
      {
        path: "你的域名/wp-content/live2d-model/yueliangxing/StarDesktop.model3.json",
        position: [0, 30],
        scale: 0.08,
        stageStyle: {
          height: 300,
          width: 300,
        },
      },
      {
        path: "你的域名/wp-content/live2d-model/zhuochongxing/zhuochongxing.model3.json",
        position: [-70, -50],
        scale: 0.18,
        stageStyle: {
          height: 300,
          width: 300,
        },
      },
    ]
  });
</script>
```

### motion 动作

motion动作对应模型文件里的motion3.json文件

如果你的模型有点击对应的区块会有不同的动作反应的功能，正常情况下，引入以后该功能就直接能使用的。

但是如果不生效，请先检查一下你的 model3.json 文件里面`motions`对象里面，`file`对应的是不是 motion3.json 文件格式，如下这段代码就是正常的，如果是对应的 exp3 或者其他格式，那么这个功能你使用不了。

```
"Motions": {
  "Idle": [
    {
      "File": "motion/haru_g_idle.motion3.json"
    },
    {
      "File": "motion/haru_g_m07.motion3.json"
    },
    {
      "File": "motion/haru_g_m15.motion3.json"
    }
  ],
  "Tap": [
    {
      "File": "motion/haru_g_m14.motion3.json",
      "Sound": "../shizuku/sounds/flickHead_00.mp3"
    },
    {
      "File": "motion/haru_g_m05.motion3.json",
      "Sound": "../shizuku/sounds/flickHead_01.mp3"
    }
  ]
}
```

### expression 表情

expression表情对应模型文件里的exp3.json文件

除此之外，目前 oh my live2d 并没有开放修改 expression 表情的 api，但是通过他的上游代码[pixi-live2d-display](https://github.com/guansss/pixi-live2d-display)，我找到了修改的方法，比如我给 menu 添加了一个按钮，在这个按钮里可以获取到`oml2d`对象，直接使用上游代码的随机表情的切换api：`void oml2d.models.model.internalModel.motionManager.expressionManager.setRandomExpression()`。

同理，如果有哪些功能是oh my live2d没有提供api的，也可以试着查看一下pixi-live2d-display文档，直接使用pixi-live2d-display的api进行实现。

```
menus: {
  items: [
		{
        id: 'SwitchModelClothes',
        icon: 'icon-skin',
        title: '百变星星',
        onClick: (oml2d) => {
		  void oml2d.models.model.internalModel.motionManager.expressionManager.setRandomExpression()
        }
		}
  ]
}
```

### 其他

若你想要修改状态栏、菜单、弹出的消息，可以参考官方文档进行设置，官方文档写的非常详细。

### 完整代码：

```javascript
<script src="https://unpkg.com/oh-my-live2d@latest"></script>
<script>
  OML2D.loadOml2d({
	  dockedPosition: 'right',
    models: [
		{
        path: '你的域名/wp-content/live2d-model/yueliangxing/StarDesktop.model3.json',
        position: [0, 30],
        scale: 0.08,
        stageStyle: {
          height: 300,
			width: 300
        }
      },
      {
        path: '你的域名/wp-content/live2d-model/zhuochongxing/zhuochongxing.model3.json',
        position: [-70, -50],
        scale: 0.18,
        stageStyle: {
          height: 300,
			width: 300
        }
      }
    ],
	  statusBar: {
		  mobileStyle: {
			  display: 'none'
		  },
		  restMessage: '星星正在睡觉',
		  loadFailMessage: '星星好像失联了',
		  loadSuccessMessage: '闪来了',
		  loadingMessage: '星星正在路上'
	  },
	  menus: {
		  items: [
      {
        id: 'Rest',
        icon: 'icon-rest',
        title: '星星困了',
        onClick: (oml2d) => {
          oml2d.statusBarOpen(oml2d.options.statusBar?.restMessage); // 展示状态条
          oml2d.clearTips(); // 清除当前提示框内容, 并停止空闲消息播放器

          // 为状态条绑定点击事件
          oml2d.setStatusBarClickEvent(() => {
            void oml2d.statusBarClose(); // 关闭状态条
            void oml2d.stageSlideIn(); // 舞台滑入
            oml2d.statusBarClearEvents(); // 清除所有事件
          });

          void oml2d.stageSlideOut(); // 舞台滑出
        }
      },
		{
        id: 'SwitchModelClothes',
        icon: 'icon-skin',
        title: '百变星星',
        onClick: (oml2d) => {
		  void oml2d.models.model.internalModel.motionManager.expressionManager.setRandomExpression()
        }
      },
      {
		  id: 'SwitchModel',
		  icon: 'icon-switch',
		  title: '切换星星',
		  onClick: (oml2d) => {
			oml2d.loadNextModel();
		  }
		}
    ],
	  },
		tips: {
			  messageLine: 4,
			  style: {
			      top: '-40px'
			  },
			idleTips: {
			  message: [
			  '这一路上，闪电拂过星辰有无数个瞬间，但我不能停下，我的意思是…我好想你',
			  '还是让星星自己落下来，留在你身边吧',
			  '如果这个世界真的已经无处可逃，那至少，你还可以逃来我身边',
			  '等颂歌里唱的都成真，等下次见面，换我来做你的骑士',
			  '宇宙在你沉睡的时候消失不见',
			  '如果下一个春天还很遥远，那我们就现在见面吧',
			  '星星闪烁的频率，是宇宙的心跳',
			  '我们终会再见，在流星降临的夜晚',
			  '双星之剑永远指向同一个方向',
			  '闪来了',
			  '楼下的猫很想你，我也是',
			  '宇宙第三定律：是我和你',
			  '骑上最快的自行车，一起去追今夜划过天际的那颗流星',
			  '我感应到的宇宙，是你',
			  '星辰花开之时，远航的人就会回到故乡',
			  '宇宙吸引力法则第二条——想见的人，无论过多久都会再见',
			  '我的光芒，只朝向你在的方向',
			  '只要我的心脏还在跳动，就会站在你身旁，直到最后一刻',
			  '人类从未放弃巴别塔的妄想，但永远，无法抵达塔尖',
			  '总有一颗星星是为你而来的',
			  '无论多少次，无论在哪里……我都会找到你',
			  '在浩瀚无垠的星空中，星光将会指引我们再次相遇',
			  '但你眼里的每一个我，也都只属于唯一的你',
			  '你是指引我回家的那颗星'
		  ]
			  }
 		}
  });
</script>
```

贴几个免费的模型下载地址：

- [【免费 Live2D 模型】小魔女一键出道+自带直播 UI+舰长礼物稿哔哩哔哩 bilibili](https://www.bilibili.com/video/BV1u14y1f7ik/?t=4&spm_id_from=333.1350.jump_directly&vd_source=b321576eba39aedaf1743088883e6c6b)
- https://github.com/Eikanya/Live2d-model
- https://github.com/oh-my-live2d/live2d-models/tree/main/models

https://cn.wordpress.org/plugins/live-2d/ wordpress 插件
