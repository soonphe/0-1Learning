
# plus属地会员开发文档

## 目录
- [项目介绍](#项目介绍)

- [文档相关](#文档相关)
- [业务模块](#业务模块)
- [数据库表结构](#数据库表结构)
- [模块接口说明](#模块接口说明)
    - [B站](#B站)
    - [github](#github)
    - [公众号](#公众号)
- [鸣谢其他开源项目](#鸣谢其他开源项目)


## 项目介绍


### 文档相关
> plus属地会员原始需求链接：https://www.yuque.com/docs/share/35f8f34d-f47e-45e0-a10e-8b4c64d4a3ed?#jAXO7

> 技术方案评审文档：http://cf.evs.sgcc.com.cn/pages/editpage.action?pageId=37488423

### 相关模块
- 活动相关
  - 创建活动
    - 查询活动主题
    - 查询活动所属单位
    - 查询当前用户对应的地区信息
	- 查询收款单位、开票单位
  - 活动列表查询
  - 活动详情查询
    - 活动规则查询
      - 活动奖品查询
  - 活动审核
  - 活动启用/禁用

- 活动购买
  - 购买和续费
  - 派劵
  - 购买开通记录
  
- 开票
  - 可开票记录查询
  - 发票申请
  - 发票下载

### 数据库表结构
本期涉及的表
- mkt_activity：活动表
- mkt_activity_plus：plus会员表
- plus_advance：劵预派表
- base_sys_area：全国省、市、县数据表
- sys_org_area：产权单位对应地区对应表

mkt_activity表改动点
```
新增：level 级别 1平台级 2属地级
```
主要用于判断平台级、属地级活动返回，属地级创建时要查询平台级列表，属地级详情返回时要携带关联平台级活动信息

mkt_activity_plus表改动点：
```
新增如下字段：
	//关联平台级活动ID
    private String parentActivityId;
    //关联平台级活动名称
    private String parentActivityName;
    //级别 1平台级 2属地级
    private Integer level;
    //省份Code(包含全国)
    private String provinceCode;
    //省份名称(包含全国)
    private String provinceName;
    //收款单位Code
    private String collectionCode;
    //收款单位名称
    private String collectionName;
    //开票单位Code
    private String invoiceCode;
    //开票单位名称
    private String invoiceName;
    //生效时间 非必填
    private Date effectTime;
    //生效状态 0未生效 1已生效
    private Integer effectStatus;
```

plus_advance改动点：
```
新增：activity_id 活动ID
新增：level 级别 1平台级 2属地级
```

### 模块接口说明

#### 创建活动
参见接口文档

#### 查询活动主题
参见接口文档：http://cf.evs.sgcc.com.cn/pages/viewpage.action?page Id=39813196

#### 查询活动所属单位
参见接口文档：http://cf.evs.sgcc.com.cn/pages/viewpage.action?pageId=41419014

#### 查询当前用户对应的地区信息
参见接口文档：http://cf.evs.sgcc.com.cn/pages/viewpage.action?pageId=41419100

#### 查询活动详情
参见接口文档：

#### 活动规则查询
参见接口文档：

#### 活动审核
参见接口文档：

#### 活动启用
参见接口文档：http://cf.evs.sgcc.com.cn/pages/viewpage.action?pageId=41419078
- ActivityAction/enable


#### 活动禁用
参见接口文档：http://cf.evs.sgcc.com.cn/pages/viewpage.action?pageId=41419090
- ActivityAction/disable


#### 可开票记录
参见接口文档：

发票具体逻辑实现由关联项目makert-activity-manager完成，
关联项目：http://gitlab.uniev.com.cn/wangyunliang/makert-activity-manager



ddqc-yx-web
	- 创建活动：
	- PlusRightsAction#getPlusMemberRecList 会员购买记录， http://10.10.18.168:8080/system/jqGrid/plus_rights/getPlusMemberRecList?randomTimeTemp=1655103716359
	- 新增权益 PlusRightsAction#addPlusRights
	- 启动/禁用权益 PlusRightsAction#changePlusRightsState
	- 查询待审核列表： http://10.10.18.168:8080/system/jqGrid/approval/getUnderApprovalRecords?approval.bsnsType=YXHDSH&creatorOuCode=3&randomTimeTemp=1655085966747
	根据登录用户ID查询角色，根据传参bsnsType查询业务列表，匹配到对应的model class，用户bsnsId等参数匹配查询model
	bsnsType放在package sunbox.core.enumer;——BsnsType，存储对应的name，code，class，url（MktActivityApproResultItem）
	对应的审核表base_sys_approval
	- 审核详情：http://10.10.18.168:8080/system/json/approval/getApprovalRecords.action?approval.bsnsId=17556&bsnsType=YXHDSH&randomTimeTemp=1655085706477
加载活动：http://10.10.18.168:8080/system/json/activity/loadActivity?randomTimeTemp=1655085707434




### 前端界面开发
需求：审核页面开发（级别，收款单位，开票单位，管理平台级活动，年卡金额）

审核列表：activity_approve.js，点击审核加载：detailUrl=/system/activity/activity_detail_plus

审核详情界面：approve_detail.js，加载detailUrl的iFrame，实际加载的页面就是：templates/system/activity/activity_detail_plus.html


---

## 补充信息

### 数据库信息
数据库连接信息在filters中，dev/test/pro

连接信息加密在sunbox——sunbox-base——CoderTest

数据库连接信息：
```
192.168.104.43:3306
账户：sunbox
```


