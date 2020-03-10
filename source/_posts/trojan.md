---
title: trojan做点不一样的事
categories:
  - 杂记
tags: [blog,trojan，ubuntu]
copyright: false
reward: false
rating: false
related_posts: true
date: 2020-03-09 20:51:55
---

# trojan 安装和使用

最近墙越来越高了,开始的ssr,在后来的V2ray,没办法,干就完事了

折腾了好久终于搞好了,记录一下

## 服务端

### 搭建vps

个人用的 **digitalocean**
　

### 获取域名

用的 **namecheap**

### 开干
这里使用了脚本,参考一下链接

[一键脚本](https://www.johnrosen1.com/trojan/)



```bash
sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/johnrosen1/trojan-gfw-script/master/vps.sh)"

```
一路默认即可

## 客户端

如果是ubuntu18.04可以用这个
```bash
sudo add-apt-repository ppa:greaterfire/trojan
sudo apt-get install trojan -y
sudo nano /etc/trojan/config.json
```

我个人时候用的ubuntu16.04,客户端使用有点狗

要下两个东西

trojan-cli

```bash
wget https://github.com/atrandys/trojan/raw/master/trojan-cli.zip
```

trojan-1.14.1-linux-amd64.tar.xz
```bash
wget https://github.com/trojan-gfw/trojan/releases/download/v1.14.1/trojan-1.14.1-linux-amd64.tar.xz
```
将trojan-1.14.1-linux-amd64.tar.xz里面的克制向文件 **trojan** 放到trojan-cli文件下

```bash
./trojan -c config.json
```

本人运行时一直有个错误

     fatal: bind: Address already in use

说端口被占用．
一直以为是服务端的问题，最后试了下 iso 端是可以用的，说明是客户端的问题，默认 1080　端口

    lsof -i:1080
查看发现并没有问题

最后解决方法是　cofig.jaon 文件中将本地端口换了一个，就可以了

神奇
