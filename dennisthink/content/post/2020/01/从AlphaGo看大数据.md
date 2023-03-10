---
title: 从AlphaGo看大数据.md
date: 2020-01-20T21:38:52+08:00
lastmod: 2020-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
tags: ["code-note"]
---

# 从AlphaGo看抖音推送----数值化的时代

记得以前听过一个笑话,是关于数学家的。有一个数学家去应聘消防队员,队长问:"如果发现仓库着火了,应该怎么办?",数学家答:"拿着灭火器把火扑灭"。队长说:"回答的不错,如果仓库没有着火呢？",数学家答:"那么就把它点着了"。队长一脸懵的问:"为什么要把仓库点着?",数学家回答:"这样就可以把一个未知问题转为已知问题了"。

这个笑话虽然有点冷,但是非常好的引出了我今天要聊的话题。也就是将很多的信息数据化以后,我们会得到什么。

## 1、街头下棋和手柄下棋

我是在小学三年级学会下中国象棋的,最开始是跟姐夫下。后来就是跟一起读书的小伙伴下,放学后杀两盘的感觉还是不错的。再后来跟街头摆摊的大爷下,输了5毛钱。后来就是跟用手柄跟红白机下了,红白机的象棋可以选择不同的难度,但是不能知道每一步对方的思考思路,所以不能做复盘。当时并不知道为什么红白机可以下象棋,只是觉得电脑的思路有点呆板,不过到后来学习了估值函数明白了电脑是如何下棋的了。

## 2、从深蓝到AlphaGo

1997年,IBM的深蓝计算机赢得了国际象棋冠军卡斯帕罗夫,是在下棋这件事情上,计算机第一次战胜人。后来人类把希望寄托在围棋上,因为围棋会有更多的可能,需要更多的算力。2017年,谷歌的AlphaGo挑战世界围棋冠军柯洁,以3:0获胜。自此,在主流的棋类运动(完全信息双方博弈)上,在计算机面前,人类已经不是其对手。这一切都要依赖于把所有的棋子信息,转为权值函数,然后通过函数来计算最优的棋子位置。但是在桥牌上,目前的AI还没有特别好的思路,主要是因为很多牌面的信息是隐藏的,所以在权值函数无法计算,因此也就没有办法保证完全胜利。

## 3、从用户画像到抖音推送

我一个朋友是做公司的推荐系统的,他经常会提到一个词汇叫做“用户画像”。也就是会有很多的数据来描述这个用户的行为和特征,以此来为这个用户进行推荐。我们知道很多的网页和App可以进行埋点,通过埋点可以获取用户的行为数据,比如点击了什么地方,在哪个网页的什么位置停留了多久,观看某个视频多长时间。通过这些数据,来构建模型对用户的喜好、性格、选择倾向的进行描述。这些方法使得之前的很多不可能变成了可能,也就是完成的信息从不可观察不可量化,到部分可观察可量化的程度。

推荐某种程度上算反馈调节,不过现在的很多推荐是一种正反馈而不是负反馈,也就是相关性推荐。信息茧房就是相关性正反馈推荐的不太好的结果,抖音的推荐也是类似于这样的,吃糖会产生更多的多巴胺,但是吃糖太多也可能会有蛀牙等产生。很多的推荐算法只考虑了多巴胺的因素,没有考虑蛀牙的因素,因为推荐算法的目标不是你的健康,而是卖出更多的糖。

## 4. 总结

以上的观点只是我个人的一点看法,如果您有什么不同的意见和看法,欢迎留言交流。

> 可以聊聊文学，   
> 可以聊聊摄影，   
> 可以聊聊程序，   
> 可以聊聊感悟，   
> 你想听我聊什么，请告诉我。

![image](https://mmbiz.qpic.cn/mmbiz_jpg/IDHaWiaS8DJpDWaY4ZNTpQR4riciaVTEqPkpwGNwbmUxHUjv8licNxNlD9IEia7rCb8KYibdRWCiamYGRfetNW1CyqWTQ/0?wx_fmt=jpeg)