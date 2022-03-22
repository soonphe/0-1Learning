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
创建文件夹：
mkdir -p ~/minio/data

创建minio容器：
docker run \
-p 9000:9000 \
-p 9001:9001 \
--name minio \
-v ~/minio/data:/data \
minio/minio server /data --console-address ":9001"
```
如果自定义用户名和密码则加上
```
-e "MINIO_ROOT_USER=AKIAIOSFODNN7EXAMPLE" \
-e "MINIO_ROOT_PASSWORD=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
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

### 手动上传/下载文件
- 上传文件：点击Upload file，将text.txt上传

- 下载文件：`http://IP:9000/bucket/路径+文件名`
使用wget命令：wget http://IP:9000/war/text.txt

### api使用
引入配置
```
<!--  minio配置  -->
<dependency>
    <groupId>io.minio</groupId>
    <artifactId>minio</artifactId>
    <version>3.0.10</version>
</dependency>
```
```
minio:
  endpoint: http://192.168.51.78:9001
  accessKey: username
  secretKey: password
  # 下载地址
  http-url: http://127.0.0.1:8080/plat/admin_api/files/url
  imgSize: 10485760
  fileSize: 104857600
```

配置类：
```
import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
 
@Component
@Data
@ConfigurationProperties("minio")
public class FilesConfig {
 
    /**minio的路径**/
    private String endpoint;
 
    /**minio的accessKey**/
    private String accessKey;
 
    /**minio的secretKey**/
    private String secretKey;
    
    /**下载地址**/
    private String httpUrl;
 
    /**图片大小限制**/
    private Long imgSize;
    
    /**文件大小限制**/
    private Long fileSize;
}
```

操作模板类：
```
import com.ktwlsoft.framework.files.config.FilesConfig;
import com.ktwlsoft.framework.files.vo.MinioItem;
import io.minio.MinioClient;
import io.minio.ObjectStat;
import io.minio.Result;
import io.minio.errors.*;
import io.minio.messages.Bucket;
import io.minio.messages.Item;
import lombok.RequiredArgsConstructor;
import lombok.SneakyThrows;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.xmlpull.v1.XmlPullParserException;
 
import java.io.IOException;
import java.io.InputStream;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
 
@Component
@RequiredArgsConstructor
public class MinioTemplate implements InitializingBean {
    @Autowired
    private FilesConfig filesConfig;
    private MinioClient client;
 
    /**
     * 检查文件存储桶是否存在
     * @param bucketName
     * @return
     */
    @SneakyThrows
    public boolean bucketExists(String bucketName){
        return client.bucketExists(bucketName);
    }
 
    /**
     * 创建bucket
     *
     * @param bucketName bucket名称
     */
    @SneakyThrows
    public void createBucket(String bucketName) {
        if (!bucketExists(bucketName)) {
            client.makeBucket(bucketName);
        }
    }
 
    /**
     * 获取全部bucket
     * <p>
     * https://docs.minio.io/cn/java-client-api-reference.html#listBuckets
     */
    @SneakyThrows
    public List<Bucket> getAllBuckets() {
        return client.listBuckets();
    }
 
    /**
     * 根据bucketName获取信息
     * @param bucketName bucket名称
     */
    @SneakyThrows
    public Optional<Bucket> getBucket(String bucketName) {
        return client.listBuckets().stream().filter(b -> b.name().equals(bucketName)).findFirst();
    }
 
    /**
     * 根据bucketName删除信息
     * @param bucketName bucket名称
     */
    @SneakyThrows
    public void removeBucket(String bucketName) {
        client.removeBucket(bucketName);
    }
 
    /**
     * 根据文件前缀查询文件
     *
     * @param bucketName bucket名称
     * @param prefix     前缀
     * @param recursive  是否递归查询
     * @return MinioItem 列表
     */
    @SneakyThrows
    public List<MinioItem> getAllObjectsByPrefix(String bucketName, String prefix, boolean recursive) {
        List<MinioItem> objectList = new ArrayList<>();
        Iterable<Result<Item>> objectsIterator = client.listObjects(bucketName, prefix, recursive);
        for (Result<Item> result : objectsIterator) {
            objectList.add(new MinioItem(result.get()));
        }
        return objectList;
    }
 
    /**
     * 获取文件外链
     *
     * @param bucketName bucket名称
     * @param objectName 文件名称
     * @param expires    过期时间 <=7
     * @return url
     */
    @SneakyThrows
    public String getObjectURL(String bucketName, String objectName, Integer expires) {
        return client.presignedGetObject(bucketName, objectName, expires);
    }
 
    /**
     * 获取文件外链
     *
     * @param bucketName bucket名称
     * @param objectName 文件名称
     * @return url
     */
    @SneakyThrows
    public String getObjectURL(String bucketName, String objectName) {
        return client.presignedGetObject(bucketName, objectName);
    }
 
    /**
     * 获取文件
     *
     * @param bucketName bucket名称
     * @param objectName 文件名称
     * @return 二进制流
     */
    @SneakyThrows
    public InputStream getObject(String bucketName, String objectName) {
        return client.getObject(bucketName, objectName);
    }
 
    /**
     * 上传文件
     *
     * @param bucketName bucket名称
     * @param objectName 文件名称
     * @param stream     文件流
     * @throws Exception https://docs.minio.io/cn/java-client-api-reference.html#putObject
     */
    public void putObject(String bucketName, String objectName, InputStream stream) throws Exception {
        client.putObject(bucketName, objectName, stream, stream.available(), "application/octet-stream");
    }
 
    /**
     * 上传文件
     *
     * @param bucketName  bucket名称
     * @param objectName  文件名称
     * @param stream      文件流
     * @param size        大小
     * @param contextType 类型
     * @throws Exception https://docs.minio.io/cn/java-client-api-reference.html#putObject
     */
    public void putObject(String bucketName, String objectName, InputStream stream, long size, String contextType) throws Exception {
        client.putObject(bucketName, objectName, stream, size, contextType);
    }
 
    /**
     * 获取文件信息
     *
     * @param bucketName bucket名称
     * @param objectName 文件名称
     * @throws Exception https://docs.minio.io/cn/java-client-api-reference.html#statObject
     */
    public ObjectStat getObjectInfo(String bucketName, String objectName) throws Exception {
        return client.statObject(bucketName, objectName);
    }
 
    /**
     * 删除文件
     *
     * @param bucketName bucket名称
     * @param objectName 文件名称
     * @throws Exception https://docs.minio.io/cn/java-client-api-reference.html#removeObject
     */
    public void removeObject(String bucketName, String objectName) throws IOException, InvalidKeyException, NoSuchAlgorithmException, InsufficientDataException, InternalException, NoResponseException, InvalidBucketNameException, XmlPullParserException, ErrorResponseException {
        client.removeObject(bucketName, objectName);
    }
 
    @Override
    public void afterPropertiesSet() throws Exception {
        this.client = new MinioClient(filesConfig.getEndpoint(), filesConfig.getAccessKey(), filesConfig.getSecretKey());
    }
}
```

MinioItem实体
```
import io.minio.messages.Item;
import io.minio.messages.Owner;
import io.swagger.annotations.ApiModelProperty;
 
import java.util.Date;
 
public class MinioItem {
    /**对象名称**/
    @ApiModelProperty("对象名称")
    private String objectName;
    /**最后操作时间**/
    @ApiModelProperty("最后操作时间")
    private Date lastModified;
    private String etag;
    /**对象大小**/
    @ApiModelProperty("对象大小")
    private String size;
    private String storageClass;
    private Owner owner;
    /**对象类型：directory（目录）或file（文件）**/
    @ApiModelProperty("对象类型：directory（目录）或file（文件）")
    private String type;
 
    public MinioItem(String objectName, Date lastModified, String etag, String size, String storageClass, Owner owner, String type) {
        this.objectName = objectName;
        this.lastModified = lastModified;
        this.etag = etag;
        this.size = size;
        this.storageClass = storageClass;
        this.owner = owner;
        this.type = type;
    }
 
 
    public MinioItem(Item item) {
        this.objectName = item.objectName();
        this.type = item.isDir() ? "directory" : "file";
        this.etag = item.etag();
        long sizeNum = item.objectSize();
        this.size = sizeNum > 0 ? convertFileSize(sizeNum):"0";
        this.storageClass = item.storageClass();
        this.owner = item.owner();
        try {
            this.lastModified = item.lastModified();
        }catch(NullPointerException e){}
    }
 
    public String getObjectName() {
        return objectName;
    }
 
    public void setObjectName(String objectName) {
        this.objectName = objectName;
    }
 
    public Date getLastModified() {
        return lastModified;
    }
 
    public void setLastModified(Date lastModified) {
        this.lastModified = lastModified;
    }
 
    public String getEtag() {
        return etag;
    }
 
    public void setEtag(String etag) {
        this.etag = etag;
    }
 
    public String getSize() {
        return size;
    }
 
    public void setSize(String size) {
        this.size = size;
    }
 
    public String getStorageClass() {
        return storageClass;
    }
 
    public void setStorageClass(String storageClass) {
        this.storageClass = storageClass;
    }
 
    public Owner getOwner() {
        return owner;
    }
 
    public void setOwner(Owner owner) {
        this.owner = owner;
    }
 
    public String getType() {
        return type;
    }
 
    public void setType(String type) {
        this.type = type;
    }
 
    public String convertFileSize(long size) {
        long kb = 1024;
        long mb = kb * 1024;
        long gb = mb * 1024;
        if (size >= gb) {
            return String.format("%.1f GB", (float) size / gb);
        } else if (size >= mb) {
            float f = (float) size / mb;
            return String.format(f > 100 ? "%.0f MB" : "%.1f MB", f);
        } else if (size >= kb) {
            float f = (float) size / kb;
            return String.format(f > 100 ? "%.0f KB" : "%.1f KB", f);
        } else{
            return String.format("%d B", size);
        }
    }
}
```
接下来写一个对外的http上传接口
```
import com.google.api.client.util.Lists;
import com.ktwlsoft.framework.files.config.FilesConfig;
import com.ktwlsoft.framework.files.service.impl.MinioTemplate;
import com.ktwlsoft.framework.files.util.BaseConstant;
import com.ktwlsoft.framework.files.util.BucketNameConfig;
import com.ktwlsoft.framework.files.vo.UpLoadResult;
import com.ktwlsoft.framework.common.MvcController;
import com.ktwlsoft.framework.common.result.ActionResult;
import com.ktwlsoft.framework.common.result.ResultCode;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import io.swagger.annotations.ApiParam;
import org.apache.commons.lang3.StringUtils;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;
 
import java.util.List;
 

@Api(value = "文件上传", description = "文件上传接口", hidden = true)
@RestController
@RequestMapping(BaseConstant.PREFIX_AUTH+"/upload")
public class UploadFilesController extends MvcController {
 
    private Log log = LogFactory.getLog(UploadFilesController.class);
    @Autowired
    private MinioTemplate minioTemplate;
    @Autowired
    private FilesConfig config;
 
    /**
     * 上传文件
     * @param upfile        上传的文件对象
     * @param bucketName    所属的存储桶（第一级目录）
     * @param perfixName    文件对象前缀名称
     * @param expires       外链过期时间
     * @return
     */
    @ApiOperation(value = "上传文件" , response = UpLoadResult.class)
    @PostMapping("file")
    public ActionResult<Object> upload(@ApiParam(name = "upfile",value = "文件参数名称") @RequestParam("upfile") MultipartFile upfile,
                                             @ApiParam(name = "bucketName",value = "文件桶名称") String bucketName,
                                             @ApiParam(name = "perfixName",value = "文件对象前缀名称") String perfixName,
                                             @ApiParam(name = "expires",value = "链接过期时间") Integer expires,
                                             @ApiParam(name = "dateFile",value = "是否需要创建时间文件夹：1是，其它否") Integer dateFile) {
        String fileName = upfile.getOriginalFilename();
        try {
            fileCheck(upfile,fileName);
        } catch (Exception e) {
            return error(ResultCode.DATA_INPUT_ERROR.getValue(),e.getMessage());
        }
        if(StringUtils.isBlank(bucketName)){
            return error(ResultCode.DATA_INPUT_EMPTY);
        }
        if(StringUtils.isBlank(fileName)){
            return error(ResultCode.DATA_INPUT_EMPTY);
        }
        StringBuilder sbFile = new StringBuilder();
        if(StringUtils.isNotBlank(perfixName)){
            sbFile.append(perfixName).append(BucketNameConfig.FILE_SPLIT_PATH);
        }
        if(dateFile != null && dateFile == 1){
            // 创建时间文件夹
            sbFile.append(BucketNameConfig.getYear());
            sbFile.append(BucketNameConfig.FILE_SPLIT_PATH);
            sbFile.append(BucketNameConfig.getMonthAndDay());
            sbFile.append(BucketNameConfig.FILE_SPLIT_PATH);
        }
        sbFile.append(fileName);
        fileName = sbFile.toString();
        try {
            minioTemplate.createBucket(bucketName);
            minioTemplate.putObject(bucketName, fileName, upfile.getInputStream());
 
            UpLoadResult result = new UpLoadResult();
            if(expires == null){
                expires = BaseConstant.ONE_DAY;
            }
            result.setFileUrl(config.getHttpUrl()+"?bucketName="+bucketName+"&objectName="+fileName+"&expires="+expires);
            result.setBucketName(bucketName);
            result.setObjectName(fileName);
            return success(result);
        } catch (Exception e) {
            log.error("文件上传异常", e);
            ResultCode code =  ResultCode.FILE_SAVE_ERROR;
            return error(code);
        }
    }
 
 
    /**
     * 多上传文件
     * @param upfileList    上传的文件对象集合
     * @param bucketName    所属的存储桶（第一级目录）
     * @param expires       外链过期时间
     * @return
     */
    @ApiOperation(value = "多上传文件" , response = UpLoadResult.class)
    @PostMapping("fileList")
    public ActionResult<Object> uploadList(@ApiParam(name = "upfileList",value = "文件集合参数名称") @RequestParam("upfileList") List<MultipartFile> upfileList,
                                       @ApiParam(name = "bucketName",value = "文件桶名称") String bucketName,
                                           @ApiParam(name = "perfixName",value = "文件前缀名称") String perfixName,
                                       @ApiParam(name = "expires",value = "链接过期时间") Integer expires,
                                        @ApiParam(name = "dateFile",value = "是否需要创建时间文件夹：1是，其它否") Integer dateFile) {
        if(StringUtils.isBlank(bucketName)){
            return error(ResultCode.DATA_INPUT_EMPTY);
        }
        List<UpLoadResult> resultList = Lists.newArrayList();
        StringBuilder sbFile = new StringBuilder();
        if(StringUtils.isNotBlank(perfixName)){
            sbFile.append(perfixName).append(BucketNameConfig.FILE_SPLIT_PATH);
        }
        if(dateFile != null && dateFile == 1){
            // 创建时间文件夹
            sbFile.append(BucketNameConfig.getYear());
            sbFile.append(BucketNameConfig.FILE_SPLIT_PATH);
            sbFile.append(BucketNameConfig.getMonthAndDay());
            sbFile.append(BucketNameConfig.FILE_SPLIT_PATH);
        }
        for (MultipartFile file:upfileList) {
            String fileName = file.getOriginalFilename();
            try {
                fileCheck(file,fileName);
            } catch (Exception e) {
                return error(ResultCode.DATA_INPUT_ERROR.getValue(),e.getMessage());
            }
            fileName = sbFile.toString()+fileName;
            try {
                minioTemplate.createBucket(bucketName);
                minioTemplate.putObject(bucketName, fileName, file.getInputStream());
                UpLoadResult result = new UpLoadResult();
                if(expires == null){
                    expires = BaseConstant.ONE_DAY;
                }
                result.setFileUrl(config.getHttpUrl()+"?bucketName="+bucketName+"&objectName="+fileName+"&expires="+expires);
                result.setBucketName(bucketName);
                result.setObjectName(fileName);
                resultList.add(result);
            } catch (Exception e) {
                log.error("文件上传异常", e);
                ResultCode code =  ResultCode.FILE_SAVE_ERROR;
                return error(code);
            }
        }
        return success(resultList);
    }
 
 
    /**
     * 判断是否图片
     */
    private boolean isImage(String fileName) {
        //设置允许上传文件类型
        String suffixList = "jpg,gif,png,ico,bmp,jpeg";
        // 获取文件后缀
        String suffix = fileName.substring(fileName.lastIndexOf(".")
                + 1, fileName.length());
        if (suffixList.contains(suffix.trim().toLowerCase())) {
            return true;
        }
        return false;
    }
 
    /**
     * 验证文件大小
     * @param upfile
     * @param fileName  文件名称
     * @throws Exception
     */
    private void fileCheck(MultipartFile upfile,String fileName) throws Exception {
        Long size = upfile.getSize();
        if(isImage(fileName)){
            if(size > config.getImgSize()){
                throw new Exception("上传对图片大于："+(config.getImgSize() / 1024 / 1024) +"M限制");
            }
        }else{
            if(size > config.getFileSize()){
                throw new Exception("上传对文件大于："+(config.getFileSize() / 1024 / 1024) +"M限制");
            }
        }
    }
}
```





