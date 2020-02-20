## FFmpeg软件架构

- **软件宏观架构**

  - 媒体输入
    - 读取本地文件
    - 拉取网络流
    - 采集设备
  - 媒体处理
    - 尺寸转换
    - 封装格式转换
    - 编码格式转换
    - 特效
  - 媒体输出
    - 保存本地文件
    - 推送网络流
    - 输出设备

- **FFmpeg关键结构体**

  - **AVPacket** ： 存储编码后的音视频数据

  - **AVFrame**： 存储原始格式的音视频数据

  - **AVDictionary** 键值对数组

    - AVDictionaryEntry
      - char *key
      - char *value;

  - **AVClass**

    - AVOption

      打开输入文件的时候，AVDictionaryEntry中参数由avformat_open_input执行av_opt_set_dict方法参数**转移**过来

  - **AVOption**

  - **AVOutputFormat**  ： 写封装格式数据抽象，有各种封装格式的实现，各种实现中使用AVCodecContext中封装格式自身特定的写入方法实现写入数据

    - write_packet

  - **AVInputFormat**：

     读封装格式数据抽象，有各种封装格式的实现，各种实现中使用AVCodecContext中封装格式自身特定的读取方法实现读取数据

    - read_packet

  - **AVFormatContext** ：全局格式上下文

    - AVClass
    - AVInputFormat
    - AVOutputFormat
    - AVIOContext
    - AVStream

  - **AVIOContext**： 二进制格式IO读写的上下文

    - AVClass

  - **AVStream**

    - AVCodecParameters
    - 流相关信息
    - AVStreamInternal

  - **AVStreamInternal**

    - AVCodecContext

  - **AVCodecContext** ：编解码器上下文

    - AVClass
    - AVMediaType
    - AVCodec
    - AVCodecID
    - AVCodecInternal
    - 编解码各种相关参数
    - AVHWAccel

  - AVCodecInternal

  - **AVCodec**：编解码抽象结构体，有各种编码格式的实现

    - AVMediaType
    - AVCodecID

  - **AVHWAccel** ：编解码硬件加速相关

  - **AVCodecParameters** ：编解码相关参数