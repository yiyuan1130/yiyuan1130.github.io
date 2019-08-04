---
layout: post
title:  "Unity编辑器扩展-右键创建GameObject或者Asset"
date:   2019-05-17 20:03:01 +0800
categories: Unity
---

每个项目中都会自己项目独有的Gameobject或者Asset
1. 比如一个Button选中效果是一个Animate，每次都需要手动拖拽，为提高开发效率，可以直接在Hierarchy面板右键->UI->ButtonWithAnimate来直接创建出绑定好动画的Button
这样的做法可以大大减少拖拽浪费的时间。
2. 很多游戏中用到Lua，Unity中没有Lua的资源格式，需要编写工具来创建，可以直接在Asset面板中右键->Create->Lua

代码如下
1. 在Hierarchy面板创建GameObject
```C#
[MenuItem("GameObject/UI/My UI")]
public static void CreateObjInHierarchy()
{
    // 具体逻辑
}
```
实现效果
![在这里插入图片描述](/styles/images/editor create with mouse/editor_create_gameobject.png)

2. 在Asset面板下创建资源
```C#
[MenuItem("Assets/Create/My Asset")]
public static void CreateObjInAsset()
{
    // 具体逻辑
}
```
![在这里插入图片描述](/styles/images/editor create with mouse/editor_create_asset.png)