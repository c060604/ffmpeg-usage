# 项目介绍

介绍一些常用的 ffmpeg 处理命令。

## FFMpeg 安装

[FFMpeg 官网](http://ffmpeg.org/)

本文使用 **ffmpeg-3.1.5** 版本。

## 选项参数

基本语法格式：

```
ffmpeg [options] [[infile options] -i infile]... {[outfile options] outfile}...
```

## 常用选项

```
-version:   显示版本信息。

-formats:   显示所有有效的格式。

-decoders:  显示所有有效解码器。

-encoders:  显示所有有效的编码器。

-bsfs:      显示有效的数据流滤镜。

-pix_fmts:  显示有效的像素格式。
```

## 主要选项

```
-f fmt(input|output):      指定输入或者输出文件格式，可依据扩展名自动指定。

-i filename(input):        指定输入文件。

-y (global):               默认自动覆盖输出文件。

-n (global):               不覆盖输出文件，如果输出文件已存在则立即退出。

-t duration(input|output): 限制输入／输出的时间。
                           如果用于输入选项，就是限定从输入中读取多少时间的数据；
                           如果用于输出选项，则表示写入多少时间数据后就停止。
                           duration 可以是以秒为单位的数值活着 hh:mm:ss[.xxx] 格式的时间值。
                           -to 和 -t 是互斥的，-t 有更高的优先级。

-to time_stop(output):     写入 time_stop 时间后就停止。
                           time_stop 可以是以秒为单位的数值或者 hh:mm:ss[.xxx] 格式的时间值。

-fs limit_size(output):    设置输出文件大小限制，单位是字节（bytes）。

-ss time_off(input|output): 指定输入文件或输出文件的开始位置。
```

## 视频选项

```
-vframes number(output): 设置输出文件的帧数。

-r rate(input|output):   设置帧率（Hz 值）。

-s size(input|output):   设置帧的尺寸。数据格式是 WxH，即宽度值x高度值。

-aspect aspect(output):  指定视频的长宽显示比例。格式为浮点数字符串或者 num:den 格式字符串。
                         如"4:3"，"16:9"，"1.333"等。

-vcodec codec(output):   设置视频编码器。
```

## 音频选项

```
-aframes number(output): 设置输出文件的帧数。

-ar rate(input|output): 设置音频的采样率，单位为 Hz。

-aq quality(output): 设置音频品质（编码指定为 VBR）。

-ac channels(input|output): 设置音频通道数。

-af filtergraph(output): 对音频使用 filtergraph 滤镜效果。
```

## 常用命令

### 获取视频信息

```
ffprobe -v quiet -print_format json -show_format -show_streams inputfile
```

### 视频截图

```
ffmpeg -i input_file -y -f mjpeg -ss 1 -t 0.001 -s widthxheight output_file

i: 源文件
y: 覆盖输出文件
f: 截图格式
ss: 起始位置，单位秒
t: 截图时间，单位秒
s: 图片宽x高
```

#### 每隔 1 秒截一张图

```
ffmpeg -i input.mp4 -f image2 -vf fps=fps=1 out%d.jpg
```

#### 每隔 20 秒截一张图

```
ffmpeg -i input.mp4 -f image2 -vf fps=fps=1/20 out%d.jpg
```

#### 将视频的前 30 帧转换成一个 Gif

```
ffmpeg -i input.mp4 -vframes 30 -y -f gif output.gif
```

#### 从视频中生成 Gif

```
ffmpeg -i input.mp4 -t 10 -pix_fmt rgb24 output.gif
```

#### 转换视频为图片（每帧一张图)

```
ffmpeg -i input.mp4 out%d.jpg
```

#### 图片转换为视频

```
ffmpeg -f image2 -i out%d.jpg -r 25 video.mp4
```

#### 提取视频的关键帧

```
ffmpeg -i input.mp4 -vf select='eq(pict_type\,I)' -vsync 2 -s 160x90 -f image2 out-%02d.jpeg
```

### 分解视频音频流

```
ffmpeg -i input_file -vcodec copy -an output_file_video     // 分离视频流

ffmpeg -i input_file -vcodec copy -vn output_file_audio     // 分离音频流 
```

### 视频转码

```
ffmpeg -i input.mp4 -vcodec h264 -an -f m4v test.264    // 转码为码流原始文件
```

### 视频封装

```
ffmpeg -i video_file -i audio_file -vcodec copy -acodec copy output_file
```

### 视频录制

```
ffmpeg -i rtsp://hostname/stream -vcodec copy output.avi

ffmpeg -f avfoundation -framerate 30 -i "0" -f mpeg1video -b 500k -r 20 -vf scale=640:360 output.avi        // 通过电脑摄像头录制
```