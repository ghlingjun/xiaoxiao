---
layout: default
title: git 命令行指引
date: 2017-09-15 10:09:09
categories:
  - 工具
tags: [git]
---
总结的一些不常用的 git 操作命令

# Git 全局配置
```
git config --global user.name "lingjun"
git config --global user.email "lingjun@live.cn"
```

# git core.autocrlf 配置说明
基于 git 服务在 linux 服务器，以及应用运行在 linux 服务器上的假设，git 换行符配置如下：
Windows 系统下开发配置如下，Git 可以在你提交时自动地把行结束符 CRLF 转换成LF，而在签出代码时把LF转换成CRLF
```
git config --global core.autocrlf true
```
Mac或者Linux下开发，则配置如下，Git 在提交时把 CRLF 转换成 LF，签出时不转换
```
git config --global core.autocrlf input
```
拒绝提交包含混合换行符的文件
```
git config --global core.safecrlf true
```

# 创建一个新的仓库
```
git clone git@code.aliyun.com:lingjun/dolphin.git
cd dolphin
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

# Existing folder or Git repository
```
cd existing_folder
git init
git remote add origin git@code.aliyun.com:lingjun/dolphin.git
git add .
git commit
git push -u origin master
```

# Ignore files
```
# in directory: /Users/ethan/workspace/jeecg
git rm --cached --force src/main/webapp/webpage/content/plug-in/ueditor/jsp/config.properties
git rm --cached --force src/main/webapp/plug-in/ueditor/jsp/config.properties

# git ls-files --others --exclude-from=.git/info/exclude
# Lines that start with '#' are comments.
# For a project mostly in C, the following would be a good set of
# exclude patterns (uncomment them if you want to use them):
# *.[oa]
# *~
```

# relocate repository
```
git remote set-url origin url
```
或者
```
git remote rm origin
git remote add origin git@code.aliyun.com:lingjun/jee-octopus.git
```

# 切换分支
```
git branch -a
git checkout master_herbinate
```

# Keep your fork synced
1. See the current configured remote repository for your fork：
```
git remote -v
```
2. Add remote upstream
```
git remote add upstream https://github.com/thinkgem/jeesite.git
```
3. Fetch the branches and their respective commits from the upstream repository
```
git fetch upstream
```
4. 确保当前分支是 master, 如果不是，切换至 master
```
git branch -a
```
5. Merge the changes from upstream/master into your local master branch
```
git merge upstream/master
```

# Revert file
```
git rm --cached -f -- naisen/src/main/webapp/WEB-INF/pages/crm/customer/add.jsp
git checkout HEAD -- naisen/src/main/webapp/WEB-INF/pages/crm/customer/add.jsp
```

# 导出提交日志
```
git log --date=iso --pretty=format:'"%h", "%an", "%ad", "%s"' >> log.csv
```

更多

[git - home](https://git-scm.com/about)
[git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
