## FFmpeg 参数系统分析

- **ffmpeg命令行参数**

  - 命令行输入的参数后保存在OptionGroup中（ffmpeg_opt.c中）

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
      - 调用：**avformat_open_input**(&ic, filename, file_iformat, &o->g->**format_opts**); 传入OptionGroup 中format_opts。
      - 调用：add_input_streams 获取流信息并加到输入流数组中
        - 调用：avcodec_alloc_context3 根据某路流对应的编码器创建新的AVCodecContext
        - 调用：avcodec_parameters_to_context 获取新某路流对应的编码器参数给新创建的AVCodecContext
        - 调用：filter_codec_opts 获取解码器选项参数
        - 解析**帧率、硬件加速**等相关参数
        - 调用：avcodec_parameters_from_context 参数设置给编码器的参数
    - 方法：**init_complex_filters** 解析滤镜相关参数
    - 方法： int **open_output_file**(OptionsContext *o, const char *filename) 打开输出文件
      - 根据参数设置情况修正并设置参数
      - 循环初始化滤镜组
        - 调用：init_output_filter
      - 调用：new_video_stream 根据输入文件最高分辨率创建对应的输出流

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
  
- **结构体：AVInputFormat**
  
  - 持有AVClass指针：priv_class，保存对应输入流的封装协议参数
  
- **结构体：AVOutputFormat**
  
  - 持有AVClass指针：priv_class，保存对应输入流的封装协议参数
  
- **结构体：AVFormatContext**
  
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
  
- **参数表**

  - 编解码 ：libavcodec/options_table.h
  - 封装 ：libavformat/options_table.h

