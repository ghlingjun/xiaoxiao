---
title: 代码提交注释格式
date: 2018-03-14 10:42:04
tags: [git,svn]
---

每次提交，Commit message 格式如下：
```
<type>[<scope>]: <subject>
```
包含三部分：type（必需）、scope（可选）和subject（必需）
1. type
	`type`用于说明 commit 的类别，只允许使用下面7个标识。
	- feat：新功能（feature）
	- fix：修补bug
	- docs：文档（documentation）
	- style： 格式（不影响代码运行的变动）
	- refactor：重构（即不是新增功能，也不是修改bug的代码变动）
	- test：增加测试
	- chore：构建过程或辅助工具的变动
2. scope
	`scope`用于说明 commit 影响的范围，例如工作流模块、系统管理模块、统计分析模块等，根据项目不同而不同
3. subject
	`subject`是 commit 目的的简短描述，不超过50个字符。
	- 以动词开头，使用第一人称现在时，比如change，而不是changed或changes
	- 第一个字母小写
	- 结尾不加句号（.）
