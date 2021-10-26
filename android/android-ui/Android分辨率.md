# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Andorid 分辨率

dpi范围	密度
0dpi ~ 120dpi	ldpi	
120dpi ~ 160dpi	mdpi	1
160dpi ~ 240dpi	hdpi	1.5
240dpi ~ 320dpi	xhdpi	2
320dpi ~ 480dpi	xxhdpi	3
480dpi ~ 640dpi	xxxhdpi	4


ico尺寸
密度	建议尺寸
mipmap-mdpi	48 * 48
mipmap-hdpi	72 * 72
mipmap-xhdpi	96 * 96
mipmap-xxhdpi	144 * 144
mipmap-xxxhdpi	192 * 192


不同分辨率图片放大缩小规则：
图片所有文件dpi x 图片尺寸 = 手机dpi
图片所有文件dpi越小，图片尺寸越大
图片所有文件dpi越大，图片尺寸越小
放大倍数未dpi之间的倍数


占用内存的算法是 (图片所属资源密度 / 手机DPI * 原始图片宽度) * (图片所属资源密度 / 手机DPI * 原始图片高度)


adb查看手机分辨率：
adb shell dumpsys window displays
结果：init=1080x1920 420dpi cur=1080x1920 app=1080x1920 rng=1080x1017-1920x1857