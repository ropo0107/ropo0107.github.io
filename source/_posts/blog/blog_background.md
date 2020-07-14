---
title: 【blog】 Hexo修改背景图片及主题透明
categories:
  - 杂记
tags: blog
copyright: false
reward: false
rating: false
related_posts: true
date: 2020-03-15 06:51:55
---

# 背景图片的更改
## 背景背景图片添加

- 首先将已选定的背景图片放到博客根目录下的\source\images下

        Blog\source\images\background.jpg

- 其次，打开配置文      Blog\themes\next\source\css\_custom\custom.styl：        

        // Custom styles.
        body { 
        background-image: url(/images/background.jpg);
        background-attachment: fixed;
        background-repeat: no-repeat;
        background-size: cover；
        }


## 界面显示优化
在上面打开的配置文件body{}中继续添加以下配置；（Blog\themes\next\source\css\_custom\custom.styl)

    //background config
    body { 
        background-image: url(/images/bc.jpg);
        background-attachment: fixed;
        background-repeat: no-repeat;
        background-size: cover；
        
        
        //改变背景色和透明度
        .main-inner {
            background: #fff;
            padding: 25px;
            opacity: 0.75;
            border-radius: 10px;
        }
        
        //修改头部导航栏显示宽度，透明度，位置等
        #header {
            padding: 5px 25px;
            margin: 0 auto;
            margin-top:15px;
            width: 700px;
            opacity: 0.8;
            border-radius: 10px;
            
        }
        
        //修改底部展示信息显示宽度，透明度，位置等
        #footer {
            padding: 5px 25px;
            position: relative;
            margin: 0 auto;
            margin-bottom: 5px;
            width: 700px;
            opacity: 0.8;
            border-radius: 10px;
        }
    }

## 大坑

上面代码会使Mist主题下的站内搜索无法跳转，具体原因是因为  **opacity: 0.8;** 这条参数，透明化的同时会让搜索栏透明化，导致无法站内搜索。
 
            #header {
            padding: 5px 25px;
            margin: 0 auto;
            margin-top:15px;
            width: 700px;
            //Notice
            //opacity: 0.8;
            border-radius: 10px;
            
        }



修改完上面的配置可能会发现首页博客主体部分的下方与底部展示信息的间隙过大，我们加入以下配置进行调整，需要注意的是，此时添加的配置与上方添加位置不同，需要在body{}下方添加；

    body .main {
    margin-bottom: 45px;
    }

# 侧边栏背景的更改
同样在配置文件 Blog\themes\next\source\css\_custom\custom.styl 中添加：


    #sidebar {
            p,span,a {color: #ffb6c1;}
    }


    #sidebar {
                background:url(../images/sidebar_bc.jpg);
                background-size: cover;
                background-position:center;
                background-repeat:no-repeat;
                p,span,a {color: #ffb6c1;}
    }

# 修改主题菜单栏背景
我用的主题是Next-Mist主题
修改文件 theme/next/source/css/_schemes/Mist/_header.styl 文件，

    .header { background: $whitesmoke; }
将背景改为透明，0.3为透明度，

    .header { background: rgba(255,255,255,0.3); }

将内容加入url可用图片作为背景，图片文件放在 Blog/source/images/ 文件目录下，

    .header { background: url('../images/header_bc.jpg'); }
