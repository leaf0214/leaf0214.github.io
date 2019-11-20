---
layout: post
title: Docker搭建深度学习环境
categories: Docker
description: 使用Docker搭建深度学习环境。
keywords: Docker
---

1. ubuntu 16 使用Docker安装anaconda

   ```
    docker search anaconda
    docker pull continuumio/anaconda3
    docker images
    docker run -t -i continuumio/anaconda3 /bin/bash
    python
   ```

1. Docker 退出容器但不关闭当前容器

   方法一：如果要正常退出不关闭容器，请按Ctrl+P+Q进行退出容器

   方法二：如果使用exit退出，那么在退出之后会关闭容器，可以使用下面的流程进行恢复

   ```
    使用docker restart命令重启容器
    使用docker attach命令进入容器
   ```

   重启httpd（service httpd restart）和radosgw(/etc/init.d/ceph-radosgw restart)，并且使用wget验证是否将radosgw重启成功(wget http://127.0.0.1)

1. docker-compose安装

   ```
    curl -L https://github.com/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose
   ```

1. ubuntu 安装nvidia-docker

安装docker-ce

   ```
    sudo curl https://get.docker.com | sudo sh
   ```

安装ncidia-docker

   ```
    curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
      sudo apt-key add -
    curl -s -L https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64/nvidia-docker.list | \
      sudo tee /etc/apt/sources.list.d/nvidia-docker.list
    sudo apt-get update
    sudo apt-get install -y nvidia-docker2
    sudo usermod -aG docker $USER﻿​
   ```

错误问题解决

   ```
    /bin/nvidia-docker: line 34: /bin/docker: Permission denied错误

    sudo semanage fcontext -a -t container_runtime_exec_t /usr/bin/nvidia-docker
    sudo restorecon -v /usr/bin/nvidia-docker
   ```
