## FFmpeg

### 安装
brew install ffmpeg

ffmpeg相关依赖（会自动下载）
==> Fetching dependencies for ffmpeg: brotli, highway, imath, openexr, webp, jpeg-xl, libvmaf, aom, aribb24, dav1d, frei0r, gmp, libtasn1, nettle, p11-kit, libevent, libnghttp2, unbound, gnutls, lame, fribidi, libunibreak, libass, libbluray, cjson, libmicrohttpd, mbedtls, librist, libsoxr, libssh, libvidstab, libogg, libvorbis, libvpx, opencore-amr, openjpeg, isl, mpfr, libmpc, gcc, openblas, numpy, pugixml, snappy, hwloc, tbb, openvino, opus, rav1e, libsamplerate, flac, mpg123, libsndfile, rubberband, sdl2, speex, srt, svt-av1, leptonica, libb2, libarchive, pango, tesseract, theora, x264, x265, xvid, libsodium, zeromq and zimg

### FFmpeg静态库
- libavformat:用于各种音视频封装格式的生成和解析。
libavutil:核心工具库，做一些基本音视频处理操作。
libavcodec:音视频各种格式的编解码。
libswscale:图像进行格式转换模块。
libavfilter:音视频滤镜库。
libpostproc:后期处理模块。
libswresample:用于音频重采样。
libadvice:用于硬件的音视频采集。
libavresample:这个是老版本下编译出来，新版本用libswresample代替。

可执行文件：
1.ffmpeg 用于转码、推流、dump媒体文件
2.ffplay 用于播放媒体文件
3.ffprobe 用于获取媒体文件信息
4.ffserver 用于简单流媒体服务器

### FFmpeg 常用命令
**ffprobe常用命令**
- ffprobe 文件名：查看音频视频文件信息
- ffprobe -show_format 文件名 ：查看文件的输出格式信息，时间长度，文件大小，比特率，流数目等。
- ffprobe -print_format json -show_streams 文件名：以json格式输出详细信息
- ffprobe -show_frames 文件名：显示帧信息
- ffprobe -show_packets 文件名：查看包信息

**ffplay常用命令**
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

**ffmpeg常用命令**
- ffmpeg -formats ：列出ffmpeg支持的所有格式
- ffmpeg -i 输入文件 -ss 00:00:50.0 -codec copy -t 20 输出文件名：剪切一段媒体文件
-i 指定输入文件
-ss 从指定时间开始
-codec 强制使用codec编解码方式 copy代表不进行重新编码
-t 指定时长 秒
- ffmpeg -i input.mp4 -t 00:00:50 -c copy small-1.mp4 -ss 00:00:50 -codec copy small-2.mp4 ：将一个时间比较长的视频文件切割为多个文件
- ffmpeg -i input.mp4 -vn -acodec copy output.m4a：提取一个视频文件中的音频文件（-vn 取消视频输出 -acodec 指定音频编码 copy代表不进行重新编码）
- ffmpeg -i input.mp4 -an -vcodec copy output.mp4：使一个视频中的音频静音，只保留视频（-an 取消音频输出 -vcodec 指定视频编码 copy代表不进行重新编码）
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


