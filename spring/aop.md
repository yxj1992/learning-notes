#### AOP  
AOP（Aspect Oriented Programming），即面向切面编程，可以说是OOP（Object Oriented Programming，面向对象编程）的补充和完善。OOP引入封装、继承、多态等概念来建立一种对象层次结构，用于模拟公共行为的一个集合。不过OOP允许开发者定义纵向的关系，但并不适合定义横向的关系，例如日志功能。日志代码往往横向地散布在所有对象层次中，而与它对应的对象的核心功能毫无关系对于其他类型的代码，如安全性、异常处理和透明的持续性也都是如此，这种散布在各处的无关的代码被称为横切（cross cutting），在OOP设计中，它导致了大量代码的重复，而不利于各个模块的重用。  

AOP技术恰恰相反，它利用一种称为"横切"的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其命名为"Aspect"，即切面。所谓"切面"，简单说就是那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块之间的耦合度，并有利于未来的可操作性和可维护性。  

使用"横切"技术，AOP把软件系统分为两个部分：核心关注点和横切关注点。业务处理的主要流程是核心关注点，与之关系不大的部分是横切关注点。横切关注点的一个特点是，他们经常发生在核心关注点的多处，而各处基本相似，比如权限认证、日志、事物。AOP的作用在于分离系统中的各种关注点，将核心关注点和横切关注点分离开来。  

#### AOP核心概念  
1、 横切关注点  
对哪些方法进行拦截，拦截后怎么处理，这些关注点称之为横切关注点  

2、 切面（aspect）  
类是对物体特征的抽象，切面就是对横切关注点的抽象  

3、 连接点（joinpoint）  
被拦截到的点，因为Spring只支持方法类型的连接点，所以在Spring中连接点指的就是被拦截到的方法，实际上连接点还可以是字段或者构造器  

4、 切入点（pointcut）  
对连接点进行拦截的定义  

5、 通知（advice）  
所谓通知指的就是指拦截到连接点之后要执行的代码，通知分为前置、后置、异常、最终、环绕通知五类  

6、 目标对象  
代理的目标对象  

7、 织入（weave）  
将切面应用到目标对象并导致代理对象创建的过程  

8、 引入（introduction）  
在不修改代码的前提下，引入可以在运行期为类动态地添加一些方法或字段  

#### Spring对AOP的支持  
Spring中AOP代理由Spring的IOC容器负责生成、管理，其依赖关系也由IOC容器负责管理。因此，AOP代理可以直接使用容器中的其它bean实例作为目标，这种关系可由IOC容器的依赖注入提供。Spring创建代理的规则为：  
1、 默认使用Java动态代理来创建AOP代理，这样就可以为任何接口实例创建代理了  

2、 当需要代理的类不是代理接口的时候，Spring会切换为使用CGLIB代理，也可强制使用CGLIB  

AOP编程其实是很简单的事情，纵观AOP编程，程序员只需要参与三个部分：  

1、 定义普通业务组件

2、 定义切入点，一个切入点可能横切多个业务组件

3、 定义增强处理，增强处理就是在AOP框架为普通业务组件织入的处理动作

所以进行AOP编程的关键就是定义切入点和定义增强处理，一旦定义了合适的切入点和增强处理，AOP框架将自动生成AOP代理，即：代理对象的方法=增强处理+被代理对象的方法。  

#### @AspectJ 支持  
@AspectJ 是一种使用 Java 注解来实现 AOP 的编码风格.  
@AspectJ 风格的 AOP 是 AspectJ Project 在 AspectJ 5 中引入的, 并且 Spring 也支持@AspectJ 的 AOP 风格.  
@AspectJ 可以以 XML 的方式或以注解的方式来使能, 并且不论以哪种方式使能@ASpectJ, 我们都必须保证 aspectjweaver.jar 在 classpath 中.  

##### 定义 aspect(切面)
```
@Component
@Aspect
public class MyTest {
}
```  
##### 声明 pointcut  
一个 pointcut 的声明由两部分组成:  

- 一个方法签名, 包括方法名和相关参数  
- 一个 pointcut 表达式, 用来指定哪些方法执行是我们感兴趣的(即因此可以织入 advice).  

在@AspectJ 风格的 AOP 中, 我们使用一个方法来描述 pointcut, 即:  
```
@Pointcut("execution(* com.xys.service.UserService.*(..))") // 切点表达式
private void dataAccessOperation() {} // 切点前面
```  
这个方法必须无返回值.  
这个方法本身就是 pointcut signature, pointcut 表达式使用@Pointcut 注解指定.  
上面我们简单地定义了一个 pointcut, 这个 pointcut 所描述的是: 匹配所有在包 com.xys.service.UserService 下的所有方法的执行.  

##### 切点标志符(designator)  
AspectJ5 的切点表达式由标志符(designator)和操作参数组成. 如 "execution( greetTo(..))" 的切点表达式, execution 就是 标志符, 而圆括号里的 greetTo(..) 就是操作参数  

execution  
匹配 join point 的执行, 例如 "execution(* hello(..))" 表示匹配所有目标类中的 hello() 方法. 这个是最基本的 pointcut 标志符.  

within  
匹配特定包下的所有 join point, 例如 within(com.xys.*) 表示 com.xys 包中的所有连接点, 即包中的所有类的所有方法. 而 within(com.xys.service.*Service) 表示在 com.xys.service 包中所有以 Service 结尾的类的所有的连接点.  

this 与 target  
this 的作用是匹配一个 bean, 这个 bean(Spring AOP proxy) 是一个给定类型的实例(instance of). 而 target 匹配的是一个目标对象(target object, 即需要织入 advice 的原始的类), 此对象是一个给定类型的实例(instance of).  

bean  
匹配 bean 名字为指定值的 bean 下的所有方法, 例如:  
```
bean(*Service) // 匹配名字后缀为 Service 的 bean 下的所有方法
bean(myService) // 匹配名字为 myService 的 bean 下的所有方法
```  

args
匹配参数满足要求的的方法. 例如:  
```
@Pointcut("within(com.xys.demo2.*)")
public void pointcut2() {
}

@Before(value = "pointcut2()  &&  args(name)")
public void doSomething(String name) {
    logger.info("---page: {}---", name);
}
```      

```
@Service
public class NormalService {
    private Logger logger = LoggerFactory.getLogger(getClass());
    public void someMethod() {
        logger.info("---NormalService: someMethod invoked---");
    }

    public String test(String name) {
        logger.info("---NormalService: test invoked---");
        return "服务一切正常";
    }
}
```    

当 NormalService.test 执行时, 则 advice doSomething 就会执行, test 方法的参数 name 就会传递到 doSomething 中.  

常用例子:  

```
// 匹配只有一个参数 name 的方法
@Before(value = "aspectMethod()  &&  args(name)")
public void doSomething(String name) {
}

// 匹配第一个参数为 name 的方法
@Before(value = "aspectMethod()  &&  args(name, ..)")
public void doSomething(String name) {
}

// 匹配第二个参数为 name 的方法
Before(value = "aspectMethod()  &&  args(*, name, ..)")
public void doSomething(String name) {
}
```  
@annotation  
匹配由指定注解所标注的方法, 例如:  
```
@Pointcut("@annotation(com.xys.demo1.AuthChecker)")
public void pointcut() {
}
```  
则匹配由注解 AuthChecker 所标注的方法.  

常见的切点表达式  

匹配方法签名  
```
// 匹配指定包中的所有的方法
execution(* com.xys.service.*(..))

// 匹配当前包中的指定类的所有方法
execution(* UserService.*(..))

// 匹配指定包中的所有 public 方法
execution(public * com.xys.service.*(..))

// 匹配指定包中的所有 public 方法, 并且返回值是 int 类型的方法
execution(public int com.xys.service.*(..))

// 匹配指定包中的所有 public 方法, 并且第一个参数是 String, 返回值是 int 类型的方法
execution(public int com.xys.service.*(String name, ..))
```  
匹配类型签名  
```
// 匹配指定包中的所有的方法, 但不包括子包
within(com.xys.service.*)

// 匹配指定包中的所有的方法, 包括子包
within(com.xys.service..*)

// 匹配当前包中的指定类中的方法
within(UserService)


// 匹配一个接口的所有实现类中的实现的方法
within(UserDao+
```  

匹配 Bean 名字  
```
// 匹配以指定名字结尾的 Bean 中的所有方法
bean(*Service)
```  

切点表达式组合  
```
// 匹配以 Service 或 ServiceImpl 结尾的 bean
bean(*Service || *ServiceImpl)

// 匹配名字以 Service 结尾, 并且在包 com.xys.service 中的 bean
bean(*Service) && within(com.xys.service.*)
```  

##### 声明 advice  
advice 是和一个 pointcut 表达式关联在一起的, 并且会在匹配的 join point 的方法执行的前/后/周围 运行. pointcut 表达式可以是简单的一个 pointcut 名字的引用, 或者是完整的 pointcut 表达式.  

下面我们以几个简单的 advice 为例子, 来看一下一个 advice 是如何声明的.  

Before advice  
```
@Component
@Aspect
public class BeforeAspectTest {
    // 定义一个 Pointcut, 使用 切点表达式函数 来描述对哪些 Join point 使用 advise.
    @Pointcut("execution(* com.xys.service.UserService.*(..))")
    public void dataAccessOperation() {
    }
}
```

```
@Component
@Aspect
public class AdviseDefine {
    // 定义 advise
    @Before("com.xys.aspect.PointcutDefine.dataAccessOperation()")
    public void doBeforeAccessCheck(JoinPoint joinPoint) {
        System.out.println("*****Before advise, method: " + joinPoint.getSignature().toShortString() + " *****");
    }
}
```  
这里, @Before 引用了一个 pointcut, 即 "com.xys.aspect.PointcutDefine.dataAccessOperation()" 是一个 pointcut 的名字.
如果我们在 advice 在内置 pointcut, 则可以:  
```
@Component
@Aspect
public class AdviseDefine {
    // 将 pointcut 和 advice 同时定义
    @Before("within(com.xys.service..*)")
    public void doAccessCheck(JoinPoint joinPoint) {
        System.out.println("*****doAccessCheck, Before advise, method: " + joinPoint.getSignature().toShortString() + " *****");
    }
}
```  

around advice  
around advice 比较特别, 它可以在一个方法的之前之前和之后添加不同的操作, 并且甚至可以决定何时, 如何, 是否调用匹配到的方法.  
```
@Component
@Aspect
public class AdviseDefine {
    // 定义 advise
    @Around("com.xys.aspect.PointcutDefine.dataAccessOperation()")
    public Object doAroundAccessCheck(ProceedingJoinPoint pjp) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        // 开始
        Object retVal = pjp.proceed();
        stopWatch.stop();
        // 结束
        System.out.println("invoke method: " + pjp.getSignature().getName() + ", elapsed time: " + stopWatch.getTotalTimeMillis());
        return retVal;
    }
}
```  
around advice 和前面的 before advice 差不多, 只是我们把注解 @Before 改为了 @Around 了.  

#### 参考资料  
- [彻底征服 Spring AOP 之 理论篇](https://segmentfault.com/a/1190000007469968)  
- [Spring3：AOP](http://www.cnblogs.com/xrq730/p/4919025.html)

