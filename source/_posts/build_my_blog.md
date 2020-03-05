---
title: 搭建自己的博客
categories:
  - 杂记
tags: blog
copyright: false
reward: false
rating: false
related_posts: true
date: 2020-03-03 06:51:55
---

自己瞎折腾的博客

## 本地安装Hexo出现的一些问题


### 安装nodejs

安装了node后，执行npm run xxx的命令的时候，报错，提示如下：

```
/usr/bin/env: node: No such file or directory
```

创建一个软连接，如下：

```
ln -s /usr/bin/nodejs /usr/bin/node
```

运行'hexo s' 时出现问题：

```
SyntaxError: Unexpected token =

    at exports.runInThisContext (vm.js:53:16)
    at Module._compile (module.js:374:25)
    at Object.Module._extensions..js (module.js:417:10)
    at Module.load (module.js:344:32)
    at Function.Module._load (module.js:301:12)
    at Function.Module.runMain (module.js:442:10)
    at startup (node.js:136:18)

    at node.js:966:3
```

把nodejs升级一下就可以了，这里我们通过安装n模块来升级

```
sudo npm install -g n
sudo n stable
```

安装
```
npm install hexo-renderer-pug --save
npm install hexo-renderer-sass --save
```
时 error:
```
../src/sass_context_wrapper.h:8:26: fatal error: sass/context.h: No such file or directory
```
solve:
```
LIBSASS_EXT="no" npm install
```



### Hexo

安装

```
npm install -g hexo-cli
```
本地预览
``` bash
hexo server
```


生成静态文件
``` bash
hexo generate
```

部署到远程
``` bash
hexo deploy
```
更多: [Server](https://hexo.io/docs/server.html), [Generating](https://hexo.io/docs/generating.html),  [Deployment](https://hexo.io/docs/one-command-deployment.html)

### 插件

```
npm install hexo-admin --save 
```
该插件可以通过访问localhost:4000/admin来管理你的文章。
并且在可视化界面中修改文章内容
