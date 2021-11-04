# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## AOP

### 代理模式
静态代理：运行之前，代理类.class文件就已经被创建了
动态代理：是在程序运行时通过反射机制动态创建的。

### 实现方式：
1、代理模式静态的实现AOP
2、aspectj静态代理实现AOP	//@Aspect	aspectj帮我们将功能，在编译的阶段将织入到被代理的class文件里面。因为是静态织入，所以性能比较不错
静态代理总结：
	优点：可以做到在符合开闭原则的情况下对目标对象进行功能扩展。
	缺点：我们得为每一个服务都得创建代理类，工作量太大，不易管理。同时接口一旦发生改变，代理类也得相应修改。 
3、jdk动态代理实现AOP	//implements InvocationHandle
~~~~
11 public class DynamicProxyHandler implements InvocationHandler {
12 
13     private Object object;
14 
15     public DynamicProxyHandler(final Object object) {
16         this.object = object;
17     }
18 
19     @Override
20     public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
21         System.out.println("买房前准备");
22         Object result = method.invoke(object, args);
23         System.out.println("买房后装修");
24         return result;
25     }
26 }

15     public static void main(String[] args) {
16         BuyHouse buyHouse = new BuyHouseImpl();
17         BuyHouse proxyBuyHouse = (BuyHouse) Proxy.newProxyInstance(BuyHouse.class.getClassLoader(), new
18                 Class[]{BuyHouse.class}, new DynamicProxyHandler(buyHouse));
19         proxyBuyHouse.buyHosue();
20     }
~~~~

动态代理总结：虽然相对于静态代理，动态代理大大减少了我们的开发任务，同时减少了对业务接口的依赖，降低了耦合度。但是还是有一点点小小的遗憾之处，那就是它始终无法摆脱仅支持interface代理的桎梏，

4、cglib动态代理实现AOP	// implements MethodInterceptor
在实际开发中，可能需要对没有实现接口的类增强，用JDK动态代理的方式就没法实现。采用Cglib动态代理可以对没有实现接口的类产生代理，实际上是生成了目标类的子类来增强
~~~~
    public void test2() {
        final LinkManDao linkManDao = new LinkManDao();
        // 创建cglib核心对象
        Enhancer enhancer = new Enhancer();
        // 设置父类
        enhancer.setSuperclass(linkManDao.getClass());
        // 设置回调
        enhancer.setCallback(new MethodInterceptor() {  //***
            /**
             * 当你调用目标方法时，实质上是调用该方法
             * intercept四个参数：
             * proxy:代理对象
             * method:目标方法
             * args：目标方法的形参
             * methodProxy:代理方法
            */
            @Override
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy)
                    throws Throwable {
                System.out.println("记录日志");
                 Object result = method.invoke(linkManDao, args);
                return result;
            }
        });
        // 创建代理对象
        LinkManDao proxy = (LinkManDao) enhancer.create();
        proxy.save();
    }
~~~~
CGLIB代理总结： CGLIB创建的动态代理对象比JDK创建的动态代理对象的性能更高，但是CGLIB创建代理对象时所花费的时间却比JDK多得多。所以对于单例的对象，因为无需频繁创建对象，用CGLIB合适，反之使用JDK方式要更为合适一些。同时由于CGLib由于是采用动态创建子类的方法，对于final修饰的方法无法进行代理。
