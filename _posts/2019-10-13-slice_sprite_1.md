---
layout: post
title:  "Unity Mesh实现图片切割（一）"
date:   2019-11-13 13:00:00 +0800
categories: Unity
---

### 一、 PolygonCollider2D获取顶点和线段
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191111192604301.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1l1QW5IYW5kU29tZQ==,size_16,color_FFFFFF,t_70)
PolygonCollider2D中有points属性，对应和图形的顶点，PolygonCollider2D的顶点可以手动添加或者删除，右键Element X可选择删除这个点。由于Unity
中所有图片都是连个三角面组成的矩形，在此需要手动拖动点勾勒出图片的有效切割范围。points属性的点顺序如图逆时针从Element0到Element3。

#### 1. 顶点的获取方式
```C#
// 通过Collider获取顶点的世界坐标，根据gameObject的缩放和位置重新计算
Vector2[] GetVerticesFromPolygonCollider(){
	SpriteRenderer sr = gameObject.GetComponent<SpriteRenderer>();
	Sprite sprite = sr.sprite;
	PolygonCollider2D collider = gameObject.GetComponent<PolygonCollider2D>();
	Vector2[] vertices = collider.points;
	for (int i = 0; i < vertices.Length; i++)
	{
		Vector2 ver = vertices[i];
		// 根据缩放重算顶点坐标
		ver = new Vector2(ver.x * transform.localScale.x, ver.y * transform.localScale.y);
		// 根据位置重算顶点坐标
		ver = new Vector2(ver.x + transform.position.x, ver.y + transform.position.y);
		vertices[i] = ver;
	}
	return vertices;
}
```
#### 2. 线段的获取方式
通过两个顶点算出线段的直线方程存放到```lines```的```List<Line>```中。
Line类中需要有以下几个属性、方法，为接下来运算做准备：
	1. 起点坐标
	2. 终点坐标
	3. 计算直线方程参数 k b（两点确定直线方程，y = kx + b）
	4. 获取两条线段的交点（求直线交点，再判断是否再线段上）
	5. 根据直线将点集分区域（分三部分：在线上、线的上方、线的下方 ）
### 二、 LineRenderer画线
画的线也属于直线，应记录起始点，在画线结束时候生成Line的实例。
实现画线的功能：
```C#
LineRenderer lr;
Vector2 startPos;
Vector2 endPos;
void DrawLine(){
	if (startPos == null || endPos == null)
		return;
	points = new Vector3[2]{startPos, endPos};		
	if (lr){
		lr.startWidth = lineWidth; 
		lr.endWidth = lineWidth;
		lr.useWorldSpace = false;
		lr.sortingLayerID = sortingLayer;
		lr.sortingOrder = orderInLayer;
		lr.positionCount = points.Length;
		lr.material = lineMaterial;
		lr.SetPositions (points);
	}
}
```
在Update中实时更新endPos值，并实时画线。注意坐标转换为：
```
Camera.main.ScreenToWorldPoint(new Vector3(Input.mousePosition.x, Input.mousePosition.y, 10))
```
---
下一遍博客将讲解交点坐标的求解以及为创建新的mesh顶点坐标的预处理：
[Unity Mesh实现图片切割（二）](https://yiyuan1130.github.io/unity/2019/11/13/slice_sprite_2.html)

全文链接： [Unity Mesh实现图片切割](https://yiyuan1130.github.io/unity/2019/11/13/slice_sprite_start.html)
