---
layout: post
title:  "Unity 自定义事件 AddListerner"
date:   2020-04-17 20:20:08 +0800
categories: Unity
---

## Unity 自定义事件 AddListerner

Unity中有的Button组件有onCick.AddListener属性，如果想要自己定义事件并能够像Button一样AddListener使用需要怎么做呢？
Unity中提供有UnityEvent类来用作事件的声明，下面直接放上代码，再一一说明。

CustomEventClass.cs
```csharp
/* 事件类型的声明类 */
using UnityEngine.Events;
using UnityEngine;

// 开始移动事件
public class StartMoveEvent : UnityEvent<GameObject, string> {}

// 移动中事件
public class OnMoveEvent : UnityEvent<GameObject, string> {}

// 移动结束事件
public class EndMoveEvent : UnityEvent<GameObject, string> {}
```
这个类主要是声明几个自定义的事件，可以按照个人喜好和代码风格来确定放在一个脚本或者是跟逻辑脚本放在一起。
自定义的类继承了 UnityEvent 就成为了一个Unity的事件，支持多参数，但至多只能是4个，具体API可以看 UnityEvent.cs 文件。

TestGameObject.cs
```csharp
/* 测试的Mono类 */
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
public class TestGameObject : MonoBehaviour {

	public StartMoveEvent startMoveEvent = new StartMoveEvent();
	public OnMoveEvent onMoveEvent = new OnMoveEvent();
	public EndMoveEvent endMoveEvent = new EndMoveEvent();

	static float startTime = 0f;

	void Awake(){
		startMoveEvent.AddListener((GameObject go, string str) => {
			Debug.Log("<color=yellow>Start Move, GameObject is -> " + "  string content is -> " + str + "</color>");
		});
		onMoveEvent.AddListener((GameObject go, string str) => {
			Debug.Log("<color=orange>On Move, GameObject is -> " + "  string content is -> " + str + "</color>");
		});
		endMoveEvent.AddListener((GameObject go, string str) => {
			Debug.Log("<color=green>End Move, GameObject is -> " + gameObject.name + "  string content is -> " + str + "</color>");
		});
	}

	void Start(){
		StartCoroutine(_CubeMove());
	}

	IEnumerator _CubeMove(){
		startMoveEvent.Invoke(gameObject, "开始移动");
		startTime = Time.time;
		while (Time.time - startTime < 2f)
		{
			transform.position += new Vector3(0.03f, 0, 0);
			yield return null;
			onMoveEvent.Invoke(gameObject, "移动中");
		}
		endMoveEvent.Invoke(gameObject, "移动结束");
	}
}
```
这个类是测试事件的类，用协程写了一个简单地移动。
同样的，在Awake中添加事件，参数也是和定义的事件类相匹配的。```xxx.Invoke()```是执行事件，因为是自定义的事件，具体执行的逻辑不通，需要在相应的地方执行。

接下来看下具体的效果（只是在对应时间输日志）：
![在这里插入图片描述](/styles/images/unityCustomEvent/customEvent.gif)