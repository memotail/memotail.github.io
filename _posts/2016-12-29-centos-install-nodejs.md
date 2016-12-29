---
layout:     post
title:      "CentOS安装nodejs方法"
subtitle:   "走在服务器的路上"
date:       2016-12-29
author:     "Memotail"
catalog:    true
tags:
    - CentOS
    - Nodejs
---

# CentOS安装nodejs方法

### 安装Nodejs

##### yum 安装
  > 需要root 账号，参考：<https://nodejs.org/en/download/package-manager/>

    // 添加源，"_6.x"是版本号，若不存在，则是默认的"0.10"版本，所以需要添加相关版本
    $ curl --silent --location https://rpm.nodesource.com/setup_6.x | bash - 

    // 安装nodejs
    $ yum install -y nodejs


##### 源码安装

    // 准备命令
    $ yum -y install gcc make gcc-c++ openssl-devel wget  
    
    // 下载源码及解压, "v6.9.2"是版本号，可以在 https://nodejs.org/en/download/ Source Code 查看最新版本
    $ wget https://nodejs.org/dist/v6.9.2/node-v6.9.2.tar.gz   
    $ tar -zvxf node-v6.9.2.tar.gz

    // 编译及安装
    $ cd node-v6.9.2
    $ ./configure
    $ make && make install

// 验证是否安装配置成功：

    $ node -v
