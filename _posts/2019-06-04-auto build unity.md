---
layout: post
title:  "Unity后台自动打包工具，支持git拉取、分支"
date:   2019-06-04 20:03:01 +0800
categories: Unity
---

### 基于git更新自动化打包的思路
1. 处理git拉取，切换分支
2. unity提供打包，打assetbunle接口
3. 编写外部打包工具脚本

#### 1、处理git拉取，切换分支
使用git拉取分支无非就是调用git的命令，在此，python中提供了subprocess库可用来执行shell命令。除了使用subprocess.call()调用git命令，python还提供了[gitpython](https://pypi.org/project/GitPython/)库方便git的使用。因为git操作用到了拉取、切换分支、重置等操作，对于整体打包流程只需知道结果，所以引入operation（操作）的概念，将所有的git操作封装到operation中，python脚本如下：

```
# file name GitOperation.py
#coding=utf-8
#liyiyuan 2019-05-30
# git upgrade operation

import subprocess
import sys

project_path = sys.argv[1]
branch = sys.argv[2]

def git(cmd):
	out_put = subprocess.Popen('git -C {0} {1}'.format(project_path, cmd), shell=True, stdout=subprocess.PIPE).communicate()[0]
	return out_put.decode('utf-8')

def update_git(project_path, branch):
	# 清空本地分支所有修改
	git_info = git('status')
	print(git_info)
	if 'nothing to commit, working tree clean' not in git_info:
		git_info = git('reset --hard')
		print(git_info)
		git_info = git('clean -df')
		print(git_info)

	# 获取本地所有分支
	# git_info = g.branch()
	git_info = git('branch')
	print(git_info)
	all_branch = git_info.replace(' ', '').split('\n')
	currentBranch = '*' + branch
	if currentBranch not in all_branch:
		# 当前分支不是此分支 判断本地是否有此分支
		if branch not in all_branch:
			git_info = git('branch -r')
			print(git_info)
			all_origin_branch = git_info.replace(' ', '').split('\n')
			origin_branch = 'origin/' + branch
			if origin_branch not in all_origin_branch:
				return 0
			else:
				git_info = git('checkout -b {0} {1}'.format(branch, origin_branch))
				print(git_info)
				git_info = git('branch')
				print(git_info)
				return 1
		else:
			# 本地有此分支，切换至此分支 拉取
			git_info = git('checkout {0}'.format(branch))
			print(git_info)
			git_info = git('pull origin {0}'.format(branch))
			print(git_info)
			git_info = git('branch')
			print(git_info)
			return 1
	else:
		# 拉取最新
		git_info = git('pull origin {0}'.format(branch))
		print(git_info)
		git_info = git('branch')
		print(git_info)
		return 1
	

if __name__ == '__main__':
	print('--------------------------------- git log start ---------------------------------')
	result = update_git(project_path, branch)
	print('---------------------------------  git log end  ---------------------------------')
	sys.exit(result)

```
#### 2、Unity 提供打包接口
做过相关自动化打包共具的小伙伴应该都知道Unity提供的MenuItem类，可以将接口暴露在Unity界面的菜单栏中，通过选中点击的方式执行函数，另外Unity也支持命令行，可以通过shell活着批处理来调用制定类中的函数，可以参照[官方的UnityCommandLine](https://docs.unity3d.com/Manual/CommandLineArguments.html)来了解。以下是Unity的打包函数例子：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;
using System;

public class AutoBuildTools {

	/// <summary>
	/// 获取参数
	/// </summary>
	/// <returns> 参数值 </returns>
	/// <param name="indexFromEnd"> 从后往前数第几个参数 </param>
	static string GetParam(int indexFromEnd){
		string[] args = Environment.GetCommandLineArgs();
		return args [args.Length - 1];
	}

	public static void BuildPackage(){
		string platform = GetParam (2);
		string buildPath = GetParam (1);
		Debug.LogFormat ("BuildPackage platfotm is {0}", platform);
		if (platform == "iOS") {
			BuildMenuItem.BuildXcode ();
		} else if (platform == "Android") {
			BuildMenuItem.BuildPackage ();
		}
	}

	public static void BuildAssetBundle(){
		string platform = GetParam (1);
		Debug.LogFormat ("BuildPackage platfotm is {0}", platform);
		if (platform == "iOS") {
			BuildMenuItem.BuildAssetBundle_iOS ();
		} else if (platform == "Android") {
			BuildMenuItem.BuildAssetBundle_Android ();
		}
	}

}

public class BuildMenuItem{

	[MenuItem("AutoBuildTools/BuildAssetBundle/iOS")]
	public static void BuildAssetBundle_iOS(string buildPath){
		Debug.LogFormat ("AutoBuildTools/BuildAssetBundle/iOS, buildPath is {0}", buildPath);
	}

	[MenuItem("AutoBuildTools/BuildAssetBundle/Android")]
	public static void BuildAssetBundle_Android(string buildPath){
		Debug.LogFormat ("AutoBuildTools/BuildAssetBundle/Android, buildPath is {0}", buildPath);
	}

	[MenuItem("AutoBuildTools/BuildPackage")]
	public static void BuildPackage(){
		Debug.Log ("AutoBuildTools/BuildPackage");
	}


	[MenuItem("AutoBuildTools/BuildXcode")]
	public static void BuildXcode(){
		Debug.Log ("AutoBuildTools/BuildXcode");
	}

}
```
#### 3、编写外部打包工具脚本
外部打包工具其实就是将前两步串接起来，根据需求编写不同的shell活着bat逻辑，来调用不同的Unity提供的打包函数。直接上脚本

BuildAssetBundle.py
```
#coding=utf-8
#liyiyuan 2019-06-04

import sys
import datetime
import subprocess

BRANCH = sys.argv[1]
PLATFORM = sys.argv[2]

UNITYPATH = '/Applications/Unity/Unity.app/Contents/MacOS/Unity'
GITOPERATIONPATH = '/Users/liyiyuan/Projects/JenkinsScripts/git/GitOperation.py'
PROJECTPATH = '/Users/liyiyuan/Priject/Unity/#PLATFORM#/Test'


star_time = datetime.datetime.now()
def log_complete():
	end_time = datetime.datetime.now()
	print('【INFO】build complete, duration : ', (end_time - star_time))


if __name__ == '__main__':
	if PLATFORM == 'iOS':
		print('【INFO】start ios build asset bundle')
	elif PLATFORM == 'Android':
		print('【INFO】start android build asset bundle')
	else:
		print('【ERROR】other error')

	PROJECTPATH = PROJECTPATH.replace('#PLATFORM#', PLATFORM)

	update_git_result = subprocess.call(f'python {GITOPERATIONPATH} {PROJECTPATH} {BRANCH}', shell=True)

	if update_git_result == 1:
		print('------------- upgrade success')
		# shell 命令行构成
		# [unity path] -quit -projectpath [project path] -executeMethod [class.method] [platfotm]
		subprocess.call('{0} -quit -projectPath {1} -executeMethod AutoBuildTools.BuildAssetBundle {2}'.format(UNITYPATH, PROJECTPATH, PLATFORM), shell=True)

	elif update_git_result == 0:
		raise SystemExit('【ERROR】git upgrade error !')

	log_complete()
```

BuildPackageOrXcode.py

```
#coding=utf-8
#liyiyuan 2019-06-04

import sys
import datetime
import subprocess

BRANCH = sys.argv[1]
PLATFORM = sys.argv[2]

UNITYPATH = '/Applications/Unity/Unity.app/Contents/MacOS/Unity'
GITOPERATIONPATH = '/Users/liyiyuan/Projects/JenkinsScripts/git/GitOperation.py'
PROJECTPATH = '/Users/liyiyuan/Priject/Unity/#PLATFORM#/Test'
BUILDPATH = '/Users/liyiyuan/Priject/Unity/Build/#PLATFORM#'


star_time = datetime.datetime.now()
def log_complete():
	end_time = datetime.datetime.now()
	print('【INFO】build complete, duration : ', (end_time - star_time))


if __name__ == '__main__':
	if PLATFORM == 'iOS':
		print('【INFO】start ios build asset bundle')
	elif PLATFORM == 'Android':
		print('【INFO】start android build asset bundle')
	else:
		print('【ERROR】other error')

	PROJECTPATH = PROJECTPATH.replace('#PLATFORM#', PLATFORM)
	BUILDPATH = BUILDPATH.replace('#PLATFORM#', PLATFORM)

	update_git_result = subprocess.call(f'python {GITOPERATIONPATH} {PROJECTPATH} {BRANCH}', shell=True)

	if update_git_result == 1:
		print('------------- upgrade success')
		# shell 命令行构成
		# [unity path] -quit -projectpath [project path] -executeMethod [class.method] [platfotm] [build path]
		subprocess.call('{0} -quit -projectPath {1} -executeMethod AutoBuildTools.BuildPackage {2} {3}'.format(UNITYPATH, PROJECTPATH, PLATFORM, BUILDPATH), shell=True)

	elif update_git_result == 0:
		raise SystemExit('【ERROR】git upgrade error !')

	log_complete()
```

