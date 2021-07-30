# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

### mockjs
官网：http://mockjs.com/
github地址：https://github.com/nuysoft/Mock


### 安装与使用
安装
```
npm install mockjs
```
使用
```
// 使用 Mock
var Mock = require('mockjs')
var data = Mock.mock({
    // 属性 list 的值是一个数组，其中含有 1 到 10 个元素
    'list|1-10': [{
        // 属性 id 是一个自增数，起始值为 1，每次增 1
        'id|+1': 1
    }]
})
// 输出结果
console.log(JSON.stringify(data, null, 4))
```

### 语法规范
Mock.js 的语法规范包括两部分：

数据模板定义规范（Data Template Definition，DTD）
数据占位符定义规范（Data Placeholder Definition，DPD）

数据模板定义规范 DTD
数据模板中的每个属性由 3 部分构成：属性名、生成规则、属性值：
```
// 属性名   name
// 生成规则 rule
// 属性值   value
'name|rule': value
```
注意：

属性名 和 生成规则 之间用竖线 | 分隔。
生成规则 是可选的。
生成规则 有 7 种格式：
'name|min-max': value               生成一个大于等于 min、小于等于 max 的整数，属性值 number 只是用来确定类型。
'name|count': value
'name|min-max.dmin-dmax': value     生成一个浮点数，整数部分大于等于 min、小于等于 max，小数部分保留 dmin 到 dmax 位。
'name|min-max.dcount': value
'name|count.dmin-dmax': value       
'name|count.dcount': value          通过重复 string 生成一个字符串，重复次数等于 count。
'name|+step': value                 属性值自动加 1，初始值为 number。
生成规则 的 含义 需要依赖 属性值的类型 才能确定。（属性值：String，Number，Boolean，Object，Array，Function，RegExp）
属性值 中可以含有 @占位符。
属性值 还指定了最终值的初始值和类型。


数据占位符定义规范 DPD
占位符 只是在属性值字符串中占个位置，并不出现在最终的属性值中。

占位符 的格式为：
```
@占位符
@占位符(参数 [, 参数])
```

用 @ 来标识其后的字符串是 占位符。
占位符 引用的是 Mock.Random 中的方法。
通过 Mock.Random.extend() 来扩展自定义占位符。
占位符 也可以引用 数据模板 中的属性。
占位符 会优先引用 数据模板 中的属性。
占位符 支持 相对路径 和 绝对路径。


### Mock.mock()
Mock.mock( rurl?, rtype?, template|function( options ) )
根据数据模板生成模拟数据。

Mock.mock( template )
根据数据模板生成模拟数据。

Mock.mock( rurl, template )
记录数据模板。当拦截到匹配 rurl 的 Ajax 请求时，将根据数据模板 template 生成模拟数据，并作为响应数据返回。

Mock.mock( rurl, function( options ) )
记录用于生成响应数据的函数。当拦截到匹配 rurl 的 Ajax 请求时，函数 function(options) 将被执行，并把执行结果作为响应数据返回。

Mock.mock( rurl, rtype, template )
记录数据模板。当拦截到匹配 rurl 和 rtype 的 Ajax 请求时，将根据数据模板 template 生成模拟数据，并作为响应数据返回。

Mock.mock( rurl, rtype, function( options ) )
记录用于生成响应数据的函数。当拦截到匹配 rurl 和 rtype 的 Ajax 请求时，函数 function(options) 将被执行，并把执行结果作为响应数据返回。


### Mock.setup()
Mock.setup( settings )
配置拦截 Ajax 请求时的行为。支持的配置项有：timeout。

指定被拦截的 Ajax 请求的响应时间，单位是毫秒。值可以是正整数，例如 400，表示 400 毫秒 后才会返回响应内容；也可以是横杠 '-' 风格的字符串，例如 '200-600'，表示响应时间介于 200 和 600 毫秒之间。默认值是'10-100'。

Mock.setup({
timeout: 400
})
Mock.setup({
timeout: '200-600'
})

### Mock.Random
Basic
boolean, natural, integer, float, character, string, range
Date
date, time, datetime, now
Image
img, dataImage
Color
color, hex, rgb, rgba, hsl
Text
paragraph, sentence, word, title, cparagraph, csentence, cword, ctitle
Name
first, last, name, cfirst, clast, cname
Web
url, domain, protocol, tld, email, ip
Address
region, province, city, county, zip
Helper
capitalize, upper, lower, pick, shuffle
Miscellaneous
guid, id, increment

### Mock.valid()
Mock.valid( template, data )
校验真实数据 data 是否与数据模板 template 匹配。

template
必选。
表示数据模板，可以是对象或字符串。例如 { 'list|1-10':[{}] }、'@EMAIL'。

data
必选。
表示真实数据。

### Mock.toJSONSchema( template )
把 Mock.js 风格的数据模板 template 转换成 JSON Schema。

template
必选。

表示数据模板，可以是对象或字符串。例如 { 'list|1-10':[{}] }、'@EMAIL'。







