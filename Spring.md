### 内容概述
1. 概念:
- Spring是一个轻量级k控制反转IoC和面向切面AOPd的容器框架
- IoC : Inversion of Control
- AOP : Aspect Oriented Programming

2. IoC
> 控制反转, 是一个概念, 是一种思想. 控制反转就是对对象控制权的转移, 从程序代码本身反转到了外部容器, 把对象的创建, 初始化, 销毁等工作交给spring容器来做. 由spring容器k控制对象的生命周期.

3. DI 
> 依赖注入:Dependency Injection. 依赖注入是指程序在运行的过程中, 若需要调用另一个对象协助时, 无须在代码中创建被调用者, 而是依赖外部容器, 由外部容器创建后传递给程序.  
依赖注入是目前最优秀的解耦方式, 依赖注入让springd的Bean之间以配置文件的方式组合在一起, 而不是以硬编码的方式耦合在一起

4. IoC与DI的关系:
> IoC是一种概念, 是一种思想, 它的实现方式多种多样. 当前比较流行的实现方式之一是DI

### Spring之IoC
1. 第一个IoC程序  
a) 导入jar包(基本7个)  
b) 创建spring配置文件  
c) Bean的定义与注册  
d) 从spring容器中获取Bean  

步骤分解: 
1. 导入jar包(基本要用的)  
com.springsource.org.apache.commons.logging-1.1.1 标准的日志接口, 不是具体实现  
com.springsource.org.apache.log4j-1.2.15 上面日志接口的具体实现  
junit-4.9 测试工具  
spring-beans-4.1.6.RELEASE.jar  
spring-context-4.1.6.RELEASE.jar  
spring-core-4.1.6.RELEASE.jar  
spring-expression-4.1.6.RELEASE.jar  

2. 创建spring配置文件applictionContext.xml
a) 引入约束文件  
spring-framework-4.1.6.RELEASE-> docs-> spring-framework-reference-> xsd-config.html
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- bean definitions here -->

</beans>
```
b) 添加本地约束文件
http://www.springframework.org/schema/beans/spring-beans.xsd -> 到schema文件夹中 -> beans -> spring-beans-4.1.xsd

### SpringIoC第一个程序实现
```
<beans>
    <!-- bean的定义,下面配置相当于SomeService service = new SomeService(); -->
    <bean id="someServiceImpl" class="com.service.impl.SomeServiceImpl">

</beans>
```
```
//下面是测试方法中的代码
//创建容器对象
ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
SomeService service = ac.getBean("someServiceImpl", SomeService.class);
service.doSomething();
```
> 如果直接用测试方法中new出ServiceImpl对象有什么缺点 : 当前测试类和service实现类耦合到了一起.

> 使用容器对象获得ServiceImpl对象有什么优点 : 实现了测试类和service实现类的解耦合.

### BeanFactory和ApplicationContext的区别
> ApplicationContext容器初始化时, 所有的容器中的bean创建完毕

> BeanFactory当调用getBean()方法获取相应对象时, 才创建对象.

### Bean的装配(创建Bean对象)_动态工厂模式
```
//创建工厂类
public class ServiceFactory{
    //实例工厂方式创建bean对象
    public SomeService getSomeService(){
        SomeService someServiceImpl = new SomeServiceImpl();
        return someServiceImpl;
    }
}
//applicationContext.xml文件中的配置
<beans>
    <!-- 注册工厂 -->
    <bean id="serviceFactory" class="com.factory.ServiceFactory"></bean>
    <!-- 从工厂中获取someServiceImpl对象 -->
    <bean id="" factory-bean="serviceFactory" factory-method="getSomeService"></bean>
</beans>

//创建容器对象, 测试是否成功
ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
SomeService service = ac.getBean("someServiceImpl", SomeService.class);
service.doSomething();
```

### Bean的装配之静态工厂方式
1. 思路是把ServiceFactory类中的getSomeService()方法定义成静态的
2. 到时候调用getSomeService()方法直接通过类名.方法名调用即可, 无需再创建对象了, 所以在applicationContext.xml中注册工厂的配置可以删除了, 代码修改如下
```
<!--从工厂中获取SomeServiceImplbean对象(调用getSomeService方法)-->
<bean id="someServiceImpl" class="com.factory.ServiceFactory" factory-method="getServcie">
```

### Bean的作用域
直接用例子说明:
```
<!-- 虽然是同一个实现类, 但是id不同, 创建工厂时便会创建连个id不同, 内容相同的对象 -->
<bean id="someServiceImpl1" class="com.service.impl.SomeServiceImpl">
<bean id="someServiceImpl2" class="com.service.impl.SomeServiceImpl">

<!-- 如果添加了scope="prototype"属性, 每次getBean都会获得新的对象 -->
<bean id="someServiceImpl" class="com.service.impl.SomeServiceImpl" scope="prototype">

//测试
ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
SomeService service1 = ac.getBean("someServiceImpl1", SomeService.class);
SomeService service2 = ac.getBean("someServiceImpl1", SomeService.class);
//如果没有给bean标签加上scope="prototype"属性, 上面两个对象是同一个对象, 如果加了就不是了.
//当然, 如果getBean的时候给出的是不同id的bean, 那肯定不是同一个对象了
```

### DI之设值注入_简单数据类型和引用数据类型注入
概念 : 所谓注入, 可以理解为为对象的属性赋值
- 基于XML的DI
1. 设值注入  
a) 简单数据类型和引用数据类型注入  
b) 集合属性注入(array, set, list, map, properties)  
c) 域属性自动注入(byName/byType; 全局和全局配置)  
d) 空字符串或null的注入
2. 构造注入
- 基于注解的DI
1. 环境搭建 : 导入aop包, 添加context约束头信息(组件扫描器)
2. 常用注解 : @Component, @Scope, @Value, @Resource, @Autowired

DI设值注入引用:
```
<!-- 开始的时候在pojo包下创建了两个实体类, 
    一个叫Star, 里面有name, age, partner三个属性
    一个叫Partner, 里面有name属性
-->
<beans>
    <bean id="star" class="com.pojo.Star">
        <!-- 简单数据类型注入 -->
        <property name="name" value="贝克汉姆"/>
        <property name="age" value="39">
        <!-- 引用数据类型注入, 需要引入其他的bean -->
        <property name="partner" ref="partner"/>
    </bean>
    <bean id="partner1" class="com.pojo.Partner">
        <property name="name" value="维多利亚">
    </bean>
    <bean id="partner2" class="com.pojo.Partner">
        <property name="name" value="布兰妮">
    </bean>
</beans>
<!-- 配置完成后, 通过工厂的getBean()方法获得以上beans的时候, bean已经被赋值了 -->
```

### DI之设置注入_集合属性注入
```
//假设创建了一个SomeService类
public class SomeService{
    private String[] myArray;
    private Set<Partner> mySet;
    private List<String> myList;
    private Map<String, String> myMap;
    private Properties myProps;
    //提供set/get方法...
}

<!-- 配置文件中的配置 -->
<bean id="someService" class="com.service.SomeService">
    <property name="myArray">
        <array>
            <value>北京</value>
            <value>上海</value>
        </array>
    </property>
    
    <property name="mySet">
        <set>
            <ref bean="partner1">
            <ref bean="partner2">
        </set>
    </property>
    
    <property name="myList">
        <list>
            <value>男</value>
            <value>女</value>
        </list>
    </property>
    
    <property name="myMap">
        <map>
            <entry key="qq" value="123456"/>
            <entry key="mobile" value="123456"/>
        </map>
    </property>
    
    <property name="myProps">
        <props>
            <prop key="兴趣">足球</prop>
            <prop key="爱好">下棋</prop>
        </props>
    </property>
</bean>
```

### DI之设置注入_域属性自动注入
1. 使用byName方式使域属性自动注入
```
//注意, 在Star类中有一项属性
private Partner partner;//保证partner和下面配置bean中的id="partner"一样才可以.

<bean id="star" class="com.pojo.Star" autowire="byName">
    <!-- byName方式域属性自动注入:要求注入的bean的id必须和被注入的bean对象那的id属性名一致 -->
    <property name="name" value="贝克汉姆"/>
    <property name="age" value="39">
    <!-- 省略了维多利亚的注入, 因为使用了byName方式注入 -->
</bean>
<bean id="partner" class="com.pojo.Partner">
    <property name="name" value="维多利亚">
</bean>
```
2. 使用byType方式域属性自动注入:spring配置文件中查询与属性类型一致的bean并进行注入
- 他有两种配置方案:  
a)局部配置 : 在某个bean的标签中加入:  autowire="byType"  
b)全局配置 : 在beans标签中加入属性 : default-autowire="byType".  
使用了全局配置后, 当前文件中所有的bean都使用域属性自动注入的方法配置

### DI之设置注入_空字符串和null的注入
1. 在bean的property标签中注入一个<value/>标签, 代表该属性为空串
```
<bean id="star" class="com.pojo.Star" autowire="byName">
    <property name="name"/><value/></property>
    <property name="age" value="39">
</bean>
```
2. 给bean的property标签中注入一个<null/>标签代表属性为null
```
<bean id="star" class="com.pojo.Star" autowire="byName">
    <property name="name"/><null/></property>
    <property name="age" value="39">
</bean>
```

### DI之构造注入
1. 顾名思义, 通过bean的构造方法给属性赋值
2. 为了演示, 老师删除掉了Star类中的get/set方法, 给出有参构造  
方案一(通常使用这种方案):
```
<bean id="star" class="com.pojo.Star" autowire="byName">
    <constructor-arg name="name" value="郭靖"></constructor-arg>
    <constructor-arg name="age" value="25"></constructor-arg>
    <constructor-arg name="partner" ref="partner"></constructor-arg>
</bean>
<bean id="partner" class="com.pojo.Partner">
    <property name="name" value="维多利亚">
</bean>
```
方案二: 对属性的获取不适用name, 而是使用index
```
<bean id="star" class="com.pojo.Star" autowire="byName">
    <constructor-arg index="0" value="郭靖"></constructor-arg>
    <constructor-arg index="1" value="25"></constructor-arg>
    <constructor-arg index="2" ref="partner"></constructor-arg>
</bean>
<bean id="partner" class="com.pojo.Partner">
    <property name="name" value="维多利亚">
</bean>
```
方案三: 可以不给出name属性, 直接写值, 要求是要保证和bean中的属性一一对应
```
<bean id="star" class="com.pojo.Star" autowire="byName">
    <constructor-arg  value="郭靖"></constructor-arg>
    <constructor-arg  value="25"></constructor-arg>
    <constructor-arg  ref="partner"></constructor-arg>
</bean>
<bean id="partner" class="com.pojo.Partner">
    <property name="name" value="维多利亚">
</bean>
```

### DI之注解
1. 底层通过AOP方式实现(面向切面编程)
2. 导入aop包, 添加context约束头信息(组件扫描器)
3. 常用注解 : @Component,  
4. @Scope,      (bean作用域注解)
5. @Value       (对简单数据类型的注解),  
6. @Resource,   (6,7是域属性自动注入的注解)   
7. @Autowried
---
1. 导入aop的jar包 : spring-aop-4.1.6.RELEASE.jar
2. 在spring配置文件中添加组件扫描器, 添加context约束
3. 添加约束之后, 不用配置bean标签了, 全部通过注解完成
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->

    <!-- 配置要扫描注解的包 -->
    <context:component-scan base-package="com.pojo"></context:component-scan>

</beans>
```
4. 在bean的实体类中添加注解@Component, 表示当前类交给spring容器管理.
```
@Component("myPartner")
public class Partner{
    @value("张三")
    private String name;
    
    public String ...
}

//表明当前类交给spring容器管理
@Component
public class Star{
    //简单数据类型通过@Value注解注入
    @value("李四")
    private String name;
    @value("23")
    private int age;
    //引用数据类型通过@Autowired注入:默认情况下是byType方式注入
    @Autowired
    //使用@Qualifier就是使用byName方式注入
    @Qualifier("myPartner")
    private Partner partner;
    
    //同时引用数据类型通过@Resource也可以是实现注入, 默认情况下是byName方式注入
    //只有找不到与名称匹配的bean的时候才会按照类型来进行注入
    @Resource
    private Partner partner;
    
    //去掉get/set方法    
}
```

### Bean相关常用注解
> 与@Component具有相同功能的还有另外3个注解

1. @Repository : 该注解添加在dao实现类上
2. @Service : 该注解添加在Service实现类上
3. @Controller : 该注解添加在Controllerl类上

> @scope是bean作用域注解, 添加在类体上面(默认是singleton,单例), 一般标为
`@Scope("prototype") //表示每次getBean()的时候获得一个新的bean对象`

### 静态代理
1. Aop引入
> 什么是代理, 比如交易链 : 厂家 -> 微商代理 -> 顾客, 微商代理的作用就是代理   
a) 使用代理可以隐藏目标类的具体实现  
b) 可以在不修改目标类代码的情况下对其功能进行加强  
c) 代理分为静态代理和动态代理两种.

> 概念 : 若代理类在程序运行前就已经存在, 那么这种代理方式被称为静态代理, 这种情况下的代理类通常都是我们在Java代码中定义的. 通常情况下, 静态代理中的代理类和目标类会实现同一接口或是派生自相同的父类.

静态代理代码演示:
```
//主业务接口
public interface SomeService {
	String doSome();
}

//目标类
public class SomeServiceImpl implements SomeService {
	@Override
	public String doSome() {
		return "China";
	}
}

//静态代理类，要和目标类实现相同接口, 我们的目的是把目标类doSome方法中返回的字符串变成大写
public class ServiceProxy implements SomeService {
	SomeService target;//目标类引用
	
	public ServiceProxy() {//无参构造
	}

	public ServiceProxy(SomeService target) {//在有参构造中完成对目标类的赋值
		this.target = target;
	}

	public String doSome(){//对目标类的目标方法进行增强
		return target.doSome().toUpperCase();
	}
}
//测试类
public class SomeTest {
	public static void main(String[] args) {
		//定义目标对象
		SomeService target = new SomeServiceImpl();
		//定义目标对象的代理对象
		SomeService proxy = new ServiceProxy(target);
		//执行代理类中增强后的目标方法.
		String result = proxy.doSome();
		System.out.println(result);
	}
}
```

### JDK动态代理
> 概念 : 代理类在程序运行时创建的代理方式被称为动态代理. 也就是说, 这种情况下, 代理类并不是在Java代码中定义的, 而是在运行时根据我们在Java代码中的"指示"动态生成的.  
如果目标对象实现了接口, 采用JDK的动态代理

```
//这时不需要给出代理类ServiceProxy, 直接使用JDK提供的API
public class SomeTest{
    public static void main(String[] args){
        //定义目标对象
        final SomeService target = new SomeServiceImpl();
        //定义目标对象的代理对象
        SomeService proxy = (SomeService)Proxy.newProxyInstance(target.getClass().getClassLoader(),//目标类的类加载器
        target.getClass().getInterfaces()), ;//目标类实现的所有接口
        new InvocationHandler(){//调用处理器
            //proxy代理对象
            //method目标方法
            //args目标方法的参数
            @Override
            public Object invoke(Object proxy, Method method, Object[] args)throws Throwable{
                String result = (String)method.invoke(target, args);
                return result.toUpperCase();//对目标方法进行增强
            }
        }
        
    }
}
```

### CGLIB动态代理
> 如果目标对象没有实现接口, 必须采用CGLIB动态代理

1. 导包 : cglib-nodep-3.1.jar
```
//创建cglib动态代理工厂
public class CglibProxyFactory implements MethodInterceptor{
    //工厂中要创建代理对象, 代理对象的创建肯定就要用待目标对象
    private SomeServiceImpl target;
    //无参构造
    public CglibProxyFactory(){
    }
    //有参构造, 在有参构造中完成对目标对象的赋值
    public CglibProxyFactory(SomeServiceImpl target){
        this.target = target;
    }
    //创建由工厂获取代理对象的方法
    public SomeServiceImpl proxyCreator(){
        //创建增强器
        Enhancer enhancer = new Enhancer();
        //指定父类(指定目标类)
        enhancer.setSuperClass(SomeServiceImpl.class);
        //指定回调接口对象
        enhancer.setCallback(this);
        //创建cglib代理对象
        return (SomeServiceImpl)enhancer.create();
    }
    
    //对目标对象的目标方法进行增强
    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable{
        String result = (String)method.invoke(target, args);
        return result.toUpperCase();
    }
}

public class TestSome{
    public static void main(String[] args){
        //定义目标对象
        SomeServiceImpl target = new SomeServiceImpl();
        //定义目标对象的代理对象
        SomeServiceImpl proxy = new CglibProxyFactory(target).proxyCreator();
        String result1 = proxy.doSome();
        System.out.println("result1");
    }
}
```

### AOP介绍
> aop即面向切面编程, 就是将交叉业务逻辑封装成切面, 利用AOP的功能将切面织入到主业务逻辑中. 所谓交叉逻辑是指:通用的, 与主业务逻辑无关的代码, 如安全检查, 事务, 日志等.

术语介绍:
- 切面 : 切面泛指交叉业务逻辑. 比如事务处理, 日志处理就可以理解为切面. 常用的切面由通知和顾问. 实际就是对主业务逻辑的一种增强.
- 织入 : 织入是指将切面代码插入到目标对象的过程.
- 连接点 : 连接点指切面可以织入的位置.
- 切入点 : 切入点指切面具体织入的位置
- 通知(Advice) : 通知是切面的一种实现, 可以完成简单织入功能(织入功能就是在这里完成的). 通知定义了增强代码切入到目标代码的时间点, 是目标方法执行之前执行, 还是之后执行等. 通知类型不同, 切入时间不同.
- 顾问(Advisor) : 顾问是切面的另一种实现, 能够将通知以更为复杂的方式织入到目标对象中, 是将通知包装为更为复杂切面的装配器. 不仅指定了切入时间点, 还可以指定具体的切入点


### 基于Schema-based方式之前置通知
1. 导入两个jar包  
a)spring-aop-4.1.6.RELEASE.jar  
b)com.springsource.org.aopalliance-1.0.0.jar  

2. 前置通知需要实现接口MethodBeforeAdvice
```
//先在SomeService接口中定义多一个doOther()方法, 在实现类中添加实现, 返回值为字符串"love"

//创建一个通知类
public class MyMethodBeforeAdvice implements MethodBeforeAdvice{
    /**
    * method : 目标方法
    * args   : 目标方法参数列表
    * target : 目标对象
    */
    @Override
    public void before(Method method, Object[] args, Object target)throws Throwable{
        System.out.println("前置通知的before()方法执行");
    }
}

//注册目标类
<bean id="someServiceImpl" class="com.service.impl.SomeServiceImpl">
//到spring配置文件applicationContext.xml中注册切面:前置通知
<bean id="myMethodBeforeAdvice" class="com.aspects.MyMethodBeforeAdvice"></bean>
//注册代理 : 把前置通知动态地注入到目标类中
<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactory">
    <!-- 指定目标对象 -->
    <property name="target" ref="someServiceImpl"></property>
    <!-- 指定目标类实现的所有接口 -->
    <property name="interfaces" value="com.service.SomeService"></property>
    <!-- 指定切面 -->
    <property name="interceptorNames" value="myMethodBeforeAdvice"></property>
</bean>

//测试方法:
@Test
public void someTest01(){
    //创建容器对象
    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    //通过容器对象获取实例对象
    SomeService service = ac.getBean("proxyFactoryBean", SomeService.class);
    service.doSome();
    String result = service.doOther();
    System.out.println(result);
}
```

### 基于Schema-based方式_后置通知
1. 后置通知需要实现接口AfterReturningAdvice
```
//先给出一个后置通知类:
//切面 : 后置通知
public class MyAfterReturningAdvice implements AfterReturningAdvice{
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target){
        System.out.println("后置通知的afterReturning()方法执行");
    }
}

//到applicationContext.xml中注册切面
<bean id="myAfterReturningAdvice" class="com.aspects.MyAfterReturningAdvice"></bean>
//然后在注册代理中指定切面
<property name="interceptorNames" value="myAfterReturningAdvice">


//在后置通知中我们可以获得目标方法的返回值, 并且可以对方法的返回值进行修改.
//在后置通知类中的afterRunning方法中, 本来就需要传递参数returnValue, 所以能使用目标方法的返回值
```

### 基于Schema-based方式_环绕通知
1. 环绕通知类需要实现接口MethodInterceptor
2. 重写invoke(MethodInvocation invocation)方法
```
//切面:环绕通知
public class MyMethodInterceptor implements MethodInterceptor{
    //invocation : 方法调用器
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable{
        System.out.println("环绕通知:目标方法执行之前!");
        //调用执行目标方法
        Object result = invocation.proceed();
        if(null != result){
            result = ((String)result).toUpperCase();
        }
        System.out.println("环绕通知:目标方法执行之后!");
        return result;
    }
}

//到applicaitonContext.xml中注册切面
<bean id="myMethodInterceptor" class="com.aspects.MyMethodInterceptor">
//将切面放到代理当中
<property name="interceptorNames" value="myMethodInterceptor"></property>
```

### 基于Schema-based方式_异常通知
1. 异常处理通知类需要实现ThrowsAdvice接口
2. 实现ThrowsAdvice接口不需要重写任何方法, 但是在注释中还是可出了可供选择的方法.
```
public class MyThrowsAdvice implements Throwable{
    //这个方法将会在目标方法出现异常时才执行.
    public void afterThrowing(Exception ex){
        System.out.println("异常通知执行!");
    }
}

//同样要在配置文件中注册切面:异常通知
//然后在代理类中给出异常.
```

### 基于AspectJ方式_注解方式前置通知
> 概念介绍 : 对于AOP编程思想, 很多框架都进行了实现. Spring就是其中之一, 可以完成面向切面编程. 然而, AspectJ也实现了AOP功能, 且其实现方式更为简便, 使用也更为方便, 而且还支持注解开发. 所以Spring又将AspectJ对于AOP的实现也引入到了自己的框架中.  
在Spring中使用AOP开发时, 一般使用AspectJ的实现方式.

AspectJ的通知类型, 常用的有5种 :   
a) 前置通知  
b) 后置通知
c) 环绕通知  
d) 异常通知  
e) 最终通知:无论程序执行是否正确, 该通知都会执行
```
execution([modifiers-parttern]  访问权限类型
    ret-type-pattern            返回值类型
    [declaring-type-pattern]    全限定性类名
    name-pattern(param-pattern) 方法名(参数名)
    [throws-pattern]            抛出异常类型
)
//切入表达式要匹配的对象就是目标方法的方法名, 所以eexecution表达式中加[]的部分表示可以省略部分, 各部分用空格分开. 在其中可以使用以下符号.

符号    意义
*       0至多个任意字符
..      用在方法参数中, 表示任意多个参数; 用在包名后, 表示当前包及其子包路径.
+       用在类名后, 表示当前类及其子类; 用在接口后, 表示当前接口及其实现类.
```
1. 导入jar包 : spring-aspects-4.1.6.RELEASE.jar
2. 到依赖文件夹中 : com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar
```
//创建切面类
@Aspect //表明当前类是一个切面
public class MyAspect{
    /* 注解解释:
    * : 方法的返回值任意
    *.. : 多级包
    service.* : service包下的任意类和接口
    .doSome(..) : 匹配上doSome方法名, 里面的..表示doSome方法的参数任意
    */
    @Before("excution(* *..service.*.doSome(..))")
    public void before(){
        System.out.println("前置通知方法执行")
    }
}

//在文档中查找spring-aop的约束
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
<!-- 注册目标类 -->
<bean id="someServiceImpl" class="com.service.impl.SomeServiceImpl"/>
<!-- 注册切面 -->
<bean id="myAspect" class="com.aspects.MyAspect"></bean>
<!-- 注册自动代理, 不用任何设置 -->
<aop:aspectj-autoproxy/>

</beans>
```

### 基于Aspect方式之注解方式后置通知
```
public class MyAspect{
    //前置通知
    @Before("excution(* *..service.*.doSome(..))")
    public void before(){
        System.out.println("前置通知方法执行")
    }
    
    //后置通知, 方法的返回值任意, *..代表多级包, service.*代表service包下的任意的类和接口, doOther()方法
    @AfterReturning("execution(* *..service.*.doOther(..))")
    public void afterReturning(){
        System.out.println("后置方法执行");
    }
}

//如何获取目标方法的返回值呢?有时添加后置通知就是想对目标方法的返回值进行增强.
public class MyAspect{
    @AfterReturning(value="excution(* *..service.*.doSome(..))", returning="result")
    public void afterReturning(Object result){
        System.out.println("后置通知方法执行!目标方法的返回值是"+result);
    }
}
```

### 基于AspectJ方式之注解方式环绕通知
```
@Around()
public Object around(ProceedingJoinPoint pjp) throws Throwable{
    System.out.println("环绕通知:目标方法执行之前!");
    String result = (String) pjp.proceed();//执行目标方法
    if(result != null){
        result = result.toUpperCase();
    }
    System.out.println("环绕通知, 目标方法执行之后");
}
```

### 基于AspectJ方式之注解方式异常通知
```
@AfterThrowing(value="excution(* *..service.*.doSome(..))", throwing="ex")
public void throwing(Exception ex){
    System.out.println("异常通知方法执行! 异常信息为:"+ex);
}
```

### 基于AspectJ方式之注解方式异常通知
```
@After("execution(* *..service.*.doSome(..))")
public void after(){
    System.out.println("最终通知方法执行.");
}
```

### 基于AspectJ_xml方式_前置通知
```
//现在自己的Aspect类中给出前置通知执行的方法
public class MyAspect{
    public void before(){
        System.out.println("前置通知方法执行!");
    }
}

//在总配置文件applicationContext.xml中进行配置
<!-- 注册目标类 -->
<bean id="myAspect" class="com.service.impl.SomeServiceImpl"></bean>
<!-- 注册切面 -->
<bean id="myAspect" class="com.aspects.MyAspect">
<!-- AOP配置 -->
<aop:config>
    <!-- 定义切入点 -->
    <aop:pointcut expression="execution(* *..service.*.doSome(..))" id="doSomePC"/>
    <aop:pointcut expression="execution(* *..service.*.doOther(..))" id="doOtherPC"/>
    <!-- 使用pointcut-ref属性配置切入点 -->
    <aop:aspect ref="myAspect">
        <aop:before method="before" pointcut-ref="doSomePC"/>
    </aop:aspect>
</aop:config>
```

### 基于AspectJ_xml方式_带参数的前置通知
```
public class MyAspect{
    public void before(){
        System.out.println("前置通知方法执行!");
    }
    
    public void before(JoinPoint jp){
        System.out.println("前置通知方法执行! jp="+jp);
    }
}

<aop:config>
    ...
    <!-- 如果通知方法有参数, 需要给在方法名后天添加括号括住参数的全限定路径 -->
    <aop:aspect ref="myAspect">
        <aop:before method="before(org.aspectj.lang.JoinPoint)" pointcut-ref="doSomePC"/>
    </aop:aspect>
</aop:config>

```

### 基于AspectJ_xml方式_配置方法后置通知
```
public class MyAspect{
    public void afterReturning(Object result){
        System.out.println("后置方法执行, 目标方法的返回值是"+result);
    }
}

<!-- applicationContext -->
<beans>
    <!-- 注册目标类 -->
    <bean id="someServiceImpl" class="com.service.impl.SomeService"/>
    <!-- 注册切面 -->
    <bean id="myAspect" class="com.aspects.MyAspect">
    <!-- AOP配置 -->
    <aop:config>
        <!-- 定义切入点 -->
        <aop:pointcut expression="excution(* *..service.*.doSome(...))" id="doSome">
        <!-- 注册通知方法 -->
            <aop:aspect ref="myAspect">
                <!-- 如果注册的方法需要参数, 就在方法名后面的括号中给出参数的全限定路径, 给出切入点, 如果增强的方法有返回值, 要用通知方法的参数变量去获得返回值 -->
                <aop:after-returning method="afterReturning(java.lang.Object)" pointcut-ref=doSome" returning="result">
            </aop:aspect>
    </aop:config>
<beans>
```

### 基于AspectJ方式_xml方式_环绕通知
> 环绕通知方法中也有一个参数, 由于该参数是默认参数, 所以在配置文件<aop:around>标签中的method属性最后不需要给出参数的全限定路径.

```
//在MyAspect类中给出环绕通知方法around
public Object around(ProceedingJoinPoint pjp)throws Throwable{
    System.out.println("环绕通知:目标方法执行!");
    String result = (String)pjp.proceed();//执行目标方法
    if(null != result){
        result = result.toUpperCase();
    }
    System.out.println("环绕通知, 目标方法执行之后!");
    return result;
}

<!-- 在配置文件中注册 -->
<beans>
    <!-- 注册目标类 -->
    <bean id="someServiceImpl" class="com.service.impl.SomeService" >
    <!-- 注册切面 -->
    <bean id="myAspect" class="com.aspects.MyAspect"/>
    <!-- AOP配置 -->
    <aop:config>
        <!-- 定义切入点 -->
        <aop:pointcut expression="excution(* *..service.*.doSome(..))">
        <aop:aspect ref="myAspect">
            <aop:around method="around" pointcut-ref"doSome">
        </aop:aspect>
    </aop:config>
</beans>
```

### 基于AspectJ_xml方式_配置方法异常通知
```
<!-- 只给出配置文件中的配置 -->
<aop:config>
    <aop:pointcut expression="excution(* *..service.*.doSome(..))">
    <aop:aspect ref=myAspect"">
        <!-- 可以获得目标方法抛出的异常, 使用throwing属性, 值要和目标方法中给出的异常变量名称一致 -->
        <aop:after-throwing method="throwing(java.lang.Exception)" pointcut-ref="doSome" throwing="ex"/>
    </aop:aspect>
</aop:config>
```

### 基于AspectJ_xml方式_配置方法_最终通知
```
<aop:config>
    <aop:pointcut expression="excution(* *..service.*.doOther(..))">
    <aop:aspect ref="myAspect">
        <aop:after method="after" pointcut-ref="doOther">
    </aop:aspect>
</aop:config>
```

### 事务管理简介及环境搭建
1. 导jar包:  
a) 为了提高效率使用c3p0数据库连接池  
spring对jdbc的支持 : spring-jdbc-4.1.6.RELEASE.jar  
spring对事务的支持 : spring-tx-4.1.6.RELEASE.jar  
spring对c3p0的支持(该jar包在依赖文件中) : com.springsource.com.mchange.v2.c3p0-0.9.1.2.jar  
数据库驱动 : mysql-connector-java-5.1.30.jar
2. 把连接数据库的信息保存在配置文件jdbc.properties中
```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql:///test
jdbc.username=root
jdbc.password=123
```

### 事务管理-创建实体类及表
> 本小案例需求是使用账户里的钱购买一点理财产品, 比如基金等

1. 创建账户类, 属性:aid账号, aname账户名, balance账户余额  
给出set/get方法  
提供有参,无参构造  
提供toString()方法
2. 创建基金类, 属性:fid基金序号, fname基金名称, count基金余额

### 事务管理-创建dao及实现类
1. 创建dao包, 在包中先给出AccountDao接口, 在接口中给出两个方法  
```
//开通银行账户
void insertAccount(String aname, double money);
//更新银行账户余额
void updateAccount(String aname, double money);
```
2. 创建FundDao接口
```
//开基金账户
void insertFund(String fname, int amount);
//更新基金账户数量
void updateFund(String fname, int amount);
```
3. 在dao包中创建实现类impl包, 实现类不仅要实现相应的接口, 还要实现JdbcDaoSupport类. 继承了JdbcDaoSupport类才能获得操作数据库的模板
```
//开银行账户
@Override
public void insertAccount(String aname, double money){
    String sql = "insert into account(aname, balance) values(?,?)";
    this.getJdbcTemplate().update(sql, aname, money);
}

//更新银行账户
@Override
public void updateAccount(String aname, double money){
    String sql = "update account set balance=balance-? where aname=?"
    this.getJdbcTemplate().update(sql, money, aname);   
}
```

### 事务管理-创建service接口及实现类
1. 创建业务层接口
```
public interface FundService{
    //开银行账户
    void openAccount(String aname, double money);
    //开基金账户
    void openFund(String fname, int amount);
    //购买基金
    void buyFund(String aname, double money, String fname, int amount);
}
```
2. 创建业务接口的实现类, service层需要通过dao层操作数据库, dao如何注入到service层, 后面在spring的配置文件中进行配置
```
public class FundServiceImpl implements FundService{
    private AccountDao accountDaoImpl;//注意创建的是接口类型, 面向接口编程
    private FundDao fundDaoImpl;
    
    //接下来要给出accountDaoImpl和fundDaoImpl的get/set方法
    
    //给出购买基金的方法示例
    @Override
    public void buyFund(String aname, double money, String fname, int count){
        accountDaoImpl.updateAccount(aname, money);
        fundDaoImpl.updateFund(fname, count);
    }
}
```

### 事务管理-测试
1. 编写配置文件applicationContext.xml
1) 开始的时候要先加载jdbc属性文件, 需要用到一个标签context标签, 需要在约束头信息中添加context  
2) 如何添加context信息:打开spring中的docs文件 --->  spring-framework-reference ---> htmlsingle ---> index.html ---> 搜索spring-context, 把对应的信息复制到自己的spring配置文件中, 复制底下两行和头顶一行包含context的
```
<beans>
    <!-- 加载jdbc属性文件 -->
    <context:property-placeholder location="jdbc.properties">
    <!-- 注册c3p0数据源, 全局查找类ComboPoolDataSource, 并把全限定路径复制到class属性中 -->
    <bean id="dataSource" class="...ComboPoolDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!-- 注册银行账户AccountDao -->
    <bean id="accountDaoImpl" class="....AccountDaoImpl">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 注册基金账户FundDao -->
    <bean id="fundDaoImpl" class="....FundDaoImpl">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 注册service -->
    <bean id="fundServiceImpl" class="...serviceImpl">
        <!-- 注意name属性的值要和实际类中的变量的名字一致 -->
        <property name="accountDaoImpl" ref="accountDaoImpl"></property>
        <property name="fundDaoImpl" ref="fundDaoImpl"></property>
    </bean>
</beans>
```
2. 编写测试文件
```
public class SomeTest{
    private FundService service;
    
    //导入的是junit的before注解
    @Before
    public void before(){
        //创建spring容器对象
        ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        //通过容器获取spring对象, 注意这里填写的参数必须和注册的bean的id一致
        service = ac.getBean("fundServiceImpl", FundService.class);
    }

    @Test
    public void openAccount(){
        service.openAccount("工商银行", 10000);
    }
    
    @Test
    public void openFund(){
        service.openFund("余额宝", 2000);
    }
    @Test
    public void buyFund(){
        //下面的方法执行完后其实是对银行账户和基金账户进行更新, 银行账户减少2000, 基金账户增加2000
        service.buyFund("工商银行", 2000, "余额宝", 2000);
    }
}
```

### 事务管理-AspectJ的AOP配置方式管理事务
> spring事务的默认回滚方式是:发生运行时异常时回滚, 发生受查时异常时提交

1. 先给出一个自己定义的异常类
```
public class FundException extends Exception{
    public FundException(){
        super();
    }
    
    public FundException(String message){
        super(message);
    }
}
```
2. 经过测试发先, 在购买基金的方法中添加异常, 老师为了躲过eclipse的检查用了if(1==1)的语句把抛出异常的语句括起来. 最后的结果是没有回滚, 导致基金和银行账户的资金流通不对等.
3. 下面演示使用AspectJ的AOP管理器管理事务
```
<!-- 注册事务管理器, 类的全名叫DataSourceTransactionManager -->
<bean id="transactionManager" class="...DataSourceTransactionManager">
    <!-- 需要连接数据库, 配备数据源属性 -->
    <property name="dataSource" ref="dataSource"></property>
</bean>
<!-- 注册事务通知, 记得要引入tx的约束信息 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!-- 配置事务的属性, 事务隔离级别, 事务传播行为, 事务默认超时时限, 以及对他们的操作 -->
    <tx:attributes>
        <!-- 连接点方法的事务属性配置 -->
        <!--带有open的方法全部匹配上, 事务隔离级别设置为默认, propagation事务的传播行为, -->
        <tx:method name="open*" isolation="DEFAULT" proagation="REQUIRED"/>
        <tx:method name="buy*" isolation="DEFAULT" proagation="REQUIRED" rollback-for="FundException"/>
    </tx:attributes>
</tx>

<!-- AOP配置 -->
<aop:config>
    <!-- 先定义切入点, 准备引入 -->
    <aop:pointcut expression="execution(* *..service.*.buyFund(..))" id="fundPC"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="fundPC"/>
</aop:config>
```

### 事务管理-事务注解管理事务
1. 在需要添加事务的方法头上添加事务注解
```
@Transaction(rollbackFor=FundException.class)
public void buyFund(...){
    ...
}
```
2. 在配置文件中扫描该注解
```
<!--使用注解管理事务只需要:事务管理器, 事务通知, AOP配置即可-->
<!-- 注册事务管理器, 类的全名叫DataSourceTransactionManager -->
<bean id="transactionManager" class="...DataSourceTransactionManager">
<!--配置事务注解驱动-->
<tx:annotation-driver transaction-manager="transactionManager"/>
<!--注册事务通知-->
<!--AOP配置-->
```