# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Stream API

### 简介

Java 8 引入了全新的 Stream API，可以使用声明的方式来处理数据，极大地方便了集合操作，让我们可以使用更少的代码来实现更为复杂的逻辑

### 什么是Stream

Stream（流）是一个来自数据源的元素队列，它可以支持聚合操作。
* 数据源：流的数据来源，构造Stream对象的数据源，比如通过一个List来构造Stream对象，这个List就是数据源；
* 聚合操作：对Stream对象进行处理后使得Stream对象返回指定规则数据的操作称之为聚合操作，比如filter、map、limit、sorted等都是聚合操作。

### 用法
例:聚合操作：
Stream对象的创建
Stream对象分为两种，一种串行的流对象，一种并行的流对象。
~~~~
// permissionList指所有权限列表
// 为集合创建串行流对象
Stream<UmsPermission> stream = permissionList.stream();
// 为集合创建并行流对象
tream<UmsPermission> parallelStream = permissionList.parallelStream();
~~~~

### filter
对Stream中的元素进行过滤操作，当设置条件返回true时返回相应元素。

~~~~
// 获取权限类型为目录的权限
List<UmsPermission> dirList = permissionList.stream()
    .filter(permission -> permission.getType() == 0)
    .collect(Collectors.toList());
~~~~

### map
对Stream中的元素进行转换处理后获取。比如可以将UmsPermission对象转换成Long对象。 我们经常会有这样的需求：需要把某些对象的id提取出来，然后根据这些id去查询其他对象，这时可以使用此方法。
~~~~
// 获取所有权限的id组成的集合
List<Long> idList = permissionList.stream()
    .map(permission -> permission.getId())
    .collect(Collectors.toList());
~~~~

### limit
从Stream中获取指定数量的元素。
~~~~
// 获取前5个权限对象组成的集合
List<UmsPermission> firstFiveList = permissionList.stream()
    .limit(5)
    .collect(Collectors.toList());
~~~~

### count
仅获取Stream中元素的个数。
~~~~
// count操作：获取所有目录权限的个数
long dirPermissionCount = permissionList.stream()
    .filter(permission -> permission.getType() == 0)
    .count();
~~~~

### sorted
对Stream中元素按指定规则进行排序。
~~~~
// 将所有权限按先目录后菜单再按钮的顺序排序
List<UmsPermission> sortedList = permissionList.stream()
    .sorted((permission1,permission2)->{return permission1.getType().compareTo(permission2.getType());})
    .collect(Collectors.toList());
~~~~

### skip
跳过指定个数的Stream中元素，获取后面的元素。
~~~~
// 跳过前5个元素，返回后面的
List<UmsPermission> skipList = permissionList.stream()
    .skip(5)
    .collect(Collectors.toList());
~~~~

### 用collect方法将List转成map
有时候我们需要反复对List中的对象根据id进行查询，我们可以先把该List转换为以id为key的map结构，然后再通过map.get(id)来获取对象，这样比较方便。
~~~~
// 将权限列表以id为key，以权限对象为值转换成map
Map<Long, UmsPermission> permissionMap = permissionList.stream()
    .collect(Collectors.toMap(permission -> permission.getId(), permission -> permission));
~~~~