---
layout: post
title:  "让调试更方便：Unity-Logs-Viewer"
date:   2019-10-05 20:03:01 +0800
categories: Unity
---
## 再也不用为真机调试发愁！！
项目开发时候有些调试需要在真机才能看到运行结果，例如：第三方支付、登陆、分享等功能。需要在真机上查看日志，每次插线调试很不方便，unity的AssetsStore终有一款免费的调试工具：LogViewer，此文章推荐大家接入LogViewer方便调试。
### 1.  导入工具
AssestStore 搜索 LogViewer，导入Unity。
![在这里插入图片描述](/styles/images/unity_log_viewer/log_viewer.png)
### 2. 创建LogViewer GameObject
导出LogViewer后会在选择Reporter/create创建LogViewer的GameObject，会自动生成在场景中，名为Reporter。![在这里插入图片描述](/styles/images/unity_log_viewer/log_viewer_create.png)
**注意：项目有多个场景，需要在入口场景创建，会跟在DontDestroyOnLoad中跟随整个项目的进行。**
此时就可以愉快的使用了：屏幕使用手势画圈，调出界面。

### 3. 扩展
#### 3.1 手势设置
在Reporter上的Reporter组件中名为NumOfCircleToShow属性，作用为：手势画圈个数调起界面。
#### 3.2 启用/禁用
/Assets/Unity-Logs-Viewer/Reporter/Reporter.cs文件中，大概在1780行左右，有如下代码：
```
void Update()
	{
		fpsText = fps.ToString("0.000");
		gcTotalMemory = (((float)System.GC.GetTotalMemory(false)) / 1024 / 1024);
		//addSample();

#if UNITY_CHANGE3
		int sceneIndex = SceneManager.GetActiveScene().buildIndex ;
		if( sceneIndex != -1 && string.IsNullOrEmpty( scenes[sceneIndex] ))
			scenes[ SceneManager.GetActiveScene().buildIndex ] = SceneManager.GetActiveScene().name ;
#else
		int sceneIndex = Application.loadedLevel;
		if (sceneIndex != -1 && string.IsNullOrEmpty(scenes[Application.loadedLevel]))
			scenes[Application.loadedLevel] = Application.loadedLevelName;
#endif

		calculateStartIndex();
		// 这里 增加自定义判断条件
		if (!show && isGestureDone()) {
			doShow();
		}


		if (threadedLogs.Count > 0) {
			lock (threadedLogs) {
				for (int i = 0; i < threadedLogs.Count; i++) {
					Log l = threadedLogs[i];
					AddLog(l.condition, l.stacktrace, (LogType)l.logType);
				}
				threadedLogs.Clear();
			}
		}
```
在你 ```doShow()```函数调用时，可以修改代码自定义增加判断条件，可根据release版本还是beta版本等。

#### 3.3 设备信息存储文件
LogViewer会在每次初始化时候存储一个本地设备信息文件：build_info.txt，不同设备会做修改，协同开发会出现此文件冲突，需要在SVN或者Git中将此文件过滤，或者修改代码解决此文件。
在/Assets/Unity-Logs-Viewer/Reporter/Editor/ReporterEditor.cs文件有如下代码：
```
public class ReporterModificationProcessor : UnityEditor.AssetModificationProcessor
{
	[InitializeOnLoad]
	public class BuildInfo
	{
		static BuildInfo()
		{
			EditorApplication.update += Update;
		}

		static bool isCompiling = true;
		static void Update()
		{
			if (!EditorApplication.isCompiling && isCompiling) {
				//Debug.Log("Finish Compile");
				if (!Directory.Exists(Application.dataPath + "/StreamingAssets")) {
					Directory.CreateDirectory(Application.dataPath + "/StreamingAssets");
				}
				string info_path = Application.dataPath + "/StreamingAssets/build_info.txt";
				StreamWriter build_info = new StreamWriter(info_path);
				build_info.Write("Build from " + SystemInfo.deviceName + " at " + System.DateTime.Now.ToString());
				build_info.Close();
			}

			isCompiling = EditorApplication.isCompiling;
		}
	}
}
```
```Updata```函数中有写入并保存本地功能，可将此部分代码进行修改，避免协同开发遇到冲突。

#### 另外有更多扩展功能可以按照需求自己修改代码，例如：上传错误，警告等信息，Unity-Logs-Viewer的Reporter.cs中有名为Log的类，可以获取Unity抛出的异常，自己探索吧。

