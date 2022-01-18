# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## minio文件存储
github地址：https://github.com/minio/minio
官网文档地址：http://docs.minio.org.cn/docs/

### 下载安装

#### brew下载：
brew install minio/stable/minio #安装
minio server /data  #替换/data为您希望 MinIO 存储数据的驱动器或目录的路径。
brew service start minio  #启动

#### docker下载：
docker pull minio/minio
简单传递一个目录：
docker run -p 9000:9000 minio/minio server /data
创建具有永久存储的MinIO容器，您需要将本地持久目录从主机操作系统映射到虚拟配置~/.minio 并导出/data目录：
```
docker run \
  -p 9000:9000 \
  -p 9001:9001 \
  -e "MINIO_ROOT_USER=AKIAIOSFODNN7EXAMPLE" \
  -e "MINIO_ROOT_PASSWORD=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
  quay.io/minio/minio server /data --console-address ":9001"

```
GNU/Linux 和 macOS
```
mkdir -p ~/minio/data

docker run \
-p 9000:9000 \
-p 9001:9001 \
--name minio1 \
-v ~/minio/data:/data \
-e "MINIO_ROOT_USER=AKIAIOSFODNN7EXAMPLE" \
-e "MINIO_ROOT_PASSWORD=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
quay.io/minio/minio server /data --console-address ":9001"
```
~/minio/data该命令在您的用户主目录中创建一个新的本地目录。然后它使用参数启动 MinIO 容器，-v将本地路径 ( ~/minio/data) 映射到指定的虚拟容器目录 ( /data)。当 MinIO 将数据写入 时/data，该数据实际上被写入本地路径~/minio/data，它可以在容器重新启动之间保持不变。

docker启动命令：
docker start $ContainerName

#### 二进制下载：
使用以下命令在 macOS 上下载并运行独立的 MinIO 服务器。替换/data为您希望 MinIO 存储数据的驱动器或目录的路径。
```
wget https://dl.min.io/server/minio/release/darwin-amd64/minio
chmod +x minio
./minio server /data
```

GNU/Linux
使用以下命令在运行 64 位 Intel/AMD 架构的 Linux 主机上运行独立的 MinIO 服务器。替换/data为您希望 MinIO 存储数据的驱动器或目录的路径。
```
wget https://dl.min.io/server/minio/release/linux-amd64/minio
chmod +x minio
./minio server /data
```

### 使用MinIO浏览器进行验证
- 安装后使用浏览器访问http://127.0.0.1:9000，如果可以访问，则表示minio已经安装成功。
- 默认凭据：minioadmin:minioadmin
![alt text](../static/middleware/minio.png "minio")

### 创建bucket
点击Create bucket，输入名称"war"
- 也可以在bucket下新建文件夹，也就是新路径


### 上传文件
点击Upload file，将text.txt上传


### 下载文件
使用wget命令：wget http://IP:9000/war/text.txt







