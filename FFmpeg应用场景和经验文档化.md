

## FFmpeg 系统化研究

- ffmpeg项目化
  - 编译规则
  - 参数规则
  - 加入新编解码
  - 加入新协议
- ffmpeg命令行参数规则   --mast
  - 系统介绍
  - 项目应用
  - 多线程支持
- 音视频封装协议分析：支持的参数，文件存储，推拉流，时间戳处理，命令行使用示例
  - rtmp   --mast 
  - rtp、rtsp 、udp   --邓军
  - srt        --尤继
  - ts     --尤继
  - hls  --尤继
- 音视频编解码分析：支持的参数，命令行转码示例       --尤继 邓军
  - 视频：硬件加速参数 编解码参数 码率 关键帧处理 尺寸 ...
    - h264       
    - h265   
  - 音频： 采样率 ...
    - aac      
    - mp3
    - g711   
- 图像视频处理分析：命令行示例，支持参数       --mast 
  - 图像尺寸转换     
  - 图像格式转换（色域空间）
  - 字幕叠加
  - 图片叠加
  - 视频特效
  - 视频拆分合并
- 音频处理分析：命令行示例，支持参数       --邓军
  - 采样率转换
  - 多声道
  - 混音

