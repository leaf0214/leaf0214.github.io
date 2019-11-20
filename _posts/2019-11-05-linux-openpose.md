---
layout: post
title: Linux OpenPose
categories: Linux
description: Linux部署OpenPose
keywords: Linux
---

1. OpenPose安装

   ```
    git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose.git ~/workspace/openpose
    cd ~/workspace/openpose
    export OPENPOSE_HOME=${PWD}
    mkdir build
    cd build
    cmake ..
    make -j`nproc`
   ```

测试

   ```
    ./build/examples/openpose/openpose.bin --image_dir examples/media/ --write_images ~/workspace/test/openpose
   ```

1. st-gcn安装

   ```
    git clone https://github.com/yysijie/st-gcn.git st-gcn
    pip3 install torch torchvision
    sudo apt-get install ffmpeg
    cd st-gcn
    pip install -r requirements.txt
    cd torchlight; python setup.py install; cd ..
    bash tools/get_models.sh
   ```

测试

   ```
    python main.py demo --openpose '~/workspace/openpose/build' --video '~/workspace/test/openpose/henry-push-up.mp4'
   ```

1. caffe安装

安装各种依赖包

   ```
    sudo apt-get install -y --no-install-recommends libboost-all-dev
    sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev libgflags-dev libgoogle-glog-dev liblmdb-dev protobuf-compiler libopenblas-dev libatlas-base-dev
   ```

下载caffe

   ```
    git clone https://github.com/BVLC/caffe
    cd caffe
    export CAFFE_HOME=${PWD}
   ```

安装python依赖

   ```
    sudo apt-get install python-pip
    export LC_ALL=C
    sudo pip install scikit-image protobuf
    cd ${CAFFE_HOME}/python
    for req in $(cat requirements.txt); do sudo pip install $req; done
   ```

修改caffe的Makefile文件

   ```
    cd ${CAFFE_HOME}
    vim Makefile.config
   ```

主要有几个属性：

   ```
    使用CPU还是GPU: 
    # CPU-only switch (uncomment to build without GPU support).
    CPU_ONLY := 1
    
    设置OpenCV版本
    # Uncomment if you're using OpenCV 3
    OPENCV_VERSION := 3
    
    设置anaconda目录
    # ANACONDA_HOME := $(HOME)/anaconda3
    # PYTHON_LIB := $(ANACONDA_HOME)/lib
   ```

遇到的错误

编译失败，缺少openlabs

   ```
    sudo apt-get install libopenblas-dev
   ```

编译失败，找不到文件 hdf5.h

   ```
    查找 hdf5.h 文件的位置，例如在 /usr/include/hdf5/serial/hdf5.h
    修改Makefile.config，修改属性 INCLUDE_PATH，添加属性值 /usr/include/hdf5/serial
   ```

链接失败，找不到文件 hdf5_hl hdf5 cblas atlas

   ```
    locate查找文件的文件，然后添加到 Makefile.config 文件中 LIBEARY_DIS 属性的值里
   ```

链接失败，找不到文件 lcblas latlas

   ```
    sudo apt-get install libatlas-base-dev
   ```

执行 make distribute 时找不到 arrayobject.h 文件

   ```
    sudo apt-get install python-numpy
   ```
