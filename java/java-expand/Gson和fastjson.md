


### Gson


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

