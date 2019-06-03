## Hexo Commands

### 创建一篇文章

创建一篇新的文章，如果没有指定`layout`，Hexo 会使用 _config.yml 中配置的`default_layout`，如果标题中包含空格，需要使用双引号将其引起来。

```
# 进入 hexo-site 目录
$ cd .../hexo-site
$ hexo new [layout] <title>
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

### 发布文章

```
$ hexo publish [layout] <filename>
```

发布一篇草稿。

### 启动服务

```
$ hexo server
```

启动本地服务，默认服务地址是： `http://localhost:4000/`.

| Option           | Description                            |
| ---------------- | -------------------------------------- |
| `-p`, `--port`   | Override default port                  |
| `-s`, `--static` | Only serve static files                |
| `-l`, `--log`    | Enable logger. Override logger format. |

### 部署

```
$ hexo deploy
```

部署你的网站。

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
