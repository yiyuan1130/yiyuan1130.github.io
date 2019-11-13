---
layout: post
title:  "Unity Mesh实现图片切割（二）"
date:   2019-11-13 14:00:00 +0800
categories: Unity
---

# Unity Mesh实现图片切割（二）
在开始计算坐标点之前线建议声明```Line```类，提供一些复用函数，为计算提方便。
### 一、 计算画的线和图形线的交点
求交点坐标直观的数学方法，就是求出两直线的方程，两方程求解。
#### 1. 求直线方程
直线方程为```y = k * x + b```，通过线段的两个点```startPos```和```endPos```可求出```k```和```b```。
可得到公式：
```
k = (endPos.y - startPos.y) / (endPos.x - startPos.x); // 注意被除数为0的情况特殊处理
b = startPos.y - k * startPos.x;
```
#### 2. 求两直线交点

```
y = k1 * x + b1	-> 1
y = k2 * x + b2 -> 2
```
1方程乘k2，2方程乘k1，再相减可消元得到方程：
```
y = (k2 * b1 - k1 * b2) / (k2 - k1);
x = (y - b1) / k1;
```
但是需要注意的是：
1. 直线垂直于X轴的特殊处理，因为垂直于X轴的直线斜率k趋近于∞，没有截距b。
2. 两直线平行是没有交点的，平行的依据是：1.斜率k相同；2.或者两条直线都是垂直于X轴
通过画的线和图形的各个线段进行求交点，会的出有限个数的交点集
### 二、 将顶点和交点按照画的线分区域（在线上、线上方、线下方）得到两个新的点集
#### 1. 合并、去重
上一步得到的交点集为```intersectionPointsList```，图形顶点做标集为```vertices```，将两个数组合并为```allPoints```。
合并后要将allPoints中点去重，此操作是为了处理交点为顶点，坐标值相同被视为连个点。或者为了切割需求，将临近点视为同一点坐标值。
#### 2. 分区域
拿到画的线的方程，将```allPoints```中点分别跟直线对比，分为在线上、线上方、线下方。如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191113154138265.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1l1QW5IYW5kU29tZQ==,size_16,color_FFFFFF,t_70)
0,1,2,3是图形的顶点vertices，4,5是画的直线和图像边的交点，合并的```allPoints```的集合就是这6个点。画的直线把一个图像分成了两部分，上半部分```part1```是4,3,2,5顶点组成的四边形，下半部分```part2```是1,0,4,5组成的四边形。其中交点是连个图形公用的点，由此可分出连个新四边形的顶点，为画mesh作准备。
计算点与直线的关系方法：将6个点的x值带入直线方程，得出```targetY```，```targetY```和```point.y```比较：
```
point.y == targetY -> 点在线上（onList）
point.y < targetY  -> 点在线下方 (downList)
point.y > targetY  -> 点在线上方 (upList)
```
将点分为三类，上半部分的图形的顶点集合：```part1Vertices = upList + onList```，下半部分的图形的顶点集合：```part2Vertices = downList + onList```，得到两图形的所有顶点。
### 三、 分离出的点顺时针排序，为创建mesh作准备
由于mesh的兴致，需要顶点顺时针排序，才能在正面显示，所以需要将```part1Vertices```和```part2Vertices```按照顺时针排序，排序方法：
1. 先找到最左边的点，即：x最小，x相同找y最小的点
2. 其余点分别跟步骤1找到的点求正切值，按正切值从大到小排序。
##### 为什么要用正切值排序？
找到最左边的点，其余点与这个点的夹角范围是[-pi/2, pi/2]，在tan的三角函数中此范围内是单调递增的，由此可判断各店的关系。

---
下一遍博客将讲解创建mesh并修改uv进行贴图：
[Unity Mesh实现图片切割（三）](https://blog.csdn.net/YuAnHandSome/article/details/103051006)

全文链接： [Unity Mesh实现图片切割](https://blog.csdn.net/YuAnHandSome/article/details/103015287)