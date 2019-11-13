---
layout: post
title:  "Unity实现图片切割效果 - 概述"
date:   2019-11-13 12:00:00 +0800
categories: Unity
---

先贴上效果图![在这里插入图片描述](https://img-blog.csdnimg.cn/20191111184333419.gif)
### 一、用到的Unity相关技术
##### 1. PolygonCollider2D
	1. 顶点编辑
##### 2. LineRenderer
	1. 画线
##### 1.Mesh
	1. 创建mesh的顶点
	2. 设置mesh的三角面
	3. 设置uv贴图

### 二、用到的算法
##### 1. 计算两线段交点
##### 2. 点集中的点按照线段分区域（线段上、线段上方、线段下方）
##### 3. 点集中的点按顺时针排列
---
## 实现思路
1. PolygonCollider2D勾勒出可被切割的区域，并得到组成图形的线和顶点
2. 画线
3. 计算画的线和图形的线交点
4. 将顶点和交点按照画的线分区域（在线上、线上方、线下方）得到两个新的点集，为切割后的两部分点集
5. 分离出的点顺时针排序，为创建mesh作准备
6. 依照排序好的点集作为新图新顶点创建mesh顶点序列和三角面
8. 重设uv坐标，将原texture贴到新的两部分mesh
#### 为实现此功能的主题，将从上面8个实现思路分博客进行介绍，博客分配如下：
[Unity Mesh实现图片切割（一）](https://yiyuan1130.github.io/unity/2019/11/13/slice_sprite_1.html)		-->		1，2	
[Unity Mesh实现图片切割（二）](https://yiyuan1130.github.io/unity/2019/11/13/slice_sprite_2.html)		-->		3，4，5
[Unity Mesh实现图片切割（三）](https://yiyuan1130.github.io/unity/2019/11/13/slice_sprite_3.html)		-->		6，7 	

注：本博客只将大体思路，不设计具体详细代码。其中一些算法不定时在其他博客中详细讲解。