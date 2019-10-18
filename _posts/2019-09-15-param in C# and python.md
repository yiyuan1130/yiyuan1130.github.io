---
layout: post
title:  "UnityC#、Python 参数传递"
date:   2019-09-15 19:03:01 +0800
categories: Algorithm
---
### Unity的C#参数传递
在使用Unity命令行时候用到参数时候，需要将参数从外部传递，在C#中读取。
例如一个shell调用Unity的命令：

```
# [unity path] -quit -projectpath [project path] -executeMethod [class.method] [platfotm]
```
其中```[platform]```就是传递的参数，需要在C#中接收，接收方如下：
```
string[] args = Environment.GetCommandLineArgs();
string platform = args [args.Length - 1];
```
args是shell命令以空格分割的数组，最后一个是需要传递的参数，所以取index为args.Length - 1的就是platform参数，如果参数多，就依次类推，取倒数第二个、倒数第三个...

### Python脚本的参数传递
调用python脚本直接使用shell或bat命令可以:
```
python xxx/xxx/xxx/test.python
```
需要传递参数就变为:
```
python xxx/xxx/xxx/test.python param1 param2
```
只需要在python文件路径后面加上参数即可，用空格分割。
在python脚本中接收参数需要用：
```
import sys
param1 = sys.argv[1]
param2 = sys.argv[2]
```
```sys.argv```是将shell命令中除去‘python’后以空格分割的数组，index=0是python脚本的路径，index=1之后的是对应为的参数。

