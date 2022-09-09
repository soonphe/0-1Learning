# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Jackson和Gson和fastjson
- jackson：反射+反射缓存、良好的stream支持、高效的内存管理
- fastjson：
  - jvm虚拟机：通过ASM库运行时生成parser字节码，支持的field不能超过200个。参考：FastJson使用ASM反序列化。
  - android虚拟机：反射的方式。
- gson：反射+反射缓存、支持部分stream、内存性能较差（gc问题）。

### Jackson
依赖
```
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.9.6</version>
</dependency>
```
使用代码
```
private static ObjectMapper mapper=new ObjectMapper();
String userJson = "{\"name\": \"jack\",\"age\": \"20\"}";

//JSON字符串->Java对象
User user = objectMapper.readValue(userJson, User.class);
//JSON数组字符串->Java对象数组
User[] users = objectMapper.readValue(usersJson, User[].class);

//Java对象转JSON字符串
String jack = objectMapper.writeValueAsString(new User("jack", 20));

//转二进制数组
byte[] jacks = objectMapper.writeValueAsBytes(new User("jack", 20));
```


### Gson
```
Gson gson = new Gson();
Person person = gson.fromJson(jsonData, Person.class);
```

### fastjson
```
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>fastjson</artifactId>
                <version>${fastjson.version}</version>
            </dependency>
```

JSONObject解析
```
例：
{
  "code": 200,
  "message": "操作成功！",
  "data": {[
    "address": "TEfgYPLn3Z8RJ3mTJiwwHtkukZspUWPreF",
    "list": []
  ]}
}
//java对象转json字符串
String result = JSON.toJSONString(dingMsg);
//截取字符串
String realResult = result.substring(4, result.length());
//字符串转换JSONObject 对象：
JSONObject data = JSON.parseObject(realResult);
//解析对象：
Class class = JSON.parseObject(realResult , xxx.class);
//解析对象数组
JSONObject jsonObject = data.getJSONObject("data");
List<Class> trxToCNYRate = JSONObject.parseArray(jsonObject.toJSONString(), Class.class);

//获取JSONObject中的对象并解析
JSONObject jsonObject = data.getJSONObject("data");
Class class = JSON.parseObject(jsonObject.toJSONString(), xxx.class);
//解析JSONObject中的string
String obj = data.getJSONObject("data").getString("address");
```

