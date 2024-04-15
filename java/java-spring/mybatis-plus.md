# mybatis-plus

## 实现模块


### CRUD接口
Service CRUD 接口
Save
SaveOrUpdate
Remove
Update
Get
List
Page
Count
Chain

Mapper CRUD 接口
Insert
Delete
Update
Select

mapper 层 选装件
AlwaysUpdateSomeColumnById
insertBatchSomeColumn
logicDeleteByIdWithFill

ActiveRecord 模式
操作步骤：

SimpleQuery 工具类
keyMap
map
group
list

Db类

### 条件构造器
AbstractWrapper
allEq
eq
ne
gt
ge
lt
le
between
notBetween
like
notLike
likeLeft
likeRight
notLikeLeft
notLikeRight
isNull
isNotNull
in
notIn
inSql
notInSql
groupBy
orderByAsc
orderByDesc
orderBy
having
func
or
and
nested
apply
last
exists
notExists

QueryWrapper
select

UpdateWrapper
set
setSql
lambda

### 常用操作
普通查询
```
service.selectListByWrapper(new QueryWrapper<TmAdvert>()
                .lambda().like(TmAdvert::getName, "毛")
               .or(e -> e.like(TmAdvert::getName, "张")));
               
new QueryWrapper<TmAdvert>().like("name", "毛")  //普通查询
new QueryWrapper<TmAdvert>().lambda().like(TmAdvert::getName, "毛")  //同上
new LambdaQueryWrapper<TmAdvert>().like(TmAdvert::getName, "毛")   //同上
Wrappers.<TmAdvert>lambdaQuery().like(TmAdvert::getName, "毛") //同上
new QueryWrapper<WmsWalletFtHistory>().lambda()
                        .eq(WmsWalletFtHistory::getUid, uid)
                        .eq(type > 0, TmAdvert::getType, type); //匹配携带条件
                        .orderByDesc(TmAdvert::getId))).getRecords();


new QueryWrapper<TmAdvert>().inSql("name", "select id from role where id = 2")  //携带子查询
new QueryWrapper<TmAdvert>().nested(i -> i.eq("role_id", 2L).or().eq("role_id", 3L))   //嵌套查询
```

分页查询
```
IPage<TmAdvert> page1 = service.selectByAdvert(page, baseVo);

IPage<TmAdvert> page = service.page(new Page<>(0, 12),
```


### 分库处理
分库处理有两种：
- 使用接口 ：@DS 切换数据源。
- 代码切换：DynamicDataSourceContextHolder
```
public final class DynamicDataSourceContextHolder {

    /**
     * 为什么要用链表存储(准确的是栈) * <pre> * 为了支持嵌套切换，如ABC三个service都是不同的数据源 * 其中A的某个业务要调B的方法，B的方法需要调用C的方法。一级一级调用切换，形成了链。 * 传统的只设置当前线程的方式不能满足此业务需求，必须模拟栈，后进先出。 * </pre>
     */
    @SuppressWarnings("unchecked")
    private static final ThreadLocal<Deque<String>> LOOKUP_KEY_HOLDER = new ThreadLocal() {
        @Override
        protected Object initialValue() {
            return new ArrayDeque();
        }
    };

    private DynamicDataSourceContextHolder() {
    }

    /**
     * 获得当前线程数据源
     * * @return 数据源名称
     */
    public static String peek() {
        return LOOKUP_KEY_HOLDER.get().peek();
    }

    /**
     * 设置当前线程数据源 * <p> * 如非必要不要手动调用，调用后确保最终清除 * </p> * * @param ds 数据源名称
     */
    public static void push(String ds) {
        LOOKUP_KEY_HOLDER.get().push(StringUtils.isEmpty(ds) ? "" : ds);
    }

    /**
     * 清空当前线程数据源 * <p> * 如果当前线程是连续切换数据源 只会移除掉当前线程的数据源名称 * </p>
     */
    public static void poll() {
        Deque<String> deque = LOOKUP_KEY_HOLDER.get();
        deque.poll();
        if (deque.isEmpty()) {
            LOOKUP_KEY_HOLDER.remove();
        }
    }

    /**
     * 强制清空本地线程 * <p> * 防止内存泄漏，如手动调用了push可调用此方法确保清除 * </p>
     */
    public static void clear() {
        LOOKUP_KEY_HOLDER.remove();
    }
}
```
首先通过调用改类的push方法将要传入的dbName，下面写你的业务的逻辑，完成后，最好再调用改类的poll方法或者clear方法（需要注意的是这个poll方法和clear方法是一定要调用的，最好写再final里面）
这样就可以通过代码来手动切换数据源啦。