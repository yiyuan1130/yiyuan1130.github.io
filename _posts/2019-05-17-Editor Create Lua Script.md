---
layout: post
title:  "Unity编辑器扩展-创建Lua脚本"
date:   2019-05-17 20:03:01 +0800
categories: Unity
---
为了能够更新游戏逻辑，很多Unity项目采用Lua脚本语言，但是本地Unity并没有支持Lua文件的创建，此文章分享一下扩展Unity编辑器创建Lua文件。直接上代码：
1. 创建Lua文件的C#逻辑
```C#
using UnityEngine;
using UnityEditor;
using System;
using System.IO;
using System.Text;
using UnityEditor.ProjectWindowCallback;
using System.Text.RegularExpressions;

public class CreateLuaScriptTools {
	[MenuItem("Assets/Create/Lua Sctipt", false, 80)]
	public static void CreatNewLua() {
		int instanceId = 0;
		EndNameEditAction endNameEditAction = ScriptableObject.CreateInstance<CreateLuaAsset> ();
		string pathName = Path.Combine (GetSelectedPathOrFallback(), "NewLuaScript.lua");
		Texture2D texture2D = null;
		string resourceFile = "Assets/CreateLuaScript/TemplateLua.lua"; // 模板文件
		ProjectWindowUtil.StartNameEditingIfProjectWindowExists (instanceId, endNameEditAction, pathName, texture2D, resourceFile);
	}

	public static string GetSelectedPathOrFallback() {
		string path = "Assets";
		foreach (UnityEngine.Object obj in Selection.GetFiltered(typeof(UnityEngine.Object), SelectionMode.Assets)) {
			path = AssetDatabase.GetAssetPath(obj);
			if (!string.IsNullOrEmpty(path)&&File.Exists(path)) {
				path = Path.GetDirectoryName(path);
				break;
			}
		}
		return path;
	}

}

class CreateLuaAsset:EndNameEditAction{
	public override void Action(int instanceId, string pathName, string resourceFile) {
		UnityEngine.Object o = CreateScriptAssetFromTemplate(pathName, resourceFile);
		ProjectWindowUtil.ShowCreatedAsset(o);
	}
	internal static UnityEngine.Object CreateScriptAssetFromTemplate(string pathName, string resourceFile) {
		string fullPath = Path.GetFullPath(pathName);
		StreamReader streamReader = new StreamReader(resourceFile);
		string text = streamReader.ReadToEnd();
		streamReader.Close();
		string fileNameWithoutExtension = Path.GetFileNameWithoutExtension(pathName);
		text = Regex.Replace(text, "@ScriptName@", fileNameWithoutExtension);

		bool encoderShouldEmitUTF8Identifier = true;
		bool throwOnInvalidBytes = false;
		UTF8Encoding encoding = new UTF8Encoding(encoderShouldEmitUTF8Identifier, throwOnInvalidBytes);
		bool append = false;
		StreamWriter streamWriter = new StreamWriter(fullPath, append, encoding);
		streamWriter.Write(text);
		streamWriter.Close();
		AssetDatabase.ImportAsset(pathName);
		return AssetDatabase.LoadAssetAtPath(pathName,typeof(UnityEngine.Object));
	}
}
```

2. Lua模板
```
local @ScriptName@ = class(nil, "@ScriptName@")

function @ScriptName@:init()

end

function @ScriptName@:Awake()

end

function @ScriptName@:Start()

end

function @ScriptName@:OnEnable()

end

function @ScriptName@:Update()

end

function @ScriptName@:OnDisable()

end

function @ScriptName@:OnDestroy()

end

return @ScriptName@
```