# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Stream API 和 Function

java8推出了很多更新，但是在编码方面，总结起来我认为有三大块：Lambda语法，Stream包，Function包。

### 1.Lambda语法

Lambda表达式可分为表达式lambda和语句lambda

基本语法:
表达式lambda：表达式位于 => 运算符右侧的lambda表达式称为表达式lambda
(parameters) -> expression  //表达式lambda

语句lambda：=> 运算符右侧是一个语句块，语句包含在大括号中
(parameters) ->{ statements; }  //语句lambda

给委托变量赋值时，表达式lambda和语句lambda写法不一样，但是最后编译器都生成一个方法。
还有个不同点，表达式lambda可以转换为类型Expression<T>的表达式树，而语句lambda不可以


### 2.Stream简介

Java 8 引入了全新的 Stream API，可以使用声明的方式来处理数据，极大地方便了集合操作，让我们可以使用更少的代码来实现更为复杂的逻辑

#### 什么是Stream

Stream（流）是一个来自数据源的元素队列，它可以支持聚合操作。
* 数据源：流的数据来源，构造Stream对象的数据源，比如通过一个List来构造Stream对象，这个List就是数据源；
* 聚合操作：对Stream对象进行处理后使得Stream对象返回指定规则数据的操作称之为聚合操作，比如filter、map、limit、sorted等都是聚合操作。

Java 8 中的 Stream 是对集合（Collection）对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作（aggregate operation），或者大批量数据操作 (bulk data operation)。Stream API 借助于同样新出现的 Lambda 表达式，极大的提高编程效率和程序可读性。

#### 常见用法
这些方法的入参是需要一系列的比较器（Ccompare）、Boolean返回器（Predicate）、Function等工具的，这些就是下面的Function包的内容了。

例:聚合操作：
Stream对象的创建
Stream对象分为两种，一种串行的流对象，一种并行的流对象。
```
// permissionList指所有权限列表
// 为集合创建串行流对象
Stream<UmsPermission> stream = permissionList.stream();
// 为集合创建并行流对象
tream<UmsPermission> parallelStream = permissionList.parallelStream();
```

#### filter
    Stream<T> filter(Predicate<? super T> predicate);

对Stream中的元素进行过滤操作，当设置条件返回true时返回相应元素。

```
// 获取权限类型为目录的权限
List<UmsPermission> dirList = permissionList.stream()
    .filter(permission -> permission.getType() == 0)
    .collect(Collectors.toList());
```

#### map
    <R> Stream<R> map(Function<? super T, ? extends R> mapper);

对Stream中的元素进行转换处理后获取。比如可以将UmsPermission对象转换成Long对象。 我们经常会有这样的需求：需要把某些对象的id提取出来，然后根据这些id去查询其他对象，这时可以使用此方法。
```
// 获取所有权限的id组成的集合
List<Long> idList = permissionList.stream()
    .map(permission -> permission.getId())
    .collect(Collectors.toList());

Stream.of("My", "Java", "My", "Life!")
        .map(String::toLowerCase)//转化为小写
        .map(Student::new);//接着以名字为参数转化Student流
        
//list转逗号分隔字符串
String.join(",", list.stream().map(String::valueOf).collect(Collectors.toList()));	

//list取某字段转逗号分隔字符串
records.stream().map(permission -> permission.getDeviceId()).map(String::valueOf).collect(Collectors.joining(","));

//map合并：
Map<String, Object> offlineMap = RedisUtil.getMap(PC_MODEL_OFFLINE_CAR_REDIS_KEY);
offlineMap.forEach((k, v) -> recordOfflineMap.merge(k, v, (v1, v2) -> v2));

```

#### flatMap
    <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);

```
Stream.of(Arrays.asList("My"), Arrays.asList("Java", "My", "Life!"))//此时，这是List流，里面是两个List对象，List里面才是字符串数据
        .flatMap(list -> list.stream())//扁平化，即把List流转化为字符串流，这里也可以使用方法引用：.flatMap(Collection::stream)
        .forEach(System.out::println);
```
说明：
Map一般用于对原始的参数进行加工处理，返回值还是基本的类型。
flatMap一般用于返回结果数目未知时不太方便，使用flatMap()会将多个数目不一的流合为一个流

将初始的对象『铺平』之后通过统一路径分发了下去。而这个『铺平』就是 flatMap() 所谓的 flat

### limit
    Stream<T> limit(long maxSize);

从Stream中获取指定数量的元素。
```
// 获取前5个权限对象组成的集合
List<UmsPermission> firstFiveList = permissionList.stream()
    .limit(5)
    .collect(Collectors.toList());
```

#### count
仅获取Stream中元素的个数。
```
// count操作：获取所有目录权限的个数
long dirPermissionCount = permissionList.stream()
    .filter(permission -> permission.getType() == 0)
    .count();
```

#### sorted
    Stream<T> sorted();

对Stream中元素按指定规则进行排序。
```
// 将所有权限按先目录后菜单再按钮的顺序排序
List<UmsPermission> sortedList = permissionList.stream()
    .sorted((permission1,permission2)->{return permission1.getType().compareTo(permission2.getType());})
    .collect(Collectors.toList());
```

#### skip
跳过指定个数的Stream中元素，获取后面的元素。
```
// 跳过前5个元素，返回后面的
List<UmsPermission> skipList = permissionList.stream()
    .skip(5)
    .collect(Collectors.toList());
```

#### 用collect方法将List转成map
有时候我们需要反复对List中的对象根据id进行查询，我们可以先把该List转换为以id为key的map结构，然后再通过map.get(id)来获取对象，这样比较方便。
```
//Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
                                    Function<? super T, ? extends U> valueMapper)
//返回一个将元素累积到 Map 中的收集器，其键和值是将提供的映射函数应用于输入元素的结果。
//如果映射的键包含重复项（根据 Object.equals(Object)），则在执行集合操作时会抛出 IllegalStateException。如果映射的键可能有重复项，请改用 toMap(Function, Function, BinaryOperator)。

// 以id为key，对象为值转换成map
Map<Long, UmsPermission> permissionMap = permissionList.stream()
    .collect(Collectors.toMap(permission -> permission.getId(), permission -> permission));
```
如果键值有重复的，则使用：
```
//Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
                                    Function<? super T, ? extends U> valueMapper,
                                    BinaryOperator<U> mergeFunction)
//返回一个将元素累积到 Map 中的收集器，其键和值是将提供的映射函数应用于输入元素的结果。
//如果映射的键包含重复项（根据 Object.equals(Object)），则将值映射函数应用于每个相等的元素，并使用提供的合并函数合并结果。
                                    
// 以id为key，对象为值转换成map，重复对象使用新对象替代
Map<Long, UmsPermission> permissionMap = permissionList.stream()
    .collect(Collectors.toMap(permission -> permission.getId(), permission -> permission, (v1, v2) -> v2)));
```
转换加去重：
```
List<PrincipalDTO> list = list.stream()
    .map(e->newPrincipalDTO(e.getUserId(), e.getUserName()))
    .distinct()
    .collect(Collectors.collectingAndThen(Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(b -> b.getId()))), ArrayList::new));
```
collectingAndThen()根据属性进行去重的操作，进行结果集的收集，收集到结果集之后再进行下一步的处理。操作中还用到了toCollection、TreeSet两个操作。
```
public static<T,A,R,RR> Collector<T,A,RR> collectingAndThen(Collector<T,A,R> downstream,
                                                                Function<R,RR> finisher)
```


collectingAndThen参数有两个:
- 第一个参数是Collector的子类，所以Collectors类中的大多数方法都能使用，Collectors.toCollection是Collectors通用的方法，其他类似方法还有：toList(),toSet(),toMap()等。去重的关键是使用了TreeSet作为结构体收集集合，同时传入Comparator.comparing比较器，根据某个属性进行比较去重校验。
- 第二个参数是一个Function函数，用到的ArrayList::new调用到了ArrayList的有参构造。 Function函数是R apply(T t)，在第一个参数downstream放在第二个参数Function函数的参数里面，将结果设置为t。

补充说明：
set三个子类的底层其实都是Map的。我们也知道Map是key-value键值对出现的。
```
    public TreeSet(Comparator<? super E> comparator) {
        this(new TreeMap<>(comparator));
    }
```
set添加方法是set.add(“xxx”)。参数只有一个，不是键值对的，那么底层Map怎么存储的呢？

从源码中，我们可以看到，把传递的参数作为key处理的。那么，value又是什么呢？
```
    // Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();

    ...

    public boolean add(E e) {
        return m.put(e, PRESENT)==null;
    }
```
其实就是new了个object对象。

问题来了：set为什么不能不能存放重复值，而list就可以了呢？

从上面add的源码中，我们可以看出，add的数据是作为map的key来存放的。在Map中，Key是不能重复的。所以，set里面的数据不能有重复的

TreeMap在添加时，如果指定的元素尚不存在，则将其添加到此集合中。如果该集合已包含该元素，则调用将保持该集合不变并返回 false。

Hashmap在添加时，将指定的值与此映射中的指定键相关联。如果映射先前包含键的映射，则替换旧值。

#### 分组获取
Collectors.groupingBy根据一个或多个属性对集合中的项目进行分组。
单属性分组
```
Map<String, List<Product>> prodMap= prodList.stream().collect(Collectors.groupingBy(Product::getCategory));
```
多属性分组
```
Map<String, List<Product>> prodMap = prodList.stream().collect(Collectors.groupingBy(item -> item.getCategory() + "_" + item.getName()));
```
根据不同条件分组
```
Map<String, List<Product>> prodMap= prodList.stream().collect(Collectors.groupingBy(item -> {
	if(item.getNum() < 3) {
		return "3";
	}else {
		return "other";
	}
}));
```

#### stream去重
按整个对象去重
```
list.stream().distinct().forEach(System.out::println);
```

按某个字段去重
```
    List<Book> unique2 = books.stream()
            .filter(distinctByKey(o -> o.getId()))
            .collect(Collectors.toList());
    
    //使用map去重
    public static <T> Predicate<T> distinctByKey(Function<? super T, Object> keyExtractor) {
        Map<Object, Boolean> seen = new ConcurrentHashMap<>();
        //如果指定的键尚未与值关联（或映射为 null），则将其与给定值关联并返回 null，否则返回当前值。
        return t -> seen.putIfAbsent(keyExtractor.apply(t), Boolean.TRUE) == null;
    }
    //使用Set去重
    private static <T> Predicate<T> distinctByKey(Function<? super T, ?> keyExtractor) {
        //创建一个由 ConcurrentHashMap 从给定类型到 Boolean.TRUE 支持的新 Set。
        Set<Object> seen = ConcurrentHashMap.newKeySet();
        //如果指定的元素尚不存在，则将其添加到此集合中（可选操作）。如果此集合已包含该元素，则调用将保持该集合不变并返回 false。
        return t -> seen.add(keyExtractor.apply(t));
    }
```


### Funcion

#### 函数式编程
函数式编程：方法即对象：Function<Integer,Integer> test1=i->i+1;
函数式编程的思想是先不去考虑具体的行为，而是先去考虑参数，具体的方法我们可以后续再设置。

#### 函数式接口
java.util.function包下面有大量的函数式接口，主要分为以下几个基本类别

* Function 输入参数为类型T， 输出为类型R， 记作 T -> R
Function<T,R>,主要方法：R apply(T t),这是一个修改者，默认方法：compose()：优先执行，andThen(),稍后执行，identity()：直接传自身。

* Consumer 输入参数为类型T， 输出为void， 记作 T ->
Consumer<T>,主要方法：T get(),这是一个生产者，可以提供一个T对象。

* Supplier 没有输入参数， 输出为类型T， 记作 void -> T
Supplier<T>,主要方法：T get(),这是一个生产者，可以提供一个T对象。

* Predicate 输入参数为类型T， 输出为类型boolean， 记作 T -> boolean
Predicate<T>,主要方法：boolean test(T t),这是一个判断者，默认方法：and()：且，or()：或，negate()：非。


例：
````
例如计算字符串长度的Function例子
Function<String, Integer> fc = s -> s.length();

再例如，切割字符串的Consumer
Consumer<String> consumer = c -> c.split(",");
````

如果输入参数是两个，这是可以使用
````
BiFunction<T,U,R> 记作 <T,U> -> R
BiConsumer<T,U> 记作 <T,U> -> void
BiPredicate<T,U> 记作 <T,U> -> boolean
````


### Option

#### Optional 类简介
  JDK8 中引入的 Optional 类可以解决空指针异常, 让我们省略繁琐 的非空判断. Optional 类就是一个可以为 null 容器, 或者保存指定类型的数据, 或者为 null.

1.判断空值
````
//Optional 可以解决空指针问题
Optional.ofNullable(user)     //把参数接收的 user 对象包装为 Optional 对象
                .map(u -> u.name)            //映射,只需要用户名
                .orElse("unknown");    //存在就返回,不存在返回 unknown
````


2 Optional 的基本操作

````
public class Test01 {
    public static void main(String[] args) {

        //1 把一个字符串封装为Optional对象
        //参数不能为null，否则抛出NullPointException
        Optional<String> ofString = Optional.of("hello");

        //Optional<String> ofString1 = Optional.of(null);

        //2 为指定的值创建Optional对象(可以为空)
        Optional<String> ofString2 = Optional.ofNullable(null);
        System.out.println(ofString2);//Optional.empty

        //3 直接创建一个空的Optional对象
        Optional<Object> empty = Optional.empty();
        System.out.println(empty);//Optional.empty

        //4 获得 Optional 对象中的值,如果值不存在会产生异常
        String text = ofString.get();
        System.out.println(text);
        String text2 = ofString2.get(); //java.util.NoSuchElementException

        //5 如果 Optional 对象中有值就返回,没有则返回指定的其他值
        text = ofString.orElse("another");

        //6 如果有值就返回,如果 Optional 对象中没值则创建一个新的
        text = ofString2.orElseGet(() -> "newString");
        System.out.println( text ); //newString

        //7 orElseThrow(),如果值存在就返回,否则抛出异常 /
        text = ofString2.orElseThrow(NullPointerException::new);
        text = ofString.orElseThrow(NullPointerException::new);
        System.out.println( text );


        //8 filter(),如果 Optional 对象有值返回满足指定条件的 Optional 对象
        // , 否则返回空 的 Optional 对象
        text = ofString.filter(s -> s.length() > 10).orElse("lenth is letter than 10");
        System.out.println( text );
        text = ofString.filter(s -> s.length() > 3).orElse("lenth is letter than 3");
        System.out.println( text );


        //9 如果 Optional 对象的值存在,执行 mapper 映射函数
        text = ofString.map(x -> x.toUpperCase()).orElse("Failure");
        System.out.println( text );
        text = ofString2.map(x -> x.toUpperCase()).orElse("Failure");
        System.out.println( text ); //Failure
        
        //10 ifPresent() 如果 Optional 对象有值就执行 Consumer 函数 
        ofString.ifPresent(s -> System.out.println("处理数据" + s)); 
        ofString2.ifPresent(s -> System.out.println("处理数据" + s)); //没有值什么也 不做 System.out.println("optional");


    }
}
````

### 关于Function中函数无法缓存
参考代码：
```
    public static void main(String[] args) throws IOException {

        /**
         * 这里的cache无法缓存，每次调用都会重新初始化
         */
        Function<String, Integer> errorCache = input -> {
            final Map<String, Integer> cache = new HashMap<>(5);
            Function<String, Integer> stringZySyncLogFunction = (job) -> cacheMethod(job);
            return cache.computeIfAbsent(input, stringZySyncLogFunction);
        };

        /**
         * 这里的cache无法缓存，每次调用都会重新初始化
         */
        Function<String, Function<String, Integer>> errorCache_FIX2 =  (String obj) -> {
            final Map<String, Integer> cache2 = new HashMap<>(5); // 这样修复就可以了
            return input -> {
                Function<String, Integer> stringZySyncLogFunction = (job) -> cacheMethod("inner"+job);
                cache2.computeIfAbsent(input, stringZySyncLogFunction);
                return errorCache.apply(input);
            };
        };

//        Integer job1 = errorCache.apply("job1");
//        Integer job1_1 = errorCache.apply("job1");
//        Integer job2 = errorCache.apply("job2");

//        supplier.get().apply("job1");
//        supplier.get().apply("job1");
//        supplier.get().apply("job2");

//        Function<String, Integer> errorCache_FIX = supplier.get();
//        Integer job1 = errorCache_FIX.apply("job1");
//        Integer job1_1 = errorCache_FIX.apply("job1");
//        Integer job2 = errorCache_FIX.apply("job2");

        Integer job11 = errorCache_FIX2.apply("job1").apply("job1");
        Integer job1_11 = errorCache_FIX2.apply("job1").apply("job1");
        Integer job21 = errorCache_FIX2.apply("job2").apply("job1");


        Function<String, Integer> cache = cacheFunction((String job) -> {
            return cacheMethod(job);
        });
//        Integer job1 = cache.apply("job1");
//        Integer job1_1 = cache.apply("job1");
//        Integer job2 = cache.apply("job2");
    }

    private static Integer cacheMethod(String job) {
        System.out.println(job + "-运行了");
        return new Random().nextInt();
    }

    /**
     * 这里的cache可以缓存，只在初始化调用一次
     */
    public static Supplier<Function<String, Integer>> supplier = () -> {
        final Map<String, Integer> cache = new HashMap<>(5); // 这样修复就可以了
        return input -> {
            Function<String, Integer> stringZySyncLogFunction = job -> cacheMethod(job);
            return cache.computeIfAbsent(input, stringZySyncLogFunction);
        };
    };

    // 创建一个缓存函数的方法
    public static <T, R> Function<T, R> cacheFunction(Function<T, R> function) {
        final Map<T, R> cache = new HashMap<>(5);
        return input -> {
            return cache.computeIfAbsent(input, function);
        };
    }
```