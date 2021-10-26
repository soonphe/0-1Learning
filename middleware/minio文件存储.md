# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## minio文件存储
官网地址：http://docs.minio.org.cn/docs/
github地址：https://github.com/minio/minio

### 下载安装
brew下载：
brew install minio/stable/minio
minio server /data

docker下载：
docker pull minio/minio
简单传递一个目录：
docker run -p 9000:9000 minio/minio server /data
创建具有永久存储的MinIO容器，您需要将本地持久目录从主机操作系统映射到虚拟配置~/.minio 并导出/data目录：
docker run -p 9000:9000 --name minio1 \
  -v /mnt/data:/data \
  -v /mnt/config:/root/.minio \
  minio/minio server /data
  
要覆盖MinIO的自动生成的密钥，您可以将Access和Secret密钥设为环境变量。MinIO允许常规字符串作为Access和Secret密钥。
docker run -p 9000:9000 --name minio1 \
  -e "MINIO_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE" \
  -e "MINIO_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
  -v /mnt/data:/data \
  -v /mnt/config:/root/.minio \
  minio/minio server /data

二进制下载：
macOS：
http://dl.minio.org.cn/server/minio/release/darwin-amd64/minio
linux：
http://dl.minio.org.cn/server/minio/release/linux-amd64/minio

使用MinIO浏览器进行验证
安装后使用浏览器访问http://127.0.0.1:9000，如果可以访问，则表示minio已经安装成功。

### 








