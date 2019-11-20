---
layout: post
title: Python API
categories: OpenPose
description: OpenPose 的 Python 模块提供了 Python API，可以用于构建 OpenPose 类，其输入是 numpy array 格式的图像，并得到 numpy array 格式的 Pose 位置估计。
keywords: OpenPose
---

1. Python API 模块及安装

   ```
    sudo apt-get install python3-dev
    sudo pip3 install numpy opencv-python
   ```

OpenPose的 Python API，需要在 CMake GUI 中设置 BUILD_PYTHON。

在 build中运行 make install 安装命令，则 OpenPose 库安装路径为 /usr/local/python。

1. 直接利用 OpenPose 库提取人体关键点

   ```python
    # Ubuntu 环境
    import sys
    import cv2
    import matplotlib.pyplot as plt

    # 添加编译安装的 OpenPose 路径：
    sys.path.append('/usr/local/python')

    # 导入 OpenPose 库
    try:
        from openpose import *
    except:
        raise Exception('Error: OpenPose library could not be found. '
                        'Did you enable `BUILD_PYTHON` in CMake and '
                        'have this Python script in the right folder?')
    # OpenPose 参数
    params = dict()
    params["logging_level"] = 3
    params["output_resolution"] = "-1x-1"
    params["net_resolution"] = "-1x368"
    params["model_pose"] = "BODY_25"
    params["alpha_pose"] = 0.6
    params["scale_gap"] = 0.3
    params["scale_number"] = 1
    params["render_threshold"] = 0.05
    # 多 GPUs 时，GPUID 设置
    params["num_gpu_start"] = 0
    params["disable_blending"] = False
    # 模型路径
    params["default_model_folder"] = "OPENPOSE_ROOT/models/"
    # 构建 OpenPose 对象，分配 GPU 显存
    openpose = OpenPose(params)

    img = cv2.imread("test.jpg")
    # 计算输出关键点，及带有人体骨骼的图像结果
    keypoints, output_image = openpose.forward(img, True)
    # 打印人体关键点结果
    # 如，包含图像中所有人体的关节点结果， [#people x #keypoints x 3]-维 numpy 对象
    print(keypoints)
    # 显示图片
    plt.imshow(output_image[:,:,::-1])
    plt.axis('off')
    plt.show()
   ```

1.  从 heatmaps 提取人体关键点

   ```python
    import sys
    # 需要指定 Caffe 的编译路径
    sys.path.insert(0, '/path/to/caffe/python')
    import caffe

    import os
    os.environ["GLOG_minloglevel"] = "1"
    import cv2
    import numpy as np
    import matplotlib.pyplot as plt

    sys.path.append('/usr/local/python')

    try:
        from openpose import OpenPose
    except:
        raise Exception('Error: OpenPose library could not be found. '
                        'Did you enable `BUILD_PYTHON` in CMake and '
                        'have this Python script in the right folder?')

    # 参数设置
    # 单尺度
    defRes = 368
    scales = [1]
    # # 多尺度Multi-scale
    # defRes = 736
    # scales = [1, 0.75, 0.5, 0.25]
    class Param:
        caffemodel = "OPENPOSE_ROOT/models/pose/body_25/pose_iter_584000.caffemodel"
        prototxt = "OPENPOSE_ROOT/models/pose/body_25/pose_deploy.prototxt"

    # 加载 OpenPose 对象和 Caffe Nets
    params = dict()
    params["logging_level"] = 3
    params["output_resolution"] = "-1x-1"
    params["net_resolution"] = "-1x"+ str(defRes)
    params["model_pose"] = "BODY_25"
    params["alpha_pose"] = 0.6
    params["scale_gap"] = 0.25
    params["scale_number"] = len(scales)
    params["render_threshold"] = 0.05
    params["num_gpu_start"] = 0
    params["disable_blending"] = False
    params["default_model_folder"] = "OPENPOSE_ROOT/models/"
    openpose = OpenPose(params)

    caffe.set_mode_gpu()
    caffe.set_device(0)
    nets = []
    for scale in scales:
        nets.append(caffe.Net(Param.prototxt, Param.caffemodel, caffe.TEST))
    print("[INFO] Caffe Net loaded")

    # 测试函数
    first_run = True
    def func(frame):

        # Get image processed for network, and scaled image
        imagesForNet, imagesOrig = OpenPose.process_frames(frame, defRes, scales)

        # Reshape
        global first_run
        if first_run:
            for i in range(0, len(scales)):
                net = nets[i]
                imageForNet = imagesForNet[i]
                in_shape = net.blobs['image'].data.shape
                in_shape = (1, 3, imageForNet.shape[1], imageForNet.shape[2])
                net.blobs['image'].reshape(*in_shape)
                net.reshape()

            first_run = False
            print("[INFO] Images Reshaped")

        # Forward 计算得到 heatmaps
        heatmaps = []
        for i in range(0, len(scales)):
            net = nets[i]
            imageForNet = imagesForNet[i]
            net.blobs['image'].data[0,:,:,:] = imageForNet
            net.forward()
            heatmaps.append(net.blobs['net_output'].data[:,:,:,:])

        # Pose from HM Test
        array, frame = openpose.poseFromHM(frame, heatmaps, scales)

        # Draw Heatmaps instead
        # hm = heatmaps[0][:,0:18,:,:]; 
        # frame = OpenPose.draw_all(imagesOrig[0], hm, -1, 1, True)
        # paf = heatmaps[0][:,20:,:,:]; 
        # frame = OpenPose.draw_all(imagesOrig[0], paf, -1, 4, False)

        return frame

    img = cv2.imread('test.jpg")
    frame = func(img)
    plt.imshow(frame[:,:,::-1])
    plt.axis('off')
    plt.show()
   ```
1. Exporting Python OpenPose
如果需要将 *.py 脚本移出其原来的路径，或者，在build/examples/tutorial_api_python 路径外新建 *py 脚本.

①安装 OpenPose - 在 Ubuntu 系统中，可以通过 sudo make install 安装 OpenPose；然后，在 python 脚本中设置 OpenPose 的安装路径(默认：/usr/local/python)，然后即可在任何位置开始使用 OpenPose。 参考：build/examples/tutorial_pose/1_extract_pose.py。

②不安装 OpenPose - 为了将 OpenPose Python API Demo 放置在不同的路径，需要在 *.py 脚本中添加 sys.path.append('{OpenPose_path}/python')，其中，{OpenPose_path} 为 OpenPose 的 build 路径。 参考：build/examples/tutorial_pose/1_extract_pose.py。

1. Python API 编译时遇到的问题解决

在 openpose 编译完成后，采用 Python API 调用时，遇到如下问题：

   ```
    from . import pyopenpose as pyopenpose
    ImportError: cannot import name pyopenpose
   ```

首先，在 openpose 路径找到文件：build/python/openpose/pyopenpose.cpython-35m-x86_64-linux-gnu.so；复制该文件到路径：/usr/local/lib/python3.5/dist-packages。

   ```
    cd OpenPose_rootpath/build/python/openpose/
    sudo cp pyopenpose.cpython-35m-x86_64-linux-gnu.so /usr/local/lib/python3.5/dist-packages/
   ```

然后，进入路径：/usr/local/lib/python3.5/dist-packages，创建软连接：

   ```
    cd /usr/local/lib/python3.5/dist-packages/
    sudo ln -s pyopenpose.cpython-36m-x86_64-linux-gnu.so pyopenpose
   ```
确认环境变量中 LD_LIBRARY_PATH 包含 /usr/local/lib/python3.5/dist-packages。

最后，在 Python 脚本中调用：

   ```
    import pyopenpose as op
    #注：
    #原始脚本中是 from openpose import pyopenpose as op，需要修改为上行代码。
   ```
