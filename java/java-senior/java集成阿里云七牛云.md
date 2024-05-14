## java集成阿里云七牛云

### 阿里云
通用依赖
```
		<!-- 阿里云文件服务 -->
		<dependency>
			<groupId>com.aliyun.oss</groupId>
			<artifactId>aliyun-sdk-oss</artifactId>
			<version>2.2.3</version>
		</dependency>
```
参考配置
```

import cn.hutool.core.date.DateUtil;
import cn.hutool.http.HttpUtil;
import com.aliyun.oss.OSSClient;
import com.aliyun.oss.model.PutObjectRequest;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import java.io.ByteArrayInputStream;
import java.net.URL;
import java.util.Date;

/**
 * Oss上传工具类
 *
 * @author soonphe
 * @since 1.0
 */
@Slf4j
@Component
public class OssUtil {
    @Value("${oss.endpoint}")
    private String endpoint;

    @Value("${oss.accessKeyId}")
    private String accessKeyId;

    @Value("${oss.accessKeySecret}")
    private String accessKeySecret;

    @Value("${oss.bucketName}")
    private String bucketName;

    /**
     * 上传文件流
     * @param fileContent
     * @param objectName
     * @return
     */
    public String uploadTxt(byte[] fileContent, String objectName) {
        String downLoadUrl = "";
        try {
            //文件上传
            OSSClient ossClient = new OSSClient(endpoint, accessKeyId, accessKeySecret);
            PutObjectRequest putObjectRequest = new PutObjectRequest(bucketName, objectName, new ByteArrayInputStream(fileContent));
            ossClient.putObject(putObjectRequest);
            //有效期
            Date expiration = DateUtil.offsetMonth(DateUtil.date(), 12);
            //获取下载链接
            URL fileUrl = ossClient.generatePresignedUrl(bucketName, objectName, expiration);
            downLoadUrl = fileUrl.toString();
        } catch (Exception e) {
            log.error("上传oss时异常。", e);
        }
        return downLoadUrl;
    }

    /**
     * 判断Oss文件是否存在
     * @param objectName
     * @return
     */
    public boolean hasFile(String objectName){
        OSSClient ossClient = new OSSClient(endpoint, accessKeyId, accessKeySecret);
        return ossClient.doesObjectExist(bucketName, objectName);
    }

    /**
     * 删除oss文件
     * @param objectName
     * @return
     */
    public void deleteFile(String objectName){
        OSSClient ossClient = new OSSClient(endpoint, accessKeyId, accessKeySecret);
        ossClient.deleteObject(bucketName, objectName);
    }


    public static void main(String[] args) {
        OSSClient ossClient = new OSSClient("oss-cn-beijing.aliyuncs.com", "xxx", "xxx");
        //测试上传
//        PutObjectRequest putObjectRequest = new PutObjectRequest("bucket-oss-test", "test/test.txt", new ByteArrayInputStream("abcabasdfasfdsafsafsa".getBytes()));
//        ossClient.putObject(putObjectRequest);
//        //有效期
//        Date expiration = DateUtil.offsetMonth(DateUtil.date(), 12);

//        //获取下载链接
//        URL fileUrl = ossClient.generatePresignedUrl("bucket-oss-test", "test/test.txt", expiration);
//        String downLoadUrl = fileUrl.toString();
//        System.out.println(downLoadUrl);

        //查看文件是否存在
        System.out.println("==="+ossClient.doesObjectExist("bucket-oss-test", "test/test.txt"));

        //直接下载文件（适用于已存在下载地址，直接下载）
        String downLoadUrl = "http://bucket-oss-test.oss-cn-beijing.aliyuncs.com/test/test.txt?Expires=1723106932&OSSAccessKeyId=LTAI5tLVQ9UmRYPSfnsqd5kz&Signature=baC/SRvY%2BqsNt5ltfkddoKAk53w%3D";
        byte[] bytes = HttpUtil.downloadBytes(downLoadUrl);
        System.out.println("==="+new ByteArrayInputStream(bytes));
        String s = new String(bytes);
        System.out.println("==="+s);
//        FileUtil.getReader(bytes,"U");
//        FileUtil.getInputStream("")
          //通过ossClient.getObject获取流下载
//        OSSObject object = ossClient.getObject("qxy-oss-test", "test/test.txt");
//        if (Objects.nonNull(object)){
//            System.out.println("true");
//            object.getObjectContent();
//        }else {
//            System.out.println("false");
//        }
    }


}
```

#### 阿里云oss
参考代码：
```

```

#### 阿里云短信
#### 阿里云推送

### 七牛云
需要开通七牛开发者帐号，如果已有账号，直接登录七牛开发者后台，点击这里🔗查看 Access Key 和 Secret Key

通用依赖
```
		<dependency>
			<groupId>com.qiniu</groupId>
			<artifactId>qiniu-java-sdk</artifactId>
			<version>[7.14.0, 7.14.99]</version>
		</dependency>
```
通用配置文件
```
qiniu:
  qiniuAccessKey: xxx
  qiniuSecretKey: xxx
  #监控命名空间
  namespaceId: statictest
  #监控模板
  recordTemplateId: 2xenzxij11gq6
```

#### 七牛云OSS
对象存储-空间管理-创建空间（需要配置域名，不配置只有30天临时域名）

java SDK文档：https://developer.qiniu.com/kodo/1239/java#simple-uptoken
```
package org.starry.ws;

import com.google.gson.Gson;
import com.qiniu.common.QiniuException;
import com.qiniu.http.Response;
import com.qiniu.storage.*;
import com.qiniu.storage.model.DefaultPutRet;
import com.qiniu.util.Auth;
import com.qiniu.util.StringMap;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.AmqpTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;


/**
 * 七牛云Oss工具类
 *
 * @author soonphe
 * @since 1.0
 */
@Slf4j
@Component
public class OssUtil {

    @Value("${qiniu.qiniuAccessKey:eBveY-tK55bg-hT1am_0AkZpfnsOrGXqbgkzRykL}")
    private String qiniuAccessKey;
    @Value("${qiniu.qiniuSecretKey:JRR-WtWvv90rCoCv_4eFWnvTRjIaNMGJcd8pbf8X}")
    private String qiniuSecretKey;

    /**
     * 客户端上传凭证
     * 默认情况下，文件上传到七牛之后，在没有设置returnBody或者回调相关的参数情况下，七牛返回给上传端的回复格式为hash和key，例如：{"hash":"Ftgm-CkWePC9fzMBTRNmPMhGBcSV","key":"qiniu.jpg"}
     *
     * @param bucket bucket名称
     * @param key    文件名称 覆盖上传除了需要简单上传所需要的信息之外，还需要想进行覆盖的文件名称，这个文件名称同时是客户端上传代码中指定的文件名，两者必须一致。
     * @return
     */
    public String ossUploadToken(String bucket,String key) {
        Auth auth = Auth.create(qiniuAccessKey, qiniuSecretKey);
        String upToken = null;
        if (StringUtils.isEmpty(key)) {
            upToken = auth.uploadToken(bucket);
        }else{
            upToken = auth.uploadToken(bucket, key);
        }
        log.info("===ossUploadToken:{}===", upToken);
        return upToken;
    }

    /**
     * 客户端上传凭证(带回调)
     * @param bucket
     * @param key
     * @return
     */
    public String ossUploadTokenCallback(String bucket,String key) {
        Auth auth = Auth.create(qiniuAccessKey, qiniuSecretKey);
        String upToken = null;
        StringMap putPolicy = new StringMap();
        putPolicy.put("callbackUrl", "http://api.example.com/qiniu/upload/callback");
        putPolicy.put("callbackBody", "{\"key\":\"$(key)\",\"hash\":\"$(etag)\",\"bucket\":\"$(bucket)\",\"fsize\":$(fsize)}");
        putPolicy.put("callbackBodyType", "application/json");
        long expireSeconds = 3600;
        if (StringUtils.isEmpty(key)) {
            upToken = auth.uploadToken(bucket);
        }else{
            upToken = auth.uploadToken(bucket, key, expireSeconds, putPolicy);
        }
        log.info("===ossUploadToken:{}===", upToken);
        return upToken;
    }

    /**
     * oss下载文件
     * 私有空间才需要Auth.create，公有空间不需要，直接url.buildURL();
     *
     * @param domain 用户 bucket 绑定的下载域名 eg: mock.qiniu.com【必须】
     * @param useHttps 是否使用 https【必须】
     * @param key 下载资源在七牛云存储的 key【必须】
     * @return
     */
    public String ossDownload(String domain, boolean useHttps, String key) {
        DownloadUrl url = new DownloadUrl(domain, useHttps, key);
//        url.setAttname(attname) // 配置 attname
//                .setFop(fop) // 配置 fop
//                .setStyle(style, styleSeparator, styleParam) // 配置 style
        // 带有效期
        long expireInSeconds = 3600;//1小时，可以自定义链接过期时间
        long deadline = System.currentTimeMillis()/1000 + expireInSeconds;
        Auth auth = Auth.create(qiniuAccessKey, qiniuSecretKey);
        String urlString = null;
        try {
            urlString = url.buildURL(auth, deadline);
        } catch (QiniuException e) {
            throw new RuntimeException(e);
        }
        log.info("===ossDownload:{}===", urlString);
        return urlString;
    }

    /**
     * oss删除文件
        * @param bucket bucket名称
        * @param key 文件名称
        * @return
        */
    public void ossDelete(String bucket, String key) {
        //构造一个带指定 Region 对象的配置类
        Configuration cfg = new Configuration(Region.region0());
        Auth auth = Auth.create(qiniuAccessKey, qiniuSecretKey);
        BucketManager bucketManager = new BucketManager(auth, cfg);
        try {
            bucketManager.delete(bucket, key);
        } catch (QiniuException e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * oss更新文件有效期
     * @param bucket bucket名称
     * @param key 文件名称
     * @param days 过期天数
     * @return
     */
    public void ossDeleteAfterDays(String bucket, String key, int days) {
        //构造一个带指定 Region 对象的配置类
        Configuration cfg = new Configuration(Region.region0());
        Auth auth = Auth.create(qiniuAccessKey, qiniuSecretKey);
        BucketManager bucketManager = new BucketManager(auth, cfg);
        try {
            bucketManager.deleteAfterDays(bucket, key, days);
        } catch (QiniuException e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * oss服务端直传文件
     * @param bucket bucket名称
     * @param key 文件名称 默认不指定key的情况下，以文件内容的hash值作为文件名
     * @param localFilePath 本地文件路径
     *
     * 其中关于Region对象和机房的关系如下：
     * 华东	Region.region0(), Region.huadong()
     * 华北	Region.region1(), Region.huabei()
     * 华南	Region.region2(), Region.huanan()
     * 北美	Region.regionNa0(), Region.beimei()
     * 东南亚	Region.regionAs0(), Region.xinjiapo()
     * 若不指定 Region 或 Region.autoRegion() ，则会使用 自动判断 区域，使用相应域名处理。如果可以明确 区域 的话，最好指定固定区域，这样可以少一步网络请求，少一步出错的可能。
     * @return
     */
    public void ossUpload(String bucket, String key, String localFilePath) {
        //构造一个带指定 Region 对象的配置类
        Configuration cfg = new Configuration(Region.region0());
        cfg.resumableUploadAPIVersion = Configuration.ResumableUploadAPIVersion.V2;// 指定分片上传版本
        //...其他参数参考类注释
        UploadManager uploadManager = new UploadManager(cfg);
        Auth auth = Auth.create(qiniuAccessKey, qiniuSecretKey);
        String upToken = auth.uploadToken(bucket);
        try {
            Response response = uploadManager.put(localFilePath, key, upToken);
            //解析上传成功的结果
            DefaultPutRet putRet = new Gson().fromJson(response.bodyString(), DefaultPutRet.class);
            System.out.println(putRet.key);
            System.out.println(putRet.hash);
        } catch (QiniuException ex) {
            ex.printStackTrace();
            if (ex.response != null) {
                System.err.println(ex.response);
                try {
                    String body = ex.response.toString();
                    System.err.println(body);
                } catch (Exception ignored) {
                }
            }
        }
    }
}
```

#### 七牛云云视频监控
鉴权：Auth auth = Auth.create(qiniuAccessKey, qiniuSecretKey);
构造流管理器：StreamManager streamManager = new StreamManager(auth);
查询流信息： Response queryStream = streamManager.queryStream(namespaceId, stream.getStreamID());
禁用推流：streamManager.disableStream(namespaceId, stream.getStreamID());
启用推流：streamManager.enableStream(namespaceId, stream.getStreamID());
创建流地址：Response stream1 = streamManager.createStream(namespaceId, stream);
更新流信息：
PatchOperation[] patchOperation = {new PatchOperation("replace", "recordTemplateId", recordTemplateId)};
streamManager.updateStream(namespaceId, streamID, patchOperation);

获取流地址:
- 创建空间时，需要选择是静态地址模式(需要配置域名,且ICP备案)或动态地址模式，请根据空间设置的模式选择对应的接口获取流地址。
```
静态模式获取流地址：qvs域名系统用于实时的视频推流或播放,与客户在对象存储里配置的域名(视频回看)是独立的。
拼装格式:
1.rtmp，推流和播放格式:“rtmp://推流或播放域名:2045/空间id/流id”。
2.hls,http格式"http://播放域名:1240/空间id/流id.m3u8",https格式"https://播放域名:447/空间id/流id.m3u8"。

代码：
StaticLiveRoute staticLiveRoute = new StaticLiveRoute("qvs-publish.jiaxiao.fun", "publishRtmp");
response = streamManager.staticPublishPlayURL(namespaceId, stream.getStreamID(), staticLiveRoute);

动态模式获取流地址：无需配置域名，流地址全部为IP形式，需通过API获取实时的流地址。适用于：1.测试阶段，快速验证。2.不想配置域名，能接受流地址通过API获取。3. http的播放环境。如果是https环境,务必使用上面的静态地址
代码：
DynamicLiveRoute dynamicLiveRoute = new DynamicLiveRoute("推流端IP地址", "拉流端ip地址");
streamManager.dynamicPublishPlayURL(namespaceId, stream.getStreamID(), dynamicLiveRoute);
```

推流代码示例：
```
/**
     * 创建推流地址
     * @param deviceId 设备ID
     * @param carNum 描述
     * @param channel channel
     * @return
     */
    public ResponseResult liveAndGetUrl(String deviceId, String carNum,String channel) {
        Auth auth = Auth.create(qiniuAccessKey, qiniuSecretKey);
        StreamManager streamManager = new StreamManager(auth);
        Response response = null;
        ResponseResult result = new ResponseResult();
        String streamID = deviceId;
        if (!StringUtils.isEmpty(channel)){
            streamID = deviceId+"_"+channel;
        }
        Stream stream = new Stream(streamID);
        stream.setDesc(carNum);
        stream.setRecordTemplateId(recordTemplateId);
//        //判断当前车辆是否有学员正在训练
//        String str1 = RedisUtil.get(PC_MODEL_ONLINE_CAR_REDIS_KEY + deviceId);
//        if (org.apache.commons.lang3.StringUtils.isNotBlank(str1)) {
//            Record record = JSON.parseObject(str1, Record.class);
//            if (Objects.nonNull(record) && Objects.nonNull(record.getStudentId())) {
//                logger.info("===当前学员:{}，推流添加录制模版ID：{}===", record.getStudentName(),recordTemplateId);
//                stream.setRecordTemplateId(recordTemplateId);
//            }
//        }
        try {
            Response stream1 = streamManager.queryStream(namespaceId, stream.getStreamID());
            StreamInfoVo streamInfoVo = JSON.parseObject(stream1.bodyString(), StreamInfoVo.class);
            logger.info("==查询当前流：{}，状态：{},信息：{}===", stream.getStreamID(), streamInfoVo.getStatus(), JSON.toJSONString(streamInfoVo));
            if (StringUtils.isEmpty(streamInfoVo.getRecordTemplateId())||!recordTemplateId.equals(streamInfoVo.getRecordTemplateId())) {
                PatchOperation[] patchOperation = {new PatchOperation("replace", "recordTemplateId", recordTemplateId)};
                streamManager.updateStream(namespaceId, streamID, patchOperation);
            }
            if (streamInfoVo.getStatus()){
                ReqObj reqObj = new ReqObj();
                reqObj.setStreamState(1);
                reqObj.setDeviceId(deviceId);
                reqObj.setChannel(channel);
                result.setCode(1);
                result.setMsgType(MSG_TYPE_PULL_EXIST.getCode().toString());
                result.setMsg(MSG_TYPE_PULL_EXIST.getCode().toString());
                result.setData(reqObj);
                return result;
            }
            JSONObject jsonObject = JSONObject.parseObject(stream1.bodyString());
            boolean disabled = (boolean)jsonObject.get("disabled");
            if(disabled){
                Response response1 = streamManager.enableStream(namespaceId, stream.getStreamID());
                logger.info("===启用推流，结果：{}===", JSON.toJSONString(response1));
            }
        } catch (QiniuException e) {
            if (e.getMessage().contains("612300")) {
                try {
                    Response stream1 = streamManager.createStream(namespaceId, stream);
                    logger.info("===当前流不存在，创建流地址，结果：{}===", JSON.toJSONString(stream1));
                } catch (QiniuException e1) {
                    result.setCode(-1);
                    result.setMsg("创建流地址失败");
                    return result;
                }
            }else{
                logger.info(e.getMessage());
            }
        }
        try {
            StaticLiveRoute staticLiveRoute = new StaticLiveRoute("qvs-publish.jiaxiao.fun", "publishRtmp");
            response = streamManager.staticPublishPlayURL(namespaceId, stream.getStreamID(), staticLiveRoute);
            JSONObject jsonObject = JSONObject.parseObject(response.bodyString());
            String publishUrl = (String) jsonObject.get("publishUrl");
            logger.info("===deviceId：{},channel:{},获取到的推流地址为：{}===",deviceId,channel,publishUrl);
            // 推送mq
            JSONObject jsonObjectSend = new JSONObject();
            jsonObjectSend.put("msgId","S02");
            jsonObjectSend.put("stream",publishUrl);
            jsonObjectSend.put("deviceId",deviceId);
            jsonObjectSend.put("channel",channel);
            amqpTemplate.convertAndSend("wuhan_yg_push_stream_down", JSON.toJSONString(jsonObjectSend));
//            WebSocketSession socketSession = deviceList.get(carNum);
//            socketSession.sendMessage(new TextMessage(jsonObjectSend.toJSONString()));
//            staticLiveRoute = new StaticLiveRoute("qvs-live.qvs.bdsana.com", "liveHls");
//            response = streamManager.staticPublishPlayURL(namespaceId, stream.getStreamID(), staticLiveRoute);
//            jsonObject = JSONObject.parseObject(response.bodyString());
//            Object playUrl = ((Map)jsonObject.get("playUrls")).get("hls");
            result.setCode(1);
            result.setMsgType(MSG_TYPE_PULL.getCode().toString());
            result.setMsg("创建流地址成功");
            return result;
        } catch (QiniuException e1) {
            logger.error("===获取流地址失败，e:{}===", e1.getMessage());
            result.setCode(-1);
            result.setMsg("获取流地址失败");
            return result;
        } catch (IOException e) {
            logger.error("===推流失败，e:{}===", e.getMessage());
            result.setCode(-1);
            result.setMsg("推流失败");
            return result;
        }
    }
```

**七牛云录制相关方法：**
控制台配置录制模板：视频监控-模板管理-添加录制模板-选择实时录制(推流即录制)和按需录制(推流同时需要调用启动录制接口)-选择存储空间(即配置的OSS)-选择存储周期(天)
存储规则：
TS:record/ts/${namespaceId}/${streamId}/${startMs}-${endMs}.ts
M3U8:record/${namespaceId}/${streamId}/${startMs}-${endMs}-${duration}.m3u8

录制分为全局录制和按需录制：
- 全局录制：在创建流地址时，设置recordTemplateId，全局录制模板ID，全局录制模板ID可以在七牛云控制台配置
- 按需录制：在推流时，指定recordTemplateId，按需录制模板ID，同时推流时需要调用启动录制接口
```
启动按需录制：
1. Response startRecord(String namespaceId, String streamId) throws QiniuException
停止按需录制：
1. Response stopRecord(String namespaceId, String streamId) throws QiniuException
删除录制片段：
1. Response deleteStreamRecordHistories(String namespaceId, String streamId, String[] files) throws QiniuException
录制视频片段合并：
1. Response recordClipsSaveas(String namespaceId, String streamId, String fname, String format, int start, int end, boolean deleteTs, String pipeline, String notifyUrl, int deleteAfterDays) throws QiniuException
录制回放：
1. Response recordsPlayback(String namespaceId, String streamId, int start, int end) throws QiniuException
查询录制记录：
1. Response queryStreamRecordHistories(String namespaceId, String streamId, int start, int end, int line, String marker) throws QiniuException
2. Response queryStreamRecordHistories(String namespaceId, String streamId, int start, int end, int line, String marker, String format) throws QiniuException
```

**七牛云常见错误返参**
七牛返参格式：info[method+url][ResponseInfo固定值status、reqId等][bodyString]
```
{"code":400207,"error":"invalid record type,0:no record, 1:live record,2:ondemand record"}
{"code":612504,"error":"stream has been offline"}
{"code":612500,"error":"record template not configed for this stream"}
{"code":612503,"error":"bucket download domain not configed"}


{"code":612303,"error":"stream is disabled"}

```
示例： 接口返回播放地址：http://sabn07wny.hn-bkt.clouddn.com/record/playback/statictest/T000000000000064_in/1710259200-1710431975.m3u8?pm3u8/0/expire/7200\u0026e=1710400806\u0026token=eBveY-tK55bg-hT1am_0AkZpfnsOrGXqbgkzRykL:jW9FT67xeT6p77aayABu9ZvRP1g


### OKhttp工具类



