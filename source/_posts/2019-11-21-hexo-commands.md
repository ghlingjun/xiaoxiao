---
title: hexo commands
comments: true
categories:
  - 工具
  - hexo
tags: 
  - hexo
date: 2019-11-21 20:43:50
updated: 2019-11-21 20:43:50
---

## Hexo Commands
最常用三个命令：
```
$ hexo new [layout] <title>
$ hexo server
$ hexo deploy -g
```
### 创建一篇文章
创建一篇新的文章，如果没有指定`layout`，Hexo 会使用 _config.yml 中配置的`default_layout`，如果标题中包含空格，需要使用双引号将其引起来。
```
# 进入 hexo-site 目录
$ hexo new [layout] <title>
```
Hexo 有三种默认布局：post、page 和 draft。在创建者三种不同类型的文件时，它们将会被保存到不同的路径；而您自定义的其他布局和 post 相同，都将储存到 source/_posts 文件夹。

|布局	|路径|
| ---------------- | ------------------ |
|post	|source/_posts|
|page	|source|
|draft	|source/_drafts|

### 启动本地服务
```
$ hexo server
```
| Option           | Description                            |
| ---------------- | -------------------------------------- |
| `-p`, `--port`   | Override default port                  |
| `-s`, `--static` | Only serve static files                |
| `-l`, `--log`    | Enable logger. Override logger format. |

### 部署网站
```
$ hexo deploy
```
| Option             | Description                |
| ------------------ | -------------------------- |
| `-g`, `--generate` | Generate before deployment |

### clean
```
$ hexo clean
```
Cleans the cache file (`db.json`) and generated files (`public`).

### 显示所有草稿
```
$ hexo --draft
```
Displays draft posts (stored in the `source/_drafts` folder).

### 发布草稿
```
$ hexo publish [layout] <filename>
```

### 代码生成
```
$ hexo generate
```
生成静态文件。

| 可选参数             | 描述                 |
| ---------------- | ------------------ |
| `-d`, `--deploy` | 生成完成后，同时发布         |
| `-w`, `--watch`  | Watch file changes |
