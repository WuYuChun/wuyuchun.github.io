---
layout: post
title:  "putty+Xming+VMare+ubuntu"
date:   2015-03-24 9:42 PM
excerpt:
tag:
- 工具
---


[9:42 PM]


# 前言
自己的笔记本电脑实在很破，跑一个虚拟机就不行，但是又需要用Ubuntu进行编程。只能使用putty+Xming了。

# 环境搭建

 1. Putty下载，并链接到Ubuntu中。
 （1）测试Ubuntu是否已经安装SSH，命令：
 安装ssh-server
sudo apt-get install openssh-server
安装ssh-client
测试：
ps -e | grep sshd 
结果： 

![这里写图片描述](http://img.blog.csdn.net/20150324212255910)

已经安装成功！
（2）![这里写图片描述](http://img.blog.csdn.net/20150324212352668)
在Host Name中输入Ubuntu的IP，点击open，接着会让你输入用户名和密码，
（3）![这里写图片描述](http://img.blog.csdn.net/20150324212711907)
为什么会出现乱码？其实很多时候，出现乱码，是因为编码格式不一致。
重新运行putty，在左边Windows->Translation，就可以看到如下画面，选择UTF-8.
![这里写图片描述](http://img.blog.csdn.net/20150324212924732)
（4）效果
![这里写图片描述](http://img.blog.csdn.net/20150324213146492)
2.Xming的使用：现在可以使用命令行了，但是想要运行Ubuntu中的界面程序该如何呢？这时就Xming出场了。
（1）下载Xming，一路安装默认的形式安装，就可以了。
3.Putty和Xming的结合使用：
（1）先运行Xming。
（2）接着配置Putty
![这里写图片描述](http://img.blog.csdn.net/20150324213625951)
![这里写图片描述](http://img.blog.csdn.net/20150324213746609)
（3）效果，如下：
![这里写图片描述](http://img.blog.csdn.net/20150324213959174)

# 结论
主要是自己的笔记本太破，只能这样子使用。
