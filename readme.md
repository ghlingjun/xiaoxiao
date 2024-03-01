## Hexo Commands

### 创建一篇文章
创建一篇新的文章，如果没有指定`layout`，Hexo 会使用 _config.yml 中配置的`default_layout`，如果标题中包含空格，需要使用双引号将其引起来。
```
# 进入 hexo-site 目录
$ cd .../hexo-site
$ hexo new [layout] <title>
```
Hexo 有三种默认布局：post、page 和 draft。在创建者三种不同类型的文件时，它们将会被保存到不同的路径；而您自定义的其他布局和 post 相同，都将储存到 source/_posts 文件夹。

|布局	|路径|
| ---------------- | ------------------ |
|post	|source/_posts|
|page	|source|
|draft	|source/_drafts|

下面是一个 front matter 的示例：
```yaml
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
```
categories 示例中表示“工具”为父分类，“hexo”为子类，并不是和“工具”同级，而是子类。
tags 示例中表示“hexo”为标签。标签也可以多个，没有级别。

### 部署网站
```
$ hexo deploy
```
| Option             | Description                |
| ------------------ | -------------------------- |
| `-g`, `--generate` | Generate before deployment |

### 启动本地服务

```
$ hexo server
```
| Option           | Description                            |
| ---------------- | -------------------------------------- |
| `-p`, `--port`   | Override default port                  |
| `-s`, `--static` | Only serve static files                |
| `-l`, `--log`    | Enable logger. Override logger format. |

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
