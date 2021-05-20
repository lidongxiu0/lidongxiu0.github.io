# Spring自动装配方法

- no: 不进行自动装配，手动设置Bean的依赖关系。
- byName: 根据Bean的名字进行自动装配。
- byType: 根据Bean的类型进行自动装配。
- constructor: 类似于byType，不过是应用于构造器的参数，如果正好有一个Bean与构造器的参数类型相同则可以自动装配，否则会导致错误。
- autodetect: 如果有默认的构造器，则通过constructor的方式进行自动装配，否则使用byType的方式进行自动装配。

自动装配没有自定义装配方式那么精确，而且不能自动装配简单属性（基本类型、字符串等），在使用时应注意。



# Spring中Bean的作用域

​	在Spring的早期版本中，仅有两个作用域：singleton和prototype，前者表示Bean以单例的方式存在；后者表示每次从容器中调用Bean时，都会返回一个新的实例，prototype通常翻译为原型。

​	设计模式中的创建型模式中也有一个原型模式，原型模式也是一个常用的模式，例如做一个室内设计软件，所有的素材都在工具箱中，而每次从工具箱中取出的素材对象的一个原型，可以通过对象克隆来实现原型模式。

​	Spring 2.x中针对WebApplicationContext新增了3个作用域，分别是：request（每次HTTP请求都会创建一个新的Bean）、session（同一个HttpSession共享同一个Bean，不同的HttpSession使用不同的Bean）和globalSession（同一个全局Session共享一个Bean）。

​	单例模式和原型模式都是重要的设计模式。一般情况下，无状态或状态不可变的类适合使用单例模式。在传统开发中，由于DAO持有Connection这个非线程安全对象因而没有采用单例模式；但在Spring环境下，所有DAO类对可以采用单例模式，因为Spring利用AOP和Java API中的ThreadLocal对非线程安全的对象进行了特殊处理。



# <font color="red">IoC和AOP</font>

## IOC（控制反转）

控制反转，简单说，就是创建对象的控制权被反转到了spring框架上。

通常，我们实例化一个对象时，都是使用类的构造方法来new一个对象，这个过程是由我们自己来控制的，而控制反转就把new对象的工作交给了spring容器。

IOC的主要实现方法有两种：依赖查找、依赖注入。而依赖注入是一种更可取的方式。

### 依赖查找和依赖注入的区别

**依赖查找** 主要是容器为组件提供了一个回调接口和上下文环境。这样一来，组件就必须自己使用容器提供的API来查找资源和协作对象，控制反转仅体现在那些回调方法上，容器调用这些回调方法，从而应用代码获取到资源。

**依赖注入** 组件不做定位查询，只提供标准的Java方法让容器去决定依赖关系。容器全权负责组件的装配，把符合依赖关系的对象通过Java Bean属性或构造方法传递给需要的对象。

### IoC容器

具有依赖注入功能的容器，可以创建对象的容器。IoC容器负责实例化、定位、配置应用程序中的对象并建立这些对象之间的依赖。

### 依赖注入

**DI** *Dependency Injection* 

**依赖注入** 由IoC容器动态地将某个对象所需要的外部资源（包括对象、资源、常量数据）注入到组件（Controller, Service等）之中。简单来说，就是IoC容器会把当前对象所需要的外部资源动态地注入给我们。

spring依赖注入的方式主要有四个，基于注解注入方式、set注入方式、构造器注入方式、静态工厂注入方式。基于注解注入方式，配置较少，比较方便。

## AOP面向切面编程

面向切面编程（AOP）就是纵向的编程。比如业务A和业务B现在需要一个相同的操作，传统方法我们可能需要在A、B中都加入相关操作代码，而应用AOP就可以只写一遍代码，A、B共用这段代码。并且，当A、B需要增加新的操作时，可以在不改动原代码的情况下，灵活添加新的业务逻辑实现。

在实际开发中，比如商品查询、促销查询等业务，都需要记录日志、异常处理等操作，AOP把所有共用代码都剥离出来，单独放置到某个类中进行集中管理，在具体运行时，由容器进行动态织入这些公共代码。

AOP主要一般应用于<font color="red">签名验签、参数校验、日志记录、事务控制、权限控制、性能统计、异常处理</font>等。

### AOP涉及名词

**切面 (Aspect)** 共有功能的实现。如日志切面、权限切面、验签切面等。在实际开发中通常是一个存放共有功能实现的标准Java类。当Java类使用了@Aspect注解修饰时，就能被AOP容器识别为切面。

**通知 (Advice)** 切面的具体实现。就是要给目标对象织入的事情。以目标方法为参照点，根据放置的地方不同，可分为前置通知（Before）、后置通知（AfterReturning）、异常通知（AfterThrowing）、最终通知（After）与环绕通知（Around）5种。在实际开发中通常是切面类中的一个方法，具体属于哪类通知，通过方法上的注解区分。

**连接点 (JoinPoint)** 程序在运行过程中能够插入切面的地点。例如，方法调用、异常抛出等。Spring只支持方法级的连接点。一个类的所有方法前、后、抛出异常时都是连接点。

**切入点 (Pointcut)** 用于定义通知应该切入到哪些连接点上。不同的通知通常需要切入到不同的连接点上，这种精准的匹配是由切入点的正则表达式来定义的。

​	比如，在上面所说的连接点的基础上来定义切入点。有一个类，类里有10个方法，那就产生了几十个连接点。但是我们并不想在所有方法上都织入通知，我们只想让其中的几个方法，在调用之前检验下入参是否合法，那么就用切点来定义这几个方法，让切点来筛选连接点，选中我们想要的方法。切入点就是来定义哪些类里面的哪些方法会得到通知。

**目标对象 (Target)** 那些即将切入切面的对象，也就是那些被通知的对象。这些对象专注业务本身的逻辑，所有的共有功能等待AOP容器的切入。

**代理对象 (Proxy)** 将通知应用到目标对象之后被动态创建的对象。可以简单理解为，代理对象的功能等于目标对象本身业务逻辑加上共有功能。代理对象对于使用者而言是透明的，是程序运行过程中的产物。目标对象被织入共有功能后产生的对象。

**织入 (Weaving)** 将切面应用到目标对象从而创建一个新的代理对象的过程。这个过程可以发生在编译时、类加载时、运行时。spring是在运行时完成织入，运行时织入通过Java语言的反射机制与动态代理机制来动态实现。



# IoC和DI

**IoC**叫控制反转，是`Inversion of Control` 的缩写，**DI** (Dependency Injection)叫依赖注入， 是对IoC更简单的诠释。控制反转是把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理。

所谓 **“控制反转”** 就是对组件对象控制权的转移，从程序代码本身转移到了外部容器，由容器来创建对象并管理对象之间的依赖关系。IoC体现了_好莱坞原则  - “Don't call me, we will call you”_。

**依赖注入的基本原则**是应用组件不应该负责查找资源或其他依赖的协作对象。配置对象的工作应该由容器负责，查找资源的逻辑应该从应用组件的代码中抽取出来，交给容器完成。

DI是对IoC更准确的描述，即组件之间的依赖关系由容器在运行期间决定，形象的来说，即由容器动态的将某种依赖关系注入到组件之中。



一个类A需要用到接口B中的方法，那么就需要为类A和接口B建立关联或依赖关系，最原始的方法是在类A中创建一个接口B的实现：类C的实例，但这种方法需要开发人员自行维护二者的依赖关系，也就是说，当依赖关系发生变动的时候需要修改代码并重新构建整个系统。如果通过一个容器来管理这些对象以及对象之间的依赖关系，则只需要在类A中定义好用于关联接口B的方法（构造器或setter方法），将类A和接口B的实现类C放入容器中，通过对容器的配置来实现二者的关联。

依赖注入可以通过setter方法注入（设值注入）、构造器注入和接口注入三种方式来实现，Spring支持setter注入和构造器注入，通常使用构造器注入来注入必须的依赖关系，对于可选的依赖关系，则setter注入是更好的选择，setter注入需要类提供无参构造器或者无参的静态工厂方法来创建对象。



# Spring中BeanFactory和ApplicationContext的区别

## 概念

**BeanFactory : **

BeanFactory是spring中比较原始、比较古老的Factory。因此BeanFactory无法支持spring插件，例如：AOP、Web应用等功能。



**ApplicationContext : **

ApplicationContext是BeanFactory的子类，因为古老的BeanFactory无法满足不断更新的spring的需求，于是ApplicationContext就基本上代替了BeanFactory的工作，以一种更面向框架的工作方式以及对上下文进行分层和实现继承，并在这个基础上对功能进行扩展：

1. MessageSource，提供国际化的消息访问
2. 资源访问（如URL和文件）
3. 事件传递
4. Bean的自动装配
5. 各种不同应用层的Context实现

## 区别

1. 如果使用ApplicationContext，如果配置的bean是singleton，那么不管有没有或想不想用它，它都会被实例化。好处是可以预先加载，坏处是浪费内存。
2. BeanFactory，当使用BeanFactory实例化对象时，配置的bean不会马上被实例化，而是等到使用该bean的时候（getBean）才会被实例化。好处是节约内存，坏处是速度较慢。多用于移动设备的开发。
3. 没有特殊要求的情况下，应该使用ApplicationContext完成。因为BeanFactory能完成的事情，ApplicationContext都能完成，并且提供了更多接近现在开发的功能。



# SpringIoC的原理；要实现IoC的步骤



## IoC (Inversion of Control，控制倒转)

**IoC**是spring的核心，贯穿始终。所谓的IoC，对于spring框架来说，就是由spring来负责控制对象的生命周期和对象间的关系。

IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（依赖注入）来实现的。比如对象A需要操作数据库，以前需要在A中编写一个代码来获得一个Connection对象，有了spring就只需要告诉spring，A中需要依赖Connection，至于这个Connection怎么构造，何时构造，A不需要知道。在系统运行时，spring会在合适的时候制造一个Connection，然后注射到A中，这样就完成了对各个对象之间关系的控制。A需要依赖Connection才能正常运行，而这个Connection是由spring注入到A中的，依赖注入的名字就是这么来的。

**DI：** Java 1.3 之后的一个重要特征是反射（reflection），它允许程序在运行的时候动态的生成对象、执行对象的方法、改变对象的属性，spring就是通过反射来实现注入的。

传统的程序开发需要我们自己去设计和实现每一个环节，在一个对象中，如果要使用另外的对象，就必须自己new一个或是从JNDI中查询一个，使用完后还需要将对象销毁（比如Connection等），对象始终会和其他的接口或类耦合起来。



## 实现IoC的步骤

1. 定义用来描述bean的配置的Java类
2. 解析bean的配置，将bean的配置信息转换为BeanDefinition对象保存在内存中，spring中采用HashMap进行对象存储，其中会用到一些xml解析技术
3. 遍历存放BeanDefinition的HashMap对象，逐条取出BeanDefinition对象，获取bean的配置信息，利用Java的反射机制实例化对象，将实例化后的对象保存在另外一个Map中。



# 依赖注入的方式

## Set方法注入

set方法注入是通过在类中实现get、set方法来实现属性或对象的依赖注入，在标签中定义一个id为userDaoImpl的bean，并通过注入了name为username，value为admin的值，注入完成后直接通过getUsername()获取到值admin。

```java
public class UserDaoImpl {
    private String username;
    
    public String getUsername() {
        return username;
    }
    
    public void setUsername(String username) {
        this.username = username;
    }
}
```

xml代码

```xml
<bean id="userDaoImpl" class="com.example.UserDaoImpl">
	<property name="username" value="admin"></property>
</bean>
```



## 构造方法注入

构造方法注入是指在构造方法中注入属性或对象来实现依赖注入，在标签中定义一个id为userDaoImpl的Bean，并通过注入了name为username， value为admin的值，注入完成后直接通过this.username获取到值admin。其中引用类型使用ref属性，基本类型使用value属性。

```java
public class UserDaoImpl {
    private String username;
    
    public UserDaoImpl(String username) {
        this.username = username;
    }
}
```

xml代码

```xml
<bean id="userDaoImpl" class="com.example.UserDaoImpl">
	<constructor-arg name="username" value="admin"></constructor-arg>
</bean>
```



## 自动装配

spring提供了自动装配的功能，简化了我们的配置，自动装配默认是不打开的，常用的方式有两种：

- byName: 通过参数名自动装配，id为userService的autowire被设置为byName后，IoC容器会通过名字来自动装配，发现UserService类中有个叫userDao的属性，然后看IoC容器中有没有id为userDao的，如果有就装配进去。

```xml
<bean id="userDao" class="com.example.UserDao"></bean>
<bean id="userService" class="com.example.UserService" autowire="byName"/>
```

- byType: 通过参数类型自动装配，当autowire被设置为byType后，IoC容器会看里面有没有UserDao类型的，有就装配进去。

```xml
<bean id="userDao" class="com.example.UserDao"></bean>
<bean id="userService" class="com.example.UserService" autowire="byType"/>
```



## 注解

`@Autowired` 注解可以实现自动装配，只要在对应的属性上标记该注解，但是`@Autowired`注解只按照byType注入。

```java
public class UserController {
    @Autowired
    private UserService userService;
}
```

`@Resource`注解可以实现自动装配，它有两个重要属性`name`和`type`， `name`属性解析为bean的名字，`type`属性则解析为bean的类型。所以如果使用`name`属性，则使用`byName`的自动注入策略，而使用`type`属性，则使用`byType`的自动注入策略。如果既不指定`name`也不指定`type`，则将通过反射机制使用`byName`自动注入策略。

`@Autowired`注解和`@Resource`注解的作用相同，只不过`@Autowired`按照`byType`注入，如果`@Autowired`想使用名称可以结合`@Qualifier`注解使用。



# @Controller和@RestController的区别

`@RestController`注解相当于`@ResponseBody` + `@Controller`合在一起使用。



# @Autowired和@Resource的区别

## 共同点

两者都可以写在字段和setter方法上。两者如果都写在字段上，则不需要再写setter方法。

## 不同点

**@Autowired**

@Autowired为Spring提供的注解，需要导入包`org.springframework.beans.annotation.Autowired`; 只按照`byType`注入。

@Autowired注解是按照类型（`byType`）装配依赖对象，默认情况下要求依赖对象必须存在，如果允许`null`值，可以设置它的`required`属性为false。如果想使用按照名称（`byName`）来装配，可以结合@Qualifier注解一起使用。

**@Resource**

@Resource默认按照`byName`自动注入，由`J2EE`提供，需要导入包`javax.annotation.Resource`。@Resource有两个重要的属性：`name`和`type`，而Spring将@Resource注解的`name`属性解析为bean的名字，而`type`属性则解析为bean的类型。所以，如果使用`name`属性，则使用`byName`的自动注入策略，而使用`type`属性时则使用`byType`自动注入策略。如果既不制定`name`也不制定`type`属性，这时将通过反射机制使用`byName`自动注入策略。



# Bean的生命周期

![](.\image\308572_1537967995043_4D7CF33471A392D943F00167D1C86C10.png)



# Spring支持的事务管理类型

spring支持编程式事务管理和声明式事务管理。

编程式事务管理：通过编程的方式管理事务，带来极大的灵活性，但是难维护。

声明式事务管理：可以将业务代码和事务管理分离，只需用注解和XML配置来管理事务。



# Spring框架优点

1. **方便解耦、简化开发** ：spring就像一个大工厂，可以将所有对象创建和依赖关系的维护，交给spring管理。
2. **AOP编程的支持** ：spring提供面向切面编程，可以方便的实现对程序进行权限拦截、运行监控等功能
3. **声明式事务的支持** ：只需通过配置就可以完成对事务的管理，而无需手动编程
4. **方便程序的测试** ：spring对Junit4支持，可以通过注解方便的测试spring程序
5. **方便集成各种优秀框架** ：spring不排斥各种优秀的开源框架，其内部提供了对各种优秀框架（如：Struts、Hibernate、MyBatis、Quartz等）的直接支持
6. **降低JavaEE API的使用难度** ：spring对JavaEE开发中非常难用的一些API（JDBC、JavaMail、远程调用等），都提供了封装，使这些API应用难度大大降低
