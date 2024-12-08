---
title: Git 常用命令
---

### 初始化一个新的 Git 仓库
``` bash
git init
```
### 设置用户名和电子邮件的配置信息
``` bash
git config --global user.name <your-name>
git config --global user.email <your-email>
```
### 克隆一个仓库
``` bash
git clone <repository-url>
```
### 将文件添加到暂存区
``` bash
git add <file>
```
### 将所有文件的更改添加到暂存区
``` bash
git add
```
### 查看未暂存的更改
``` bash
git diff
```
### 提交暂存区的更改
``` bash
git commit -m "message"
```
### 重置暂存区到上次提交的状态
``` bash
git reset
```
### 查看工作目录和暂存区的状态
``` bash
git status
```
### 从索引和工作目录中移除文件
``` bash
git rm ‹file>
```
### 列出提交历史
``` bash
git log
```
### 查看提交的元数据和内容更改
``` bash
git show <commit-hash>
```
### 列出所有本地分支
``` bash
git branch
```
### 创建一个新分支
``` bash
git branch <branch-name>
```
### 重命名当前分支
``` bash
git branch -m <new-branch-name>
```
### 删除一个分支
``` bash
git branch -d <branch-name>
```
### 切换到另一个分支
``` bash
git checkout <branch-name>
```
### 将指定分支合并到当前分支
``` bash
git merge <branch-name>
```
### 创建一个新的远程仓库连接
``` bash
git remote add <name> <repository-url>
```
### 移除与远程仓库的连接
``` bash
git remote remove <name>
```
### 更新与远程仓库的连接
``` bash
git remote set-url <name> <repository-url>
```
### 查看与远程仓库的连接
``` bash
git remote get-url <name>
```
### 推送已提交的更改到远程仓库
``` bash
git push <remote> <branch>
```
### 从远程仓库下载内容
``` bash
git pull <remote>
```
### 清理不必要的文件并优化本地仓库
``` bash
git gc
```
### 暂时移除未提交的更改并保存以供以后使用
``` bash
git stash
```
### 重新应用之前暂存的更改
``` bash
git stash apply
```
