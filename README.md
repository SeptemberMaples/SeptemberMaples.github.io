### [hexo 常用指令](https://hexo.io/zh-cn/docs/commands)

#### init
```bash
hexo init [folder]
```

#### new
``` bash
hexo new [layout] <title>
```

新建一篇文章。如果没有设置 layout 的话，默认使用 _config.yml 中的 default_layout 参数代替。如果标题包含空格的话，请使用引号括起来。

``` bash
hexo new "post title with whitespace"
```

### generate  生成静态文件
``` bash
hexo generate    // 简写  hexo g
```

### deploy 部署网站
```bash
hexo deploy   简写 hexo d
```
-g, --generate	部署之前预先生成静态文件
```bash
hexo d -g
```

### publish 发表草稿
```bash
hexo publish [layout] <filename>
```

### 显示草稿
```bash
hexo server --draft
```
显示 `source/_drafts` 文件夹中的草稿文章

### server
```bash
hexo server
```
启动服务器。默认情况下，访问网址为： http://localhost:4000/


---
[Markdonw常用基本语法](https://www.jianshu.com/p/191d1e21f7ed)