### 深入理解Feign
Feign是一个声明式的Web Service客户端，它的目的就是让Web Service调用更加简单。它整合了Ribbon和Hystrix，从而让我们不再需要显式地使用这两个组件。Feign还提供了HTTP请求的模板，通过编写简单的接口和插入注解，我们就可以定义好HTTP请求的参数、格式、地址等信息。接下来，Feign会完全代理HTTP的请求，我们只需要像调用方法一样调用它就可以完成服务请求。

#### Feign具有如下特性：
- 可插拔的注解支持，包括Feign注解和JAX-RS注解
- 支持可插拔的HTTP编码器和解码器
- 支持Hystrix和它的Fallback
- 支持Ribbon的负载均衡
- 支持HTTP请求和响应的压缩

#### Feign的源码实现的过程如下：
    1. 首先通过@EnableFeignCleints注解开启FeignCleint
    2. 根据Feign的规则实现接口，并加@FeignCleint注解
    3. 程序启动后，会进行包扫描，扫描所有的@FeignCleint的注解的类，并将这些信息注入到ioc容器中。
    4. 当接口的方法被调用，通过jdk的代理，来生成具体的RequesTemplate
    5. RequesTemplate在生成Request
    6. Request交给Client去处理，其中Client可以是HttpUrlConnection、HttpClient也可以是Okhttp
    7. 最后Client被封装到LoadBalanceClient类，这个类结合类Ribbon做到了负载均衡。