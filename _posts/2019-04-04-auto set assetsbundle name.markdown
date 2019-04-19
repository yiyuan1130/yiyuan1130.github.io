---
layout: post
title:  "Unity AssetBundle 自动设置 assetBundle名"
date:   2019-04-04 17:17:41 +0800
categories: Unity
---

做项目时候有需求模块化更新，模块化的打bundle，作为程序开发人员，一次拿来多个模块的资源包，需要批量将资源包更改assetBundleName。

如图
![在这里插入图片描述](/styles/images/auto set assetsbundle name/floders.png)
例如：在BundleFloders目录下，有很多以文件夹为单位的资源包，需要全部设置assetBundleName
可以写一个工具一键自动生成对应的assetBundleName

代码如下：

```C#
using UnityEditor;
using UnityEngine;
using System.IO;

public class Tools {

	[MenuItem("Tools/AutoSet AssetBundleName")]
	// 获取目标文件夹目录
	public static void AutoSetBundleName(){
		string path = Path.Combine (Application.dataPath, "SetBundNameTest/BundleFloders");
		DirectoryInfo dir_info = new DirectoryInfo (path);
		// 获取所有子文件夹
		DirectoryInfo[] dir_arr = dir_info.GetDirectories ();

		for (int i = 0; i < dir_arr.Length; i++) {
			DirectoryInfo current_dir = dir_arr [i];

			string dir_name = current_dir.Name;
			// 通过子文件夹名拼接成 assetsbundle 名
			string assetbundle_name = string.Format ("auto_set_{0}", dir_name.ToLower());

			string dir_path = current_dir.FullName;
			string asset_path = dir_path.Replace (Application.dataPath, "Assets");
			// 通过在工程内的路径获取 AssetImporter
			AssetImporter ai = AssetImporter.GetAtPath(asset_path);

			ai.assetBundleName = assetbundle_name;
			// 也可以修改 AssetImporter 中 assetBundleVariant 属性
			ai.assetBundleVariant = "variant";
		}
	}
}

```
工具完成后会在MenuItem自定义目录中显示
![在这里插入图片描述](/styles/images/auto set assetsbundle name/tools.png)
点击AutoSet AssetBundleName 后，会在动将BundleFloders目录下所有文件夹设置assetBundleName
![在这里插入图片描述](/styles/images/auto set assetsbundle name/assetbundlenames.png)
