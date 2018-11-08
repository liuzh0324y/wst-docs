# rtc 存储模块文档

## 性能测试


## ffmpeg

### ffmpeg 裁剪

```sh
ffmpeg -i INPUT -strict -2 -vf crop=1080:1080:0:420 OUTPUT
```

其中的 *crop=1080:1080:0:420* 才裁剪参数，具体含义是 crop=width:height:x:y，其中 width 和 height 表示裁剪后的尺寸，x:y 表示裁剪区域的左上角坐标。比如当前这个示例，我们只需要保留竖向视频的中间部分，所以 x 不用偏移，故传入0，而 y 则需要向下偏移：(1920 – 1080) / 2 = 420

### ffmpeg 缩放视频

```sh
ffmpeg -i INPUT -vf scale=853:480 -acodec aac -vcodec h264 OUTPUT
```