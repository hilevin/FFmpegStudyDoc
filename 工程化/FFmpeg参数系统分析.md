## FFmpeg 参数系统分析

- **ffmpeg命令行参数**

  - 命令行输入的参数被解析后保存在OptionGroup中（ffmpeg_opt.c中）

    typedef struct OptionGroup {
        const OptionGroupDef *group_def;
        const char *arg;

        Option *opts;//指向了一个option数组
        int  nb_opts;
        
        AVDictionary *codec_opts;//编解码相关参数
        AVDictionary *format_opts;//封装格式相关参数
        AVDictionary *resample_opts;//重采样相关参数
        AVDictionary *sws_dict;
        AVDictionary *swr_opts;
    } OptionGroup;

    OptionsContext结构体持有OptionGroup 指针。

    - 方法：static int **open_input_file**(OptionsContext *o, const char *filename) 打开输入文件
      - 根据参数设置情况修正并设置参数
      - 调用：av_find_input_format 查找输入文件封装格式
      - 调用：**avformat_open_input**(&ic, filename, file_iformat, &o->g->**format_opts**); 传入OptionGroup 中format_opts。
      - 调用：add_input_streams 获取流信息并加到输入流数组中
        - 调用：avcodec_alloc_context3 根据某路流对应的编码器创建新的AVCodecContext
        - 调用：avcodec_parameters_to_context 获取新某路流对应的编码器参数给新创建的AVCodecContext
        - 调用：filter_codec_opts 获取解码器选项参数
        - 解析**帧率、硬件加速**等相关参数
        - 调用：avcodec_parameters_from_context 参数设置给编码器的参数
    - 方法：**init_complex_filters** 解析滤镜相关参数
    - 方法： int **open_output_file**(OptionsContext *o, const char *filename) 打开输出文件
      - 创建出事文件的AVFormatContext
      - 根据参数设置情况修正并设置参数
      - 循环初始化滤镜组
        - 调用：init_output_filter
      - 判断流的maps关系
        - **选择创建输出流，每种流只会创建一个**
          - 调用：**new_video_stream** 根据输入文件最高分辨率创建对应的输出流
            - 调用：new_output_stream 创建视频流，并创建了编码的AVCodecContext ost->**enc_ctx**且获取了命令行的编码器参数初始化编码器
            - 一系列解析命令行传入的视频编码器参数
          - 调用：**new_audio_stream**，选择输入流中通道计算分数最高的流，来创建一个输出音频流
            - 调用：new_output_stream 创建视频流，并创建了编码的AVCodecContext ost->**enc_ctx**且获取了命令行的编码器参数初始化编码器
            - 一系列解析命令行传入的视频编码器参数
        - **根据map关系创建输出流**
          - 调用：**new_video_stream** 根据输入文件最高分辨率创建对应的输出流
            - 调用：new_output_stream 创建视频流，并创建了编码的AVCodecContext ost->**enc_ctx**且获取了命令行的编码器参数初始化编码器
            - 一系列解析命令行传入的视频编码器参数
          - 调用：**new_audio_stream**，选择输入流中通道计算分数最高的流，来创建一个输出音频流
            - 调用：new_output_stream 创建视频流，并创建了编码的AVCodecContext ost->**enc_ctx**且获取了命令行的编码器参数初始化编码器
            - 一系列解析命令行传入的视频编码器参数
      - 处理附加文件
      - 检查所有的编码器参数都被应用了
      - 设置解码器需要的标志，创建简单的滤镜
      - ...
      - 复制元数据信息
      - 复制章节
      - 复制默认的元数据
      - 处理节目信息
      - 处理人工设置元数据
    - 方法：**new_output_stream** 根据传入参数创建输出的视频流、音频流
      - 创建AVStream保存到OutputStream
      - 保存一些参数到OutputStream
      - 选择编码器保存到OutputStream
      - 创建编码的AVCodecContext，保存到OutputStream ost->**enc_ctx**,后面的流程会获取命令行编码器相关参数
      - 分配默认编码器参数,
      - 获取命令行传入的编码器参数，保存到ost->encoder_opts
      - 获取命令行时间基参数
      - 获取命令行max_frames参数
      - ...
      - 

- **参数相关结构体**

  - **结构体：AVOption**
  
- 编码器
  
      - 每种编码都有一个静态AVOption数组，保存了支持的参数及设置参数的规则
  
    - 封装器
  
- **结构体：AVClass**
  
  - 持有AVOption指针
  
- **结构体：AVCodec**
  
  - 持有AVClass指针
  
  - init 方法：初始化为对应的编码器初始化方法
      - 方法中使用AVCodecContext中编解码相关参数初始化指定编码器的参数
  
- **结构体：AVCodecContext**
  
  - 持有AVCodec指针
    - void *priv_data 保存对应编码器实现的指针
    - **编码解码相关参数**
  
- **结构体：AVStream**

  - 持有AVCodecContext指针

- **结构体：AVInputFormat**
  
  - 持有AVClass指针：priv_class，保存对应输入流的封装协议参数
  
- **结构体：AVOutputFormat**
  
  - 持有AVClass指针：priv_class，保存对应输入流的封装协议参数
  
- **结构体：AVFormatContext**
  
  - 持有AVClass指针
    - 值为avformat_options 定义在libavcodec/options_table.h文件中，定义了封装格式所支持的所有参数
  - 持有AVInputFormat：解封装时才有
  - 持有AVOutputFormat：封装时才有
  
- **参数相关方法及流程**

  - **编解码**
    - 方法：avcodec_alloc_context3 初始化AVCodecContext 并设置默认编解码参数
      - 内部调用init_context_defaults
        - 调用：av_opt_set_defaults  设置默认参数
        - 调用av_opt_set 设置**键值对参数**
    - 方法：avcodec_get_context_defaults3 获取默认编解码参数
  - **封装**
    
    - 方法：avformat_open_input，打开输入，封装参数要没传入指定，要么自动探测
    
      - avformat_open_input
    
        - avformat_alloc_context
    
          - avformat_get_context_defaults
    
            - av_opt_set_defaults
    
              - av_opt_set_defaults2  
    
                遍历AVCodecContext  AVClass里面封装格式AVOption列表，设置成默认值
    
        - 如果传入了options,则用传入的覆盖默认参数
  
- **参数表**

  - 封装参数表 ：libavformat/options_table.h
    - **官方文档地址**：https://www.ffmpeg.org/ffmpeg-formats.html#Format-Options
  - 编解码参数表 ：libavcodec/options_table.h
    - **官方文档地址**：https://www.ffmpeg.org/ffmpeg-codecs.html#Codec-Options



## 参数设置总结

- 全局参数，封装格式参数，编解码参数，数据繁多，官网有详解介绍
- 解封装
  - 一般情况下无论是使用命令行还是编写解封装代码avformat_open_input无需指定Options，avformat_open_input内存会使用默认参数探测输入文件的封装格式信息
- 封装
- 

