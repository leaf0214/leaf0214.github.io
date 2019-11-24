---
layout: post
title: Windows部署Tensorflow
categories: Tensorflow
description: Tensorflow在Win10上的部署。
keywords: Tensorflow
---

1. Win10搭建Tensorflow Object Detection API

   ```
    pip install tensorflow==1.13.1

    安装依赖项：
    pip install --user Cython
    pip install --user contextlib2
    pip install --user pillow
    pip install --user lxml
    pip install --user jupyter
    pip install --user matplotlib

    https://github.com/protocolbuffers/protobuf/releases
    手动下载安装：protoc-3.0.0-win32.zip（把压缩包解压即可）
    把解压出来的整个文件夹拷贝至和 object_detection 同一级目录
    命令行运行：
    \models-master\research> ./protoc-3.0.0-win32/bin/protoc.exe object_detection/protos/*.proto --python_out=.
    
    添加环境变量：
    PYTHONPATH  models-master\reserch;models-master\reserch\slim

    命令行运行：
    \models-master\research> python object_detection/builders/model_builder_test.py

    命令行运行：
    \models-master\research\object_detection> jupyter notebook，则在浏览器启动了jupyter，点击选中object_detection_tutorial.ipynb文件直接运行
   ```

1. object_detection/protos/*.proto: No such file or directory

    运行命令 protoc object_detection/protos/*.proto --python_out=.  时报错处理

   ```
    models文件夹下使用 shift+右键 的Windows powershe中使用以下命令可以全部编译：
    Get-ChildItem object_detection/protos/*.proto |Resolve-Path -Relative | %{protoc $_ --python_out=.}     
    ```
