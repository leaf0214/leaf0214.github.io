---
layout: post
title: Windows部署OpenPose
categories: OpenPose
description: OpenPose在Win10上的部署。
keywords: OpenPose
---

![](/images/posts/openpose/openpose-body-architecture-1024x291-1.webp)
OpenPose的架构图，输入会是一张大小为 w x h 的彩色影像；接着会先经过 Stage 0 的 VGG-19 Network，得到输入影像的 feature map；然后会在 Stage 1 经过一些 CNN 来得到身体各部位的 confidence map。Stage 1 分成两个branch，branch 1 可以产生某特定部位的 confidence map。

OpenPose下载：https://github.com/CMU-Perceptual-Computing-Lab/openpose/releases

CUDA下载：https://developer.nvidia.com/cuda-downloads

CUDA官方安装教程：https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html

CUDA环境变量配置：path中加入C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0\bin

验证安装：nvcc -V

cuDNN下载：https://developer.nvidia.com/cudnn

cuDNN官方安装教程：https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#installwindows

cuDNN环境变量配置：path中加入C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0\lib\x64

CMake下载：https://cmake.org/download

官网配置地址：https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/installation.md

配置过程：
1. 顺序安装Visual Studio、CUDA、cuDNN，OpenPose 的官方要求是 Visual Studio 2015 Enterprise Update 3，将cuDNN压缩包里的文件拷贝到CUDA安装目录覆盖；
1. 将OpenPose解压，并建一个build空文件夹，运行 3rdparty\windows 里的getCaffe.bat、getCaffe3rdparty.bat、getFreeglut.bat、getOpenCV.bat这四个文件；
1. 运行 models 里的 getModels.bat 文件
1. 用 cmake 在build文件夹下生成 OpenPose.sln 文件
1. 将 OpenPose 设为启动项并在 Release 下运行
1. 在shell中执行exe文件，需要将所有{openpose_folder}\3rdparty\windows\caffe\bin\中的dll文件拷贝到{openpose_folder}\windows\x64\Release。将所有{openpose_folder}\3rdparty\windows\opencv\x64\vc14\bin\中的dll文件拷贝到 {openpose_folder}\windows\x64\Release。
