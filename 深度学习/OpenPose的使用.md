---
title: OpenPose的使用
categories: 深度学习
abbrlink: 65a0e96c
date: 2018-12-30 22:15:58
---
### Windows版本

&emsp;&emsp;首先确保计算机中有显卡，并安装好`CUDA`模块。然后下载`OpenPose`的`Windows`版本，并进行解压操作。
&emsp;&emsp;查看`OpenPose`的参数可以使用如下命令：

``` bash
bin\OpenPoseDemo.exe --help
```

&emsp;&emsp;使用OpenPose分析视频文件如下：

``` bash
bin\OpenPoseDemo.exe --video .\examples\media\video.avi
```

如果只需要显示一个人的姿态，可以使用参数`number_people_max`：

``` bash
bin\OpenPoseDemo.exe --video .\examples\media\video.avi --number_people_max 1
```

保存视频可以使用参数`write_video`：

``` bash
bin\OpenPoseDemo.exe --video .\examples\media\video.avi --write_video .\examples\video.avi
```

&emsp;&emsp;保存人体姿态的图像使用参数`write_images`：

``` bash
bin\OpenPoseDemo.exe --image_dir .\examples\media\ --write_images .\examples\media\images\
```

&emsp;&emsp;将人体关键点保存为`json`文件可以使用参数`write_keypoint_json`：

``` python
bin\OpenPoseDemo.exe --image_dir .\examples\media\ --write_keypoint_json .\examples\media\json\
```

保存的`json`文件如下：

``` json
{
    "version": 1.2,
    "people": [
        {
            "pose_keypoints_2d": [
                385.895, 130.626, 0.784749,
                429.924, 246.015, 0.480111,
                334.069, 253.87, 0.492662,
                303.744, 357.555, 0.417447,
                268.541, 309.6, 0.721677,
                510.142, 239.183, 0.364753,
                564.929, 294.949, 0.189901,
                528.75, 197.12, 0.278824,
                0, 0, 0,
                0, 0, 0,
                0, 0, 0,
                0, 0, 0,
                0, 0, 0,
                0, 0, 0,
                369.297, 113.999, 0.89678,
                418.163, 115.94, 0.88171,
                0, 0, 0,
                473.962, 159.959, 0.743814
            ],
            "face_keypoints_2d": [ ],
            "hand_left_keypoints_2d": [ ],
            "hand_right_keypoints_2d": [ ],
            "pose_keypoints_3d": [ ],
            "face_keypoints_3d": [ ],
            "hand_left_keypoints_3d": [ ],
            "hand_right_keypoints_3d": [ ]
        }
    ]
}
```

`pose_keypoints`即为当前图像中人体`18`个关节点的数据信息，一个关节点信息包括`(x, y, score)`三个信息，`x`和`y`即为图像中的坐标信息，取值范围为`(0, image.size)`；而`score`则表示预测评分，取值范围为`(0, 1)`，越接近`1`值表示预测得越准确，其关节点的还原度就越高，同时姿态的还原度也就越高。