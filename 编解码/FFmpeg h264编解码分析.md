- ffmpeg x264 编码参数对照表:[网址](https://blog.csdn.net/byxdaz/article/details/80663718)

- 

- **ffmpeg 编解码命令参数** [参考网址](https://blog.csdn.net/Lyman_Ye/article/details/80305904)
  
  - **编码器预设参数preset**
  
    主要调节编码速度和质量的平衡，有ultrafast、superfast、veryfast、faster、fast、medium、slow、slower、veryslow、placebo这10个选项，从快到慢，默认的编码速度是medium模式
  
    ./ffmpeg -i ../testFiles/E1.mp4 -vcodec libx264 -preset ultrafast -b:v 2000k E1_2000k.mp4
  
  - **编码优化参数tune**
  
    这个参数配合视频类型和视觉优化的参数。可选项
  
    - film:电影真人类型
    - animation：动画
    - grain：需要保留大量的grain
    - stillimage：静态图像编码时使用
    - psnr：提高psnr做了优化的参数
    - ssim：提高ssim做了优化参数
    - fastdecode：可以快速解码的参数
    - zerolatency：零延迟，用在需要非常低的延迟的情况，比如电视电话会议编码
  
    ./ffmpeg -i input.mp4 -vcodec libx264 -tune zerolatency -b:v 2000k output.mp4
  
  - **profile和level设置**
  
    profile和level的设置和H.264标准文档ISO-14496-Part10描述的profile和level信息基本相同。profile有如下选项：
  
    - Baseline：编码后的视频不含B帧
    - Extented
    - Main
    - High：编码后的视频含B帧
    - High10
    - High422
    - High444
  
    profile设置信息不同会影响编码出来的视频的很多参数不同。例如是否支持I与P分片。level也会影响很多参数，例如最大解码速度不同。
  
    下面使用baseline profile和high profile编码一个H.264视频，分析两个编码出来的文件的区别。有一个知识我们提取了解一下baseline profile编码出来额视频不会包含B帧，而high profile包含B帧，下面就看它们B帧的差别
  
    ./ffmpeg -i input.mp4 -vcodec libx264 -profile:v baseline -level 3.1 -s 352x288 -an -y -t 10 ouput_baseline.ts
    ./ffmpeg -i input.mp4 -vcodec libx264 -profile:v high -level 3.1 -s 352x288 -an -y -t 10 ouput_high.ts
    生成了两个文件，通过ffprobe来查看包含B帧的信息
  
    ./ffprobe -v quiet -show_frames -select_streams v output_baseline.ts |grep "pict_type=B"|wc -l
    输出0
  
    ./ffprobe -v quiet -show_frames -select_streams v output_high.ts |grep "pict_type=B"|wc -l
    输出161
    验证了我们的理论baseline profile包含0个B帧，而high profile包含B帧。
  
    

