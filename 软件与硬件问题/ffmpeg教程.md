---
title: ffmpeg教程
date: 2021-03-05 05:55:21
categories: 软件与硬件问题
---
### 简介

&emsp;&emsp;`FFmpeg`是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序。<!--more-->

### 安装方法

&emsp;&emsp;从`FFmpeg`官网上下载`Windows`版`FFmpeg`文件，然后将其中的`ffmpeg.exe`路径添加到系统环境变量中。
&emsp;&emsp;在`cmd`上输入`ffmpeg`，如果出现对应的版本信息，则说明安装成功。

### 使用方法

#### 视频格式转换

&emsp;&emsp;将视频的编码从`MPEG4`，转换为`H264`：

``` bash
ffmpeg -i input.mp4 -vcodec libx264 output.mp4
```