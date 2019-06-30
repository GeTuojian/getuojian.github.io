---
title: 强化学习（1）：简介
date: 2019-06-25 00:01:46
tags:
- MachineLearning
- ReinforcementLearning
categories:
- 强化学习教程
mathjax: true
---

> 本系列基于Richard S. Sutton的教材《Reinforcement Learning: An Introduction》，总结并翻译各章节核心内容，对其做概要性的整理，复写书中主要样例的代码。

## 参考资料

1. 主要教材
   - [《Reinforcement Learning: An Introduction》](http://incompleteideas.net/book/the-book.html)
   - 主页：[Richard S. Sutton](http://incompleteideas.net/)
   - Slide & Video：[谷歌网盘](https://drive.google.com/drive/folders/0B3w765rOKuKANmxNbXdwaE1YU1k)
2. [莫凡python系列教程](https://morvanzhou.github.io/tutorials/)
3. 在线课程

   - [PSYCH 209: Neural Network Models of Cognition: Principles and Applications](http://web.stanford.edu/class/psych209/)
   - [udacity-deep-reinforcement-learning]( https://github.com/udacity/deep-reinforcement-learning)
   - [UCL Course on RL](http://www0.cs.ucl.ac.uk/staff/d.silver/web/Teaching.html)
4. 进阶教材：[《Reinforcement Learning: State of the Art》- Wiering M.A.](ed2k://|file|B52A9923923FF555110AE81AD0D384A7.pdf|14958513|20928309F624388FB3F59F7E1F993937|h=PDH6JPZEHNPHFP27BGNVPOUQDHIX4KXP|/)
5. 在线资料：

   - [DQN 从入门到放弃](https://zhuanlan.zhihu.com/p/21262246?refer=intelligentunit)
   - [强化学习读书笔记](https://www.cnblogs.com/steven-yang/p/6481772.html)
   - [增强学习](https://www.cnblogs.com/jinxulin/tag/%E5%A2%9E%E5%BC%BA%E5%AD%A6%E4%B9%A0/)
   - [强化学习系列](http://www.algorithmdog.com/ml/rl-series)
   - [强化学习专题系列](https://zhuanlan.zhihu.com/p/28563483)

<!--more-->

## 强化学习简介

### 是什么是强化学习？

强化学习的目标是学习如何采取***行动(actions)***来适应***环境(situations)***，以取得（长期）最大的***收益(rewards)***。

强化学习不同于监督学习、无监督学习，主要体现在两大方面：

1. ***探索(exploration)***和***利用(exploitation)***的权衡，是强化学习的算法中考虑的重点之一。
2. 如何通过与未知环境的互动，达到***全局目标***的最优。监督学习通常只考虑子问题的最优（样本集上的最优），不存在交互与优化的过程。

![机器学习范畴](ReinforcementLearning-1-Introduction/ML.png)

## 强化学习的主要构成元素





