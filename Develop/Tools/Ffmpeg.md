---
source_title: Ffmpeg
categories:
- Doc
- Tools
last_modified: '2025-08-01T07:14:01Z'
---
多媒体视频处理工具 FFmpeg 有非常强大的功能，包括视频采集功能、视频格式转换、视频抓图、给视频加水印等。

### 常用参数

-i 设定输入流

-f 设定输出格式

-ss 开始时间

#### 视频参数

-b:v 设定视频流量(码率), 默认为200Kbit/s

-r:v 设定帧速率, 默认为25

-s:v 设定画面的宽与高

-aspect:v 设定画面的比例

-c:v 设定视频编解码器, 未设定时则使用与输入流相同的编解码器

-vn 不处理视频

#### 音频参数

-ar 设定采样率

-ac 设定声音的Channel数

-c:a 设定声音编解码器, 未设定时则使用与输入流相同的编解码器

-b:a 设定声音流量(码率)

-an 不处理音频

#### 翻转

-vf "hflip" 水平翻转

-vf "vflip" 垂直翻转

-vf "transpose="
```
 0: 逆时针旋转90度并垂直翻转
 1: 顺时针旋转90度
 2: 逆时针旋转90度
 3: 顺时针旋转90度后并垂直翻转
```
```
 -vf "transpose=0" 逆时针旋转90度并垂直翻转
 -vf "transpose=2" 逆时/顺时针旋转180度
 -vf "rotate=PI/2" 指定角度:90度（原宽高没变，所以显示两侧有黑边）
 -vf "rotate=PI/3" 指定角度:60度（原宽高不变，出现黑底，也有画面被隐藏）
```

### Merge
```
 ffmpeg -i a.m4a -i v.mp4 -c:v copy -c:a copy out.mp4
```

### Extract

视频中提取音频
```
 ffmpeg -i ${FN}.mp4 -vn -c:a copy ${FN}.m4a
 # mp4/mkv/... To AAC（128kbps, m4a 封装）
 ffmpeg -i ${FN}.mp4 -vn -c:a aac -b:a 128k ${FN}.m4a
```

### Cut
```
 ffmpeg -ss 00:05:00 -to 00:07:30 -i in.mp4 -c:v copy -c:a copy out.mp4
 ffmpeg -ss 00:05:00 -t 00:2:30 -i in.mkv -c copy out.mp4
```

### Convert

检查 FFmpeg 的支持格式的列表
```
 ffmpeg -formats
```
```
 X264='-c:v libx264 -preset medium -crf 23'
 # 视频质量 -crf, 18～28, 越小质量越好
 # 速度 -preset, medium/fast/slow
```

视频格式转换
```
 ffmpeg -i "${FN}.mkv" ${X264} -c:a copy "_out_${FN}.mp4"
 # 音频质量 -c:a aac -b:a 192k
```

更改视频分辨率
```
 ffmpeg -i in.mp4 -vf scale="1280:-1" ${X264} -c:a copy out.mp4
```

音频格式转换
```
 # flac/wav/... To ALAC（Apple Lossless Audio Codec, m4a 封装）
 ffmpeg -i ${FN}.flac -c:a alac ${FN}.m4a
```

### Rotate
```
 ffmpeg -i in.mp4 -vf "transpose=2" out.mp4
```

### Concat
```
 ffmpeg -f concat -i list.txt -c copy out.mp4
 # list.txt
 file /tmp/l1.mp4
 file /tmp/l2.mp4
```

### Download

#### m3u8
```
 ffmpeg -i https://????.m3u8 -c copy -bsf:a aac_adtstoasc out1.mp4
```
