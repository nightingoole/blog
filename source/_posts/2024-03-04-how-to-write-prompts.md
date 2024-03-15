---
title: 如何书写提示词？
date: 2024-03-04 15:01:50
description: 在stable-diffusion中如何快速写出让AI看得懂的提示词呢？
tags: [stable-diffusion, AI绘画]
---
# **提示词书写方式**

- 提示词要是英文
- 提示词以词组为单位，不需要有完整的语法结构

> “一条又长又宽的面和一个又大又圆的碗”→（面，长，宽），（碗，大，圆）

- 词组之间用英文的半角逗号,隔开
- 提示词可以换行，末尾最好加上逗号

# **提示词的分类**

## **内容型提示词**

- 人物及主体特征
- 场景特征
- 环境光照
- 补充：画幅视角

![](G5JSbTQvQontIexYbqCcR3vanfc.png)

## **标准化提示词**

只有内容型提示词画出的东西很大概率不会满意
则需要加入其他提示词——**标准化提示词**

- 常用

best quality,ultra- detailed,masterpiece,hires,8k,extremely detailed CG unity 8k wallpaper

最高的质量，超级细节，杰作，高分辨率，8k（分辨率），超级细节的 Unity CG 壁纸

- 画风

插画风：illustation,painting,paintbrush

二次元：anime,comic,game CG

写实系：phototorealistic,realistic,photograoh

## **提示词模板框架**

![](AgbTbtXCKojoUTxD8xgcqZy2nwg.png)

![](IB3Sb4fJJo86PhxjbzqckOfXnmh.png)

# **提示词权重**

## **括号**

1. 圆括号( )：例：(词组)，权重为原来的 1.1 倍
2. 花括号{ }：例：{词组}，权重为原来的 1.05 倍
3. 方括号[ ]：例：[词组]，权重为原来的 0.9 倍

括号都是英文的

可以套多层括号，每套一层就乘对应的倍数

例：(((词组)))，权重：1.1***1.1*1.1=1.331 倍

## **数字 + 括号**

例：(词组:1.5)，权重为原来的 1.5 倍

![](ZwhcbTmJ9oZlhKxIoyCcs5qInBb.png)

## **权重分配**

避免个别词条权重太高，安全范围在 1±0.5

![](JLmMbfwJAojBiIxbZBlcKxbLnWg.png)

## **进阶语法**

![](WRLMbnyOAozsxuxf5pZcFJernBh.png)

# **反向提示词**

你不希望画面中出现什么内容，就往反向提示词里丢词条

![](WfOAbLNpIoAfxAxDzatc8vhxnZd.png)

# **出图参数**

## **采样步数**

模拟一次画面会清晰一些，生成画面每闪一下代表迭代了一步。步数一般为 **20**，算力充足要求高就设置 **30-40**，最低不要低于 **10。**

![](KEjVb3R2qoxUdoxQhUVcMTXQnBg.png)

![](SxI2bSSx9oufhFxPkNLc9Csqnhh.png)

## **采样方法**

AI 生成图形时所使用的某种算法，一般常用就 4~5 个算法：

- Euler 和 Euler a 适合插画风格
- DPM 2M 和 2M Karras 速度较快
- SDE Karras 细节较为丰富

## **宽度和高度**

默认：<u>512*512</u>

一般先低分辨率绘制，再高清修复（Hires Fix）来放大

## **高清修复（Hires Fix）**

提高出图的分辨率

# **面部修复**

采用对抗算法识别人物面部并进行修复

## **平铺/分块（Tiling）**

用来生成无缝贴满整个屏幕的纹理性图片

## **提示词相关性（CFG Scale）**

数值越高，越贴近提示词；与权重一样不会浮动太多，太高容易变形

比较安全的数值：<u>7~12</u>

## **随机种子**

控制画面一致性的重要参数

## **生成批次**

按照同一组提示词和参数出图的次数

**每批数量：每批次绘制的图像数量（显存小的容易爆，不建议调）**

# **写提示词的三大方法**

## **翻译大法**

用自然语言说出想要画的东西

## **借助工具**

Tag 生成器：NovelAI tag 生成器        [https://wolfchen.top/tag/](https://wolfchen.top/tag/)        2.1 全新升级！

魔咒百科词典        [https://aitag.top/](https://aitag.top/)        网友分享的 tag 组合很多

Danbooru 标签超市        https://tags.novelai.dev/        超级好用！这就是我理想的交互方式

魔导绪论        [https://magic-tag.netlify.app/](https://magic-tag.netlify.app/)        全 tag 搜索，交互方式也很有趣

魔法导论        [https://www.noveltags.com/allTag](https://www.noveltags.com/allTag)        全 tag 搜索

## **抄作业**

复制别人在网上分享的提示词：

- openart.ai：有很多基于 SD 官方模型和欧美主流模型生成的作品
- arthub.ai：二次元和亚洲审美的内容会多些
- civitai.com：专业 ai 绘画网站

