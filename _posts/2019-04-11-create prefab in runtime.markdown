---
layout: post
title:  "Unity运行时（RunTime/代码）创建预设体"
date:   2019-04-11 16:22:38 +0800
categories: Unity
---

有事需要将SceneA中的一个对象在SceneB中使用，切换场景时候会将A场景对象全部卸载，此时就需要在切场景时候创建一个预设体，然后在SceneB中使用，动态的使用代码创建预设体，需要用到Unity的一个类PrefabUtility

代码如下
```C#
using UnityEngine;
using System.Collections;
using UnityEditor;
using System.IO;

public class CreatePrefabInRunTime : MonoBehaviour {
	void RunTimeCreatePrefab(GameObject obj, string prefabName) 
	{
		string path = "Assets/Resources/CreatePrefabInRunTime/Prefabs/" + prefabName + ".prefab";
		if (File.Exists (path)) {
			Debug.Log ("is have");
		}

		// 如果不做路径判断，则会覆盖原路径中的文件
		PrefabUtility.CreatePrefab (path, obj);
	}

	void OnGUI(){
		if (GUILayout.Button ("create prefab")) {
			GameObject obj = GameObject.Find ("TestPanel");
			RunTimeCreatePrefab (obj, "test_panel_prefab");
		}
	}
}
```

有时会有需求：根据策划的配置来动态生成预设体，可以编写一个编辑器工具来生成
```C#
[MenuItem("Tools/CreatePrefab/InEditor")]
public static void RunTimeCreatePrefab() 
{
	Debug.Log ("auto create a prefab");
	string prefabName = "testPrefab";
	GameObject obj = new GameObject(prefabName);
	obj.transform.position = Vector3.zero;
	Image obj_image = obj.AddComponent <Image>();
	obj_image.color = Color.red;


	GameObject obj_child1 = new GameObject ("child1");
	obj_child1.transform.SetParent (obj.transform);
	obj_child1.transform.localScale = Vector3.one * 0.5f;
	Image obj_child1_image = obj_child1.AddComponent<Image> ();
	obj_child1_image.color = Color.green;

	string path = "Assets/Resources/CreatePrefabInRunTime/Prefabs/" + prefabName + ".prefab";
	if (File.Exists (path)) {
		Debug.Log ("is have");
	}

	// 如果不做路径判断，则会覆盖原路径中的文件
	PrefabUtility.CreatePrefab (path, obj);
}
```




