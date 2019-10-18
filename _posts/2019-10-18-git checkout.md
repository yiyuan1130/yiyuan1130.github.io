---
layout: post
title:  "Git 用法总结"
date:   2019-07-18 17:57:00 +0800
categories: Git
---
# 记录Git用法，持续更新 
## 更新时间 2019-10-18 EamonnLi


## 一、 checkout - 切换分支/检出文件
### 1. 切换分支
##### 从当前分支切换到本地已有目标分支
```
git chekcout local_branch
```
### 2. 切换并创建新分支
##### 从当前分支创建新本地分支并切换到此分支
```
git checkout -b new_branch
```
### 3. 检出本地其他分支文件
##### 从branch_name中检出file_name到当前分支
```
git checkout branch_name file_name
```
### 4. 检出本地提交的文件
##### 从commit_id的提交记录中检出file_name到当前
```
git checkout commit_id file_name
```

---

## 二、 stash - 贮存功能
##### 详情用法请见: [git stash 详细用法](https://yiyuan1130.github.io/git/2019/07/18/git-stash.html)

---

## 三、 log/show - 查看记录
### 1. 查看所有提交记录
##### 显示所有几率，包含以下信息：
1. commit_id
2. Author
3. Date
4. commit msg
```
git log
```

### 2. 显示提交的差异内容
```
git log -p
```

### 3. 显示最近n此提交，n是数字
```
git log -n
```

### 4. 显示提交记录修改的文件行数
```
git log --stat
```

### 5. 查看某个用户的提交记录
##### 用户名用双引号包起来
```
git log --author="EamonnLi"
```

### 6. 查看某次提交的记录
```
git show commit_id
```

### 7. 查看某次提交的指定文件
```
git show commit_id file_path
```

## 四、 add/commit 提交
### 1. 提交到本地指定并添加指定文件
支持多个文件，空格分割
```
git add file1 file2
```
### 2. 提交到本地所有文件
三种方式任意，不建议使用
```
git add .
git add --all
git add -A
```

### 3. 添加提价记录
```
git commit -m "xxx"
```

### 4. 日觉所有文件到本地并添加提交记录
两种方式任意
```
git commit -am "xxx"
git commit -a -m "xxx"
```

## 五、 status 查看本地状态
### 1. 查看本当前分支的文件状态
```
git status
```

### 2. 查看本当前分支的文件状态 简略模式
```
git status -s
```

