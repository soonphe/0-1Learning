## FFmpeg

### 安装
brew install ffmpeg

ffmpeg相关依赖（会自动下载）
==> Fetching dependencies for ffmpeg: brotli, highway, imath, openexr, webp, jpeg-xl, libvmaf, aom, aribb24, dav1d, frei0r, gmp, libtasn1, nettle, p11-kit, libevent, libnghttp2, unbound, gnutls, lame, fribidi, libunibreak, libass, libbluray, cjson, libmicrohttpd, mbedtls, librist, libsoxr, libssh, libvidstab, libogg, libvorbis, libvpx, opencore-amr, openjpeg, isl, mpfr, libmpc, gcc, openblas, numpy, pugixml, snappy, hwloc, tbb, openvino, opus, rav1e, libsamplerate, flac, mpg123, libsndfile, rubberband, sdl2, speex, srt, svt-av1, leptonica, libb2, libarchive, pango, tesseract, theora, x264, x265, xvid, libsodium, zeromq and zimg

### FFmpeg静态库
- libavformat:用于各种音视频封装格式的生成和解析。
- libavutil:核心工具库，做一些基本音视频处理操作。
- libavcodec:音视频各种格式的编解码。
- libswscale:图像进行格式转换模块。
- libavfilter:音视频滤镜库。
- libpostproc:后期处理模块。
- libswresample:用于音频重采样。
- libadvice:用于硬件的音视频采集。
- libavresample:这个是老版本下编译出来，新版本用libswresample代替。

可执行文件：
1. ffmpeg 用于转码、推流、dump媒体文件
2. ffplay 用于播放媒体文件
3. ffprobe 用于获取媒体文件信息
4. ffserver 用于简单流媒体服务器

### ffplay常用命令
- ffplay 文件名 ：播放音频、视频文件
- ffplay 文件名 -loop 10 ：循环播放文件10次
- ffplay 文件名 -ast 1 ：播放视频第一路音频流 参数如果是2 就是第二路音频流 如果没有就会静音。
- ffplay 文件名 -vst 1 ：播放视频第一路视频流 参数如果是2 就是第二路视频流 没有显示黑屏
- ffplay .pcm文件 -f 格式 -channels 2 声道数 -ar 采样率 ：播放pcm文件 必须设置参数正确
- ffplay -f rawvideo -pixel_format yuv420p -s 480480 .yuv文件：播放yuv原始数据 （-f rawvideo 代表原始格式，-pixel_format yuv420p 表示格式，-s 480480 宽高）
- ffplay -f rawvideo -pixel_format rgb24 -s 480*480 .rgb文件：播放rgb原始数据
- ffplay 文件名 -sync audio ：指定ffplay使用音频为基准进行音视频同步默认ffplay也是这样
- ffplay 文件名 -sync video ：指定ffplay使用视频为基准进行音视频同步
- ffplay 文件名 -ext video ：指定ffplay使用外部时钟为基准进行音视频同步

### ffprobe常用命令
- ffprobe 文件名：查看音频视频文件信息
- ffprobe -show_format 文件名 ：查看文件的输出格式信息，时间长度，文件大小，比特率，流数目等。
- ffprobe -print_format json -show_streams 文件名：以json格式输出详细信息
- ffprobe -show_frames 文件名：显示帧信息
- ffprobe -show_packets 文件名：查看包信息

### ffmpeg常用命令
- ffmpeg -formats ：列出ffmpeg支持的所有格式
- ffmpeg -i input.mp4 -ss 00:00:50.0 -codec copy -t 20 output.mp4：剪切一段媒体文件
- ffmpeg -i input.mp4 -t 00:00:50 -c copy small-1.mp4 -ss 00:00:50 -codec copy small-2.mp4 ：将一个时间比较长的视频文件切割为多个文件
- ffmpeg -i input.mp4 -vn -acodec copy output.m4a：提取一个视频文件中的音频文件（-vn 取消视频输出 -acodec 指定音频编码 copy代表不进行重新编码）
- ffmpeg -i input.mp4 -an -vcodec copy output.mp4：使一个视频中的音频静音，只保留视频（-an 取消音频输出 -vcodec 指定视频编码 copy代表不进行重新编码）
- ffmpeg -i input.mp4 -vn -codec:a libmp3lame -q:a 4 output.mp3            提取视频文件为MP3
- ffmpeg -i input.mp4 -vn -c:a aac -b:a 192k output.m4a             提取视频文件为m4a
- ffmpeg -i input.mp3 -ss 00:00:00 -t 5 -acodec copy output.mp3           将音频音频从指定位置剪辑5秒输出

参数解析：
```
-i 指定输入文件
-ss 从指定时间开始
-vn: 表示不包含视频流（即仅提取音频）。
-codec:a libmp3lame: 指定输出音频编码器为 libmp3lame，这是 MP3 编码。
-q:a 4: 音频质量设置，范围从 0（最好）到 5（最差），通常使用范围在 0 到 9。
-c:a aac 指定输出音频编码为AAC。
-b:a 192k 设置音频比特率为192k。

-codec 强制使用codec编解码方式 copy代表不进行重新编码
-acodec copy 表示复制音频编码流。
-t 指定时长 秒
-output.mp3: 输出文件的名称，这里是转换后的 MP3 文件。
```

- ffmpeg -i output.mp4 -an -vcodec copy -bsf:v h264_mp4toannexb output.h264 ：视频数据使用h264_mp4toannexb这个bitstream filter来转换为原始的H264数据。 从mp4文件中抽取视频流导出为裸H264数据
- ffmpeg -i test.aac -i test.h264 -acodec copy -bsf:a aac_adtstoasc -vcodec copy -f mp4 output.mp4：使用aac音频数据和H264的视频生成MP4文件（-f 指定输出格式）
- ffmpeg -i input.wav -acodec libfdk_aac output.aac ：对音频文件的编码格式做转换
- ffmpeg -i input.wav -acodec pcm_s16le -f s16le output.pcm ：从wav音频文件中导出pcm裸数据
- ffmpeg -i input.flv -vcodec libx264 -acodec copy output.mp4 ：重新编码视频文件，复制音频流 同时封装到MP4格式文件中
- ffmpeg -i input.mp4 -vf scale=100:-1 -t 5 -r 10 image.gif：将一个MP4格式的视频转换为gif格式的动图（-vf设置视频的过滤器，按照分辨率比例不动宽度改为100（使用videofilter的scalefilter），帧率改为10（-r）时长改为5（-t））
- ffmpeg -i input.mp4 -r 0.25 frames_%04d.png ：每4 秒截取一帧视频生成一张图片，生成的图片从frames_0001.png开始递增
- ffmpeg -i frames_%04d.png -r 5 output.gif ：使用一组图片可以组成一个gif
- ffmpeg -i input.wav -af ‘volume=0.5’ output.wav ：使用音量效果器，改变一个音频媒体文件中的音量
- ffmpeg -i input.wav -filter_complex afade=t=in:ss=0:d=5 output.wav ：将该音频文件前5秒钟做一个淡入效果
- ffmpeg -i input.wav -filter_complex afade=t=out:st=200:d=5 output.wav ：将音频从200s开始 做5秒的淡出效果
- ffmpeg -i input1.wav -i input2.wav -filter_complex amix=inputs=2:duration=shortest output.wav ：将两个音频进行合并，按照时间较短的音频时间长度作为输出
- ffmpeg -i input.wav -filter_complex atempo=0.5 output.wav ：将音频按照0.5倍的速度进行处理生成output.wav 时间长度变为原来的2倍，音高不变
- ffmpeg -i test.avi -i frames_0004.jpeg -filter_complex overlay after.avi ：给视频添加水印
- ffmpeg -i test.avi -vf"drawtext=fontfile=simhei.ttf:text=‘雷’:x=100:y=10:fontsize=24:fontcolor=yellow:shadowy=2" after.avi ：添加文字水印
- ffmpeg -i input.flv -c:v libx264 -b:v 800k -c:a libfdk_aac -vf eq=brightness=0.25 -f mp4 output.mp4 ：视频提高亮度 参数brightness 取值范围-1.0到1.0。
- ffmpeg -i input.flv -c:v libx264 -b:v 800k -c:a libfdk_aac -vf eq=contrast=1.5 -f mp4 output.mp4 ：视频增加对比度 参数contrast，取值范围是从-2.0到2.0
- ffmpeg -i input.mp4 -vf “transpose=1” -b:v 600k output.mp4 ：视频旋转效果器使用
- ffmpeg -i input.mp4 -an -vf “crop=240:480:120:0” -vcodec libx264 -b:v 600k output.mp4 ：视频裁剪效果器使用
- ffmpeg -i input.mp4 -vf scale=852:484 -preset slow -crf 18 video.mp4 ：调整视频的分辨率
- ffmpeg -f rawvideo -pix_fmt rgba -s 480*480 -i texture.rgb -f image2 -vcodec mjpeg output.jpg ：将一张RGBA格式表示的数据转换为JPEG格式的图片
- ffmpeg -f rawvideo -pix_fmt yuv420p -s 480*480 -i texture.yuv -f image2 -vcodec mjpeg output.jpg ：将一个YUV格式表示的数据转换为JPEG格式的图片
- ffmpeg -re -i ipnut.mp4 -acodec copy -vcodec copy -f flv rtmp://xxx ：将一段视频推送到流媒体服务器上
- ffmpeg -i http://xxx/xxx.flv -acodec copy -vcodec copy -f flv output.flv ：将流媒体服务器上的流dump到本地

- ffmpeg -list_devices true -f dshow -i dummy：列出 Windows 系统上可用的音视频设备
  -list_devices true：这是一个选项参数，用于告诉 FFmpeg 列出可用的设备。
  -f dshow：这是另一个选项参数，用于指定使用 DirectShow 框架来访问设备。
  -i dummy：这是输入参数，dummy 是一个虚拟设备名称，用于触发设备列表的输出。
- ffplay -f dshow -i video="Integrated Camera"：使用前置摄像头进行捕捉画面


### 推流的几种方式
```
RTMP
rtmp://xxx/static-train-simulator/20240418489545_in
FLV
http://xxx:1360/static-train-simulator/20240418489545_in.flv
HLS
http://xxx:1240/static-train-simulator/20240418489545_in.m3u8
WebRTC
webrtc://xxx:1240/static-train-simulator/20240418489545_in
```

flv本地文件推流推流
- ffmpeg -re -i ipnut.mp4 -acodec copy -vcodec copy -f flv rtmp://xxx
- ffmpeg -stream_loop -1 -re -i input.mp4 -c copy -f flv  rtmp://192.168.1.232:1935/http_flv/stream_key
- ffmpeg -re -i input.MP4 -c:v libx264 -preset veryfast -maxrate 3000k -bufsize 6000k -pix_fmt yuv420p -g 50 -c:a aac -b:a 160k -ar 44100 -ac 2 -f flv rtmp://xxx:2045/static-train-simulator/20240328093947_in

flv读取摄像头并推流：（未验证）
- ffmpeg -f v4l2 -framerate 30 -video_size 1280x720 -i /dev/video0 -c:v libx264 -preset veryfast -maxrate 1000k -bufsize 2000k -pix_fmt yuv420p -g 50 -f flv rtmp://yourserver/live/stream_key
- ffmpeg -f dshow -i video="Integrated Camera":audio="麦克风阵列 (Synaptics SmartAudio HD)" -vcodec libx264 -acodec copy -preset:v ultrafast -tune:v zerolatency -f flv "rtmp://192.168.20.107:1935/live/home"

hls切片
- ffmpeg -re -i input.mp4 -c:v libx264 -c:a aac -strict -2 -f hls -hls_list_size 0 -hls_time 5 /Users/luoxiaosheng/Downloads/ts/test.m3u8
hls切片+推流
- ffmpeg -re -i input.mp4 -c:v libx264 -c:a aac -strict experimental -b:a 128k -b:v 256k -f hls -hls_time 10 output.m3u8

参数：
```
stream_loop -1 表示循环推本地mp4文件
-re 按原始帧率读取
-c:v libx264：视频编码器使用libx264。
-preset veryfast：使用快速编码预设。
-maxrate 1000k：视频编码最大码率。
-bufsize 2000k：码率控制缓冲区大小。
-pix_fmt yuv420p：像素格式。
-g 50：I帧间隔。
-c:a aac：音频编码器使用AAC。
-b:a 160k：音频编码比特率。
-ar 44100：音频采样率。
-ac 2：音频通道数。
-f flv：输出格式为FLV，FLV是一种流媒体格式，常用于RTMP推流
rtmp://yourserver/live/stream：RTMP输出地址。

-f v4l2: 指定输入格式为v4l2，即Video4Linux2，通常用于访问Linux系统上的摄像头。
-framerate 30: 设置帧率为30。
-i /dev/video0: 指定输入设备，通常是摄像头的文件路径。

- hls time n: 设置每片的长度，默认值为2。单位为秒
- hls list size n:设置播放列表保存的最多条目，设置为0会保存有所片信息，默认值为5
- his wrap n设置多少片之后开始覆盖，如果设置为0则不会覆盖，默认值为0.,这个选项能够避免在磁盘上存储过多的片，而且能够限制写入磁盘的最多的片的数量
- hls start number n:设置播放列表中sequence number的值为number，默认值为0
```

M3U8文件是指UTF-8编码格式的M3U文件。M3U文件是记录了一个索引纯文本文件，打开它时播放软件并不是播放它，而是根据它的索引找到对应的音视频文件的网络地址进行在线播放。
TS（Transport Stream，传输流）是一种封装的格式，它的全称为MPEG2-TS。主要应用于数字广播系统，比如DVB、ATSC与IPTV。传输流最初是为广播而设计的。后来，通过在标准的188字节数据包中添加4字节的时间码（TC），从而使该数据包成为192字节的数据包，使其适用于数码摄像机，录像机和播放器。

m3u8 文件其实是 HTTP Live Streaming（缩写为 HLS） 协议的部分内容，而 HLS 是一个由苹果公司提出的基于 HTTP 的流媒体网络传输协议
简而言之，HLS 是新一代流媒体传输协议，其基本实现原理为将一个大的媒体文件进行分片，将该分片文件资源路径记录于 m3u8 文件（即 playlist）内，其中附带一些额外描述（比如该资源的多带宽信息···）用于提供给客户端。客户端依据该 m3u8 文件即可获取对应的媒体资源，进行播放。

m3u8文件：
```
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-TARGETDURATION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXTINF:8.333333,
test0.ts
#EXTINF:3.533333,
test1.ts
#EXT-X-ENDLIST
```

### 推流格式说明
> RTSP （Real-Time Stream Protocol）由Real Networks 和 Netscape共同提出的，基于文本的多媒体播放控制协议。RTSP定义流格式，流数据经由RTP传输；RTSP实时效果非常好，适合视频聊天，视频监控等方向。一般摄像头都是RTSP格式的。h5原生不支持这种格式。优点，可以控制到视频帧，因此可以承载实时性很高的应用。这个优点是相对于HTTP方式的最大优点。复杂度主要集中在服务器端，可以进行倍速播放功能，其他视频协议都无法支持。 网络延时低，一般在0.5S以内；缺点，就是服务器端的复杂度也比较高，实现起来也比较复杂。ios端不支持该协议，对移动端支持较弱；除了 Firefox 浏览器可以直接播放 RTSP 流之外,几乎没有其他浏览器可以直接播放 RTSP 流。RTSP协议，此协议和RTMP效果差不多，在技术上只是区别于传输数据上占用多少通道、传输格式流不太一样而已，RTSP其实也可以用于直播。但依然是因为市场环境，RTSP目前主要应用在安防监控上，和RTMP一样，早已形成了自己的盈利链

> RTMP（Real Time Message Protocol） 有 Adobe 公司提出，用来解决多媒体数据传输流的多路复用（Multiplexing）和分包（packetizing）的问题，优势在于低延迟，稳定性高，支持所有摄像头格式，RTMP协议是采用实时的流式传输，所以不会缓存文件到客户端，这种特性说明用户想下载RTMP协议下的视频是比较难的，视频流可以随便拖动，既可以从任意时间点向服务器发送请求进行播放，并不需要视频有关键帧。相比而言，HTTP协议下视频需要有关键帧才可以随意拖动，rtmp协议只支持flashplayer 就是只能在PC端（或安卓环境中安装了flashplayer组件，这种环境比较少）安装了flashplayer的情况下使用，浏览器加载 flash插件就可以直接播放,但是flash已经日落西山了。因此，目前 RTMP 主要用于提取 stream。也就是，当设置解编码器将视频发送到托管平台时，视频将使用 RTMP 协议发送到 CDN，随后使用另一种协议（通常是HLS）传递给播放器。

> HTTP: 当使用http协议的时候视频格式需要是m3u8或HTTP-FLV协议视频流。HLS 协议由三部分组成：HTTP、M3U8、TS。这三部分中，HTTP 是传输协议，M3U8 是索引文件，TS 是音视频的媒体信息m3u8是有延迟的。并不能实时，实时传输方面不如rtmp协议。因为m3u8的直播原理是将直播源不停的压缩成指定时长的ts文件（比如9秒，10秒一个ts文件）并同时实时更新m3u8文件里的列表以达到直播的效果。这样就会有一个至少9,10秒的时间延迟。如果压缩的过小，可能导致客户端网络原因致视频变卡。HTTP-FLV 即将流媒体数据封装成 FLV 格式，然后通过 HTTP 协议传输给客户端 HTTP协议中有个约定：content-length字段，http的body部分的长度服务器回复http请求的时候如果有这个字段，客户端就接收这个长度的数据然后就认为数据传输完成了，如果服务器回复http请求中没有这个字段，客户端就一直接收数据，直到服务器跟客户端的socket连接断开。http-flv直播就是利用第二个原理，服务器回复客户端请求的时候不加content-length字段，在回复了http内容之后，紧接着发送flv数据，客户端就一直接收数据了

> 除了HTTP、WebSocket类的传输协议，其他是无法通用地传输到浏览器的，所以，如果要做一款通用的H5视频播放器，基本上就是一款HTTP/WebSocket协议的视频播放器，如果是类似于RTMP、RTSP类型协议的视频源，是不可避免，需要经过服务器转换的


|        | RTMP/RTSP                                                           | HTTP-FLV                                                                                                                                                                   | HLS                                                                                          |
|--------|---------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| 传输协议   | TCP                                                                 | HTTP                                                                                                                                                                       | НТТР                                                                                         |
| 视频封装格式 | flv                                                                 | flv                                                                                                                                                                        | ts                                                                                           |
| 延时     | 1-3秒                                                                | 1-3秒                                                                                                                                                                       | 5-20秒                                                                                        |
| Web支持  | H5 不能直接播放                                                           | H5 不能直接播放                                                                                                                                                                  | 支持H5                                                                                         |
| 数据     | 连续流                                                                 | 连续流                                                                                                                                                                        | 切片文件                                                                                         |
| 优点     | 基于TCP长连接，不需要多次建连，延时低，通常只有1~35；技术成熟，配套完善。                            | flv.js在获取到FLV格式的音视频数据后将 FLV 文件流转码复用成 ISO BMFF（MP4 碎片）片段，再通过Media Source Extensions API 传递给原生HTML5 Video 标签进行播放。低延时，整体效果与RTMP 非常接近；相较于RTMP协议，能有效避免 用在移动设备上是再合适不过了防火墻和代理的影响。 | 基于HTTP协议，所以接入CDN较为容易，很少被防火墻拦下，且自带多码率自适应；作为苹果提出的协议，在macOS/iOS下有极大优势，Android中也提供了对应的支持；可以说此项协议 |
| 缺点     | 在PC浏览器中只能通过 Flash使用，且无法在移动浏览器使用；鉴于Flash即将退出舞台，所以在网页播放端基本不会以RTMP做拉流。 | 把音视频数据封装成FLV，然后通过HTTP 延时较大，通常不低于10s。大量的TS连接传输，与RTMP相比只是传输协议变了。对于网页播放端，本来还是需要Flash 力才能播放，但「flvjs」的出现又弥补了这个缺陷它的传输特性会让流媒体资源缓存在本地客户端，也就是说保密性不怎么样；直到目前仍然不兼容iOS的浏览器              | 切片文件，会造成服务器存储和请求的压力                                                                          |

### 搭建推流服务器
- 方案一：Nginx添加模块nginx-rtmp-module
- 方案二：Nginx添加模块http-flv-module

nginx-http-flv-module是基于nginx-rtmp-module 的流媒体服务器。 它具备了所有nginx-rtmp-module的功能，并且新增多种新功能，功能对比如下。详见https://github.com/winshining/nginx-http-flv-module
```
#增加如下配置即可
# 推流时会发布到多个子进程
rtmp_auto_push on;
rtmp_auto_push_reconnect 1s;
rtmp {  
    server {  
        listen 1935;  #监听的端口号
        chunk_size 8192; # 单一推流数据包的最大容量
        
        application hls {  # 创建rtmp应用hls
            live on;  # 当路径匹配时，开始播放
            #HLS协议进行m3u8实时直播.如果是http-flv不需要配置下面的
            wait_key on;#保护TS切片
            hls on;  #实时回访
            hls_nested on;#每个流都自动创建一个文件夹
            hls_path /tmp/hls; #媒体块ts的位置
            hls_fragment 5s; #每个ts文件为5s的样子
            hls_playlist_length 30s;  #保存m3u8列表长度时间，默认是30秒，可考虑三小时10800秒
            hls_cleanup on;#是否删除列表中已经没有的媒体块TS文件，默认是开启
            hls_continuous on;#连续模式
       }  
    } 
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    server {
        listen       88;
        server_name  localhost;
        error_log logs/rtmp/error.log;# http协议下的访问日志
        access_log  logs/rtmp/access.log;
        location /stat { # 开启这两个页面可以观察rtmp流的统计,不需要可去掉
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }
    location /stat.xsl { # 查看状态的部分，不需要可去掉
        root /opt/nginx-1.19.1-rtmp/nginx-http-flv-module/;
    }
    location /flv_live { # 拉http-flv的配置
            flv_live on;
            chunked_transfer_encoding on;
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Credentials' 'true';
    }
    # 通过http访问的这个路径会被转发到/tmp/hls 寻找对应的m3u8文件和视频片段例如 111.m3u8，111-622.ts，前缀名字为视频上传流的stream_key
    location /hls { # 拉m3u8的配置
        types {
              application/vnd.apple.mpegurl m3u8;
              video/mp2t ts;
            }
        alias /tmp/hls;   
        expires -1;
        add_header 'Cache-Control' 'no-cache'; 
    }
    location / { # 首页
        root html;
        index index.html index.htm;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
            root   html;
    }
  }
}
```

### 拉流
- 推流地址 rtmp://192.168.1.232:1935/hls/stream_key
- rtmp拉流地址：rtmp://192.168.1.232:1935/hls/stream_key(与推流地址相同，但都可以播放)
- http-flv拉流地址：http://192.168.1.232:88/flv_live?port=1935&app=live&stream=stream_key
- hls-m3u8拉流地址：http://192.168.1.232:88/hls/stream_key/index.m3u8
备注：关于m3u8 因为要缓存文件，所以hls_fragment 和hls_playlist_length 尽量稍微保留久一些，不然会出现读取了m3u8，但是媒体块ts被系统更新了，导致媒体文件不能正常播放M3U8

- ffplay 命令进行拉流操作：ffplay rtmp://39.105.129.233/myapp/   推流完毕后 , 推流的命令行直接退出 , 拉流的命令行需要手动退出 ;
- ffmpeg 命令进行拉流操作：ffmpeg -i rtmp://39.105.129.233/myapp/ -c copy output.flv

### mp3和m4a 
mp3和m4a：
- M4A是MPEG-4音频标准的文件的扩展名。而MP3则是属于MPEG-3音频标准
* M4A属于苹果专用的音频格式，而MP3则是一种通用音频格式。
* M4A属于高品质压缩类型的音乐文件，而MP3则是低品质有损压缩。
- M4A是使用高级音频编码（AAC）编码的音频文件格式，而MP3则使用有损压缩编码。在音质上，M4A通常被认为优于MP3

### html中的video标签
```
<video controls="" autoplay="" name="media">
	<source src="xxx" type="video/mp4">
</video>
```
video和source是html5的新标签
<video> 元素支持三种视频格式：MP4、WebM、Ogg
* MP4 = MPEG 4文件使用 H264 视频编解码器和AAC音频编解码器
* WebM = WebM 文件使用 VP8 视频编解码器和 Vorbis 音频编解码器
* Ogg = Ogg 文件使用 Theora 视频编解码器和 Vorbis音频编解码器

video加载加密视频
```
<video class="_1bL7cF" preload="auto" x5-video-player-type="h5-page" playsinline="" webkit-playsinline="" src="https://vodkgeyttp9.vod.126.net/cloudmusic/obj/w5zDkcKQw6LDiWzDgcK2/9307099946/2982/a4d2/ea7e/90e004ffb551677743f401c59099799b.data?wsSecret=8b87543dee2e4b43528b3ce57d3346e1&amp;wsTime=1724223436"></video>

<video webkit-playsinline="" playsinline="" x-webkit-airplay="" x5-playsinline="" preload="preload" autoplay="autoplay" style="width: 100%; height: 100%;" src="blob:https://jpjk.bdsana.com/5420cdd4-53d2-4c2b-8c51-38fa9ad1b69e"></video>
```
blob:https并不是一种协议，而是html5中blob对象在赋给video标签后生成的一串标记，blob对象对象包含的数据，浏览器内部会解析
要使用 blob 来表征数据资源，需做到以下两点：
1、服务端返回的为资源的二进制数据
2、前端接收到二进制数据后，使用 URL.createObjectURL(blobData) 方法将服务端返回的二进制数据转换为 blob 的 url 资源挂载到相应的资源对象。

关于blob加密视频资源下载
1、利用Chrome 浏览器插件
2、第二种方法 找到m38u文件, 使用 window 下载工具 M3U8下载工具
3、审查 下载的 JS 资源

