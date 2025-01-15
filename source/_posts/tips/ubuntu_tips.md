---
title:  使用ubuntu的小tips
categories: [杂记]
tags:   [ubuntu,linux]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-03-04 02:51:55
---


# 常用的 linux 配置

## zsh

```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
git clone https://github.com/zsh-users/zsh-autosuggestions
```

## tmux 


# 常用的 linux 命令

## 后台运行命令

 - 第一种 &
  
    关闭终端停止运行   
  
        command &
 
 - 第二中 nohup   
    关闭终端停止运行,如果你正在运行一个进程，而且你觉得在退出帐户时该进程还不会结束，那么可以使用nohup命令。该命令可以在你退出帐户之后继续运行相应的进程。nohup就是不挂起的意思( no hang up)。 该命令的一般形式为：

        nohup conmmand &
    如果使用nohup命令提交作业，那么在缺省情况下该作业的所有输出都被重定向到一个名为nohup.out的文件中，除非另外指定了输出文件：

         nohup command > myout.file 2>&1

    在上面的例子中，输出被重定向到myout.file文件中。


## 安装有道词典

一直出现：
```        
    ModuleNotFoundError: No module named 'PyQt5.QtWebKitWidgets    
```
找了好多方法并没有解决。

    1) 检查有用 APT 安装上 PyQt5
    ----------------------------------------
    $ apt search python3-pyqt5 | grep installed
    python3-pyqt5/bionic,now 5.10.1+dfsg-1ubuntu2 amd64 [installed]
    python3-pyqt5.qtmultimedia/bionic,now 5.10.1+dfsg-1ubuntu2 amd64 [installed]
    python3-pyqt5.qtquick/bionic,now 5.10.1+dfsg-1ubuntu2 amd64 [installed]
    python3-pyqt5.qtwebkit/bionic,now 5.10.1+dfsg-1ubuntu2 amd64 [installed]

    确保 PyQt5 以上安装在 /usr/lib/python3/dist-packages/PyQt5

    1) PIP3 上卸载 PyQt5 (目前最新是 5.12.1)
    ------------------------------------------------------
    $ su
    $ apt install python3-pip
    $ pip3 list | grep PyQt5

    如有列出 PyQt5, 
    再查一查这个 PyQt5 是否安装在 /usr/local/lib/python3.6/dist-packages
    $ pip3 show PyQt5

    如是, 撤除 PyQt5 和 PyQt5-sip.
    $ pip3 uninstall PyQt5 PyQt5-sip

    1) 打开新的 terminal, 运行 (不用 $PYTHONPATH)
    ----------------------------------------------------------------
    $ youdao-dict


## vscode

vscode  虚拟环境的时候只需要通过命令面板Python: Select Interpreter就会列出所有的虚拟环境.

## ssr服务器安装脚本
```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh && chmod +x ssr.sh && bash ssr.sh
```

## ubuntu16.04安装小飞机：
```
sudo add-apt-repository ppa:hzwhuang/ss-qt5

sudo apt-get update

sudo apt-get install shadowsocks-qt5
```

## Anaconda:
```
conda config --set auto_activate_base false

conda env export > environment.yaml

conda env create -f environment.yaml

//是否自动启动conda　base 环境：
conda config --set auto_activate_base false

conda activate py35

conda deactivate
//conda 安装 opencv
conda install -c https://conda.binstar.org/menpo opencv

conda clean -p      //删除没有用的包
conda clean -t      //tar打包
//删除所有的安装包及cache
conda clean -y -all 

// 更换清华源　conda
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes

// 优先使用清华源
conda config --prepend channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/ 
```

## 显示屏设置：
```
xrandr     (查看显示屏设置)
xrandr --output HDMI2 --auto --primary
xrandr --output eDP1 --right-of HDMI2 --auto
xrandr 是查看和设置的命令
HDMI2 是显示屏的名字
auto 是自动分辨率
primary 是主屏
eDP1 是笔记本显示屏的名字
--right-of HDMI2  就是放在HDMI2 显示器的右边
xrandr --output HDMI-0 --auto --primary
xrandr --output HDMI-0 --right-of DVI-D-0 --auto
```

## Docker:

docker pull bhyang12/replab

docker run -it --rm --privileged bhyang12/replab
(-d，以后台方式执行命令；

-e，设置环境变量

-i，交互模式

-t，设置TTY

-u，用户名或UID )

- 方法一：如果要正常退出不关闭容器，请按Ctrl+P+Q进行退出容器
- 方法二：如果使用exit退出，那么在退出之后会关闭容器，可以使用下面的流程进行恢复
  
        使用docker restart命令重启容器
        使用docker attach命令进入容器

docker exec -it [container ID] bash


## scp 和 ssh
```
往服务器传文件：

scp -r <本地文件名> wzt@166.111.131.24:<上传保存路径即文件名> 

从服务器下载文件：

scp -r wzt@166.111.131.24:/home/wzt/remotefile.txt

scp -r wzt@166.111.131.24:~/Downloads Anaconda3-2018.12-x86_64.sh
```

## ros安装
### 添加源
```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'

// 设置key
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654

sudo apt-get update
sudo apt-get install ros-kinetic-desktop-full

initialize rosdep && sudo rosdep init && rosdep update
```

## Scholar 学术搜索
- 修改  /etc/hosts
    2404:6800:4008:c06::be scholar.google.com
    2404:6800:4008:c06::be scholar.google.com.hk
    2404:6800:4008:c06::be scholar.google.com.tw
    2404:6800:4005:805::200e scholar.google.cn #www.google.cn

- 修改/etc/shadowsock/use_config.jiso 

    dns_ipv6 :true

## 虚拟Ｘ-service
```
xvfb-run --auto-servernum --server-num=1 c-s "-screen 0 640x480x24" cmd
```

## visudo 权限修改
在 Ubuntu 下新建用户 需要赋予用户权限， 
修改文件是 /etc/sudoers
- 比较笨的方法，容易出问题
    - 添加用户
        adduser XXX
    - 修改文件权限
        chmod 660 /etc/sudoers
    - 添加字段
        user ALL=(ALL:ALL) ALL
    - 修改回权限(特别重要，不然就炸了)
        chmod 440 /etc/sudoers
- visudo
    - 添加字段
    - ctrl + o 保存
    - ctrl + x 退出