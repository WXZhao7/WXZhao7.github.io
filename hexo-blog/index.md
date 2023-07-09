# Hexo博客的构建



## 预备工作

### 服务器

存储端有多种方案，对比如下

| 方案 | 特色 |
| :---- | :----: |
| Github pages | 免费，但是国内的访问速度堪忧 |
| GithubPages + 域名 | [付费域名] |
| GithubPages + CodingPages + 域名 | [付费域名] |
| 云服务器 + 域名 | 需要付费购买服务器[付费域名] |
| FTP空间 | 学校免费提供,但是毕业需要搬迁 |

个人考量: 低成本域名可以拥有, github服务器足够可靠

软件安装

## 其他事项

### node版本过高导致的warning

```shell
$ hexo -s                              
(node:87224) Warning: Accessing non-existent property 'lineno' of module exports inside circular dependency
(Use `node --trace-warnings ...` to show where the warning was created)
(node:87224) Warning: Accessing non-existent property 'column' of module exports inside circular dependency
(node:87224) Warning: Accessing non-existent property 'filename' of module exports inside circular dependency
(node:87224) Warning: Accessing non-existent property 'lineno' of module exports inside circular dependency
(node:87224) Warning: Accessing non-existent property 'column' of module exports inside circular dependency
(node:87224) Warning: Accessing non-existent property 'filename' of module exports inside circular dependency
```

* 方案1:
node降级到12.xx版本
* 方案2:
在 package.json 里增加 resolutions 来覆盖版本定义

  ```json
  "resolutions": {
    "stylus": "^0.54.8"
  }
  ```

