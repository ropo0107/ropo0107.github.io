---
title: 【blog】 Hexo迁移到vps
categories:
  - 杂记
tags: [vps,blog,ubuntu]
copyright: false
reward: false
rating: false
related_posts: true
date: 2020-03-10 07:23:55
---

搭建完 **trojan** 后把域名和 **vps** 绑定后想把 **blog** 迁移到 **vps** 上

## 安装必要工具

 - 配置SSH
    避免每次登录都需要密码
    - 本地密钥获取
        ```
        cat ~/.ssh/id_rsa.pub
        ```
    - 服务端将密钥复制进去即可
        ```
        cd .ssh
        vim authorized_keys 
        ```

 - 首先需要将文件上传，我们选择 git ，然后对于网页展示我们使用久负盛名的 nginx。   
    在root目录下安装
    ```bash
    mkdir hexo.git
    cd hexo.git
    git init --bare 
    #  --bare是建立裸仓库
    ```
 - 然后来到/var/www文件夹下，我们的网页就会存放在这里的某一个文件夹下，这里我们创建一个文件夹hexo，然后赋予它操作权限，同时赋予自己权限来操作它。

    ```sh
    mkdir hexo
    chmod 0755 hexo
    chown root:root -R /var/www/hexo
    ```

## githook。

当我们将本地网页文件传上来之后，我们可能还要到服务器端操作一番，以能够将网页文件放到/var/www/hexo下展示。git自带一个功能，那就是只要有文件传输它就会执行某个脚本。那么我们怎么做呢？写个脚本就好了！

```sh
vim hexo.git/hooks/post-receive
```
将下面内容贴进去

```sh
#!/bin/bash

GIT_REPO=~/hexo.git                      # 触发 hook
TMP_GIT_CLONE=/tmp/hexo                  # 存在 /tmp 下
PUBLIC_WWW=/var/www/hexo                 # 展示网站的目录
rm -rf ${TMP_GIT_CLONE}                  # 删除之前内容
git clone $GIT_REPO $TMP_GIT_CLONE       # 将 Git 仓库上传的内容复制到/tmp
rm -rf ${PUBLIC_WWW}/*                   # 删除展示网站的目录的全部内容
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}  # 将/tmp所有内容复制到网站目录
```
## 配置nginx

正常应该是：
```
cd /etc/nginx/sites-available
vim default
```
因为之前安装过 **trojan** 用到了 **nginx** 所以我的

```
cd /etc/nginx/conf.d/trojan.conf
vim trojan.conf
```
我的只需要修改下面内容即可

        location / {
                root /usr/share/nginx/html/;
                        index index.html;
                }


将上面的内容替换

        location / {
                root  /var/www/hexo/;
                index  index.html index.htm;
        }

 - 这儿有个坑，看别的博主 **root  /var/www/hexo/;** 的博主，这句都没有最后的 **\/** ,我这儿是有问题的

完整的config文件

    server {
            listen 80;
            listen [::]:80;

            root /var/www/hexo; #坑的我好苦
            index index.html index.htm index.nginx-debian.html;

            server_name 你的域名;#如果没有的话就别填了QwQ
        
            location ~* ^.+\.(ico|gif|jpg|jpeg|png)$ {
                    root /var/www/hexo/;
                    access_log   off;
                    expires      1d;
            }
            location ~* ^.+\.(css|js|txt|xml|swf|wav)$ {
                    root /var/www/hexo;
                    access_log   off;
                    expires      10m;
            }
            location / {
                    root /var/www/hexo;
                    if (-f $request_filename) {
                            rewrite ^/(.*)$  /$1 break;
                    }
            }
    }


重启nginx即可
```bash
service nginx restart
```

在浏览器输入自己域名就ok了！！！


## 同时部署到多个仓库

在 **_config.yaml** 添加要部署的地址即可，前面的名字无所谓，冒号后面要空格.

```yaml
deploy:
  type: git
  repo: 
        vps: vpa_user@vps_ip:hexo.git 
        epository: git@github.com:username/username.github.io.git
  branch: master  
```
