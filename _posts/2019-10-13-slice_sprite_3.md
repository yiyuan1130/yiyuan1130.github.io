---
layout: post
title:  "Unity Mesh实现图片切割（三）"
date:   2019-11-13 15:00:00 +0800
categories: Unity
---

建议了解mesh原理，此篇博客不对mesh详解，值针对此功能做简单介绍以及思路引导。
### 一、 创建mesh
要想自己创建mesh并应用，需要用到```MeshRenderer```和```MeshFilter```组件，```Mesh```就是```MeshFilter```上的一个属性值。

若不考虑渲染图像，创建mesh需要声明mesh的顶点```vertices```和三角面```triangles```，顶点就是上篇博客最后求出来的排好序的点，三角面是任意三个点规定的三角形（必须是顺时针）的下标数组，如图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191113162438196.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1l1QW5IYW5kU29tZQ==,size_16,color_FFFFFF,t_70)
mesh的顶点```vertices```就是0,1,2,3排好序的点数组```Vector3[]```。
mesh的三角行就是 a 和 b，a和b的表达方式为```new int[] {0, 1, 2, 0, 2, 3};```，每三个顶点下标确定一个三角形。

代码创建如图网格mesh方式：
```C#
GameObject meshObj = new GameObject("MeshObj");
MeshRenderer meshRenderer = meshObj.AddComponent<MeshRenderer>();
MeshFilter meshFilter = meshObj.AddComponent<MeshFilter>();
Mesh mesh = new Mesh();
meshFilter.mesh = mesh;
Vector3[] meshVertices = new Vector3[]{p0, p1, p2, p3,};
int[] meshTriangles = new int[] {0, 1, 2, 0, 2, 3};
mesh.triangles = meshTriangles;
mesh.vertices = meshVertices;
```
由此就可以创建一个mesh了，如果要渲染需要赋值```MeshRenderer```的```material```属性。

### 二、 修改uv
##### 贴图UV和mesh的关系
可以将mesh理解为一个框架，给定了很多顶点就是vertices。经贴图理解为布，布的左下角是(0, 0)，将这个布按照自己规定的点钉到框架上，中间的像素会自己计算拉伸、位置等。这些自己规定的看就是UV（**是跟mesh的vertices长度相同的数组**）。

##### uv的设置方式
贴图的坐标系如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191113163649354.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1l1QW5IYW5kU29tZQ==,size_16,color_FFFFFF,t_70)
贴图的uv坐标是左下角为(0, 0)，右上角为(1, 1)的坐标系，设置uv坐标就是按照贴图坐标系，将mesh的顶点的x y值映射到[0, 1]区间内。
相当于**未切割前的整图左下角顶点**为贴图坐标系**原点**，其他顶点一次映射。一定要注意uv坐标数组长度适合vertices坐标数组长度相同。
通过```mesh.uv = meshUv;```可是这mesh的uv。

PS:另外如有需要，可以设置法线```mesh.normals = meshNormals;```

全文链接： [Unity Mesh实现图片切割](https://yiyuan1130.github.io/unity/2019/11/13/slice_sprite_start.html)