---
title: git常见命令总结
date: 2021-06-8 17:32:32
tags: git
categories: 学习笔记
---

## 常见命令

```
git clone <url>
git init 
git status 查看状态
git diff 查看变更内容
git pull 
git push 3 // master 分支
git push origin <branch> // 其他分支
git add . 1
git add <file>
git commit -m 'xxx' 2
git commit -m 'xxx -a // 新增所有
git branch <new branch>
git branch <branch> -d // 删除分支
git checkout 切换分支
git merge <branch>
git mv <file> <newfilename> 修改文件名字
git rm <file> // 删除文件
git log // 查看历史
git tag <tagname>
git checkout a.js // 撤销
git revert <commit> // 回滚 撤销指定的提交 有历史记录
git reset --hard <commit> // 撤销指定的提交 无历史记录
=> git push 'xx' --force

```

## 基本使用

```
(1). 基本使用
git pull 拉取并merge代码
git add .; //添加 暂存区
git commit -m 'zhu shi'// 提交到本地仓库并加上注释
git push origin master //往远程仓库推送代码
(2). 多人协作方式
git branch -a 查看所有的分支
git checkout -b aaa 创建新的分支aaa
git checkout aaa 切换到aaa分支
git push origin aaa 推送aaa 分支到远程仓库aaa分支
git push origin master:aaa 推送master 到远程的aaa 分支
git branch -d 删除一个分支
```

**vscode 安装 git history diff**