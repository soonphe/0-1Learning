# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## SQL注入

sql注入验证
一般使用单引号‘ 测试SQL注入
用？占位符可以避免SQL注入
PreparedStatement st = conn.prepareStatement(sql);
st.setString(1, "儿童"); // 参数赋值  ——这里做了字符转义


PreparedStatement也有漏洞：
比如Like查询中：
用户直接输入了"%儿童%"，整个查询的意思就变了，变成包含查询。实际上不用这么麻烦，用户什么都不输入，或者只输入一个%，都可以改变原意。
     虽然此种SQL注入危害不大，但这种查询会耗尽系统资源，从而演化成拒绝服务攻击。
     那如何防范呢？笔者能想到的方案如下：
          ·直接拼接SQL语句，然后自己实现所有的转义操作。这种方法比较麻烦，而且很可能没有PreparedStatement做的好，造成其他更大的漏洞，不推荐。
          ·直接简单暴力的过滤掉%。笔者觉得这方案不错，如果没有严格的限制，随便用户怎么输入，既然有限制了，就干脆严格一些，干脆不让用户搜索%，推荐。


