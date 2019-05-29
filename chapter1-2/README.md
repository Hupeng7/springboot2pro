Spring Boot 2.x基础教程：工程结构推荐

Spring Boot框架本身并没有对工程结构有特别的要求，但是按照最佳实践的工程结构可以帮助我们减少可能会遇见的坑，尤其是Spring包扫描机制的存在，如果您使用最佳实践的工程结构，可以免去不少特殊的配置工作。

典型示例
以下结构是比较推荐的package组织方式：

com
  +- example
    +- myproject
      +- Application.java
      |
      +- domain
      |  +- Customer.java
      |  +- CustomerRepository.java
      |
      +- service
      |  +- CustomerService.java
      |
      +- web
      |  +- CustomerController.java
      |
root package：com.example.myproject，所有的类和其他package都在root package之下。
应用主类：Application.java，该类直接位于root package下。通常我们会在应用主类中做一些框架配置扫描等配置，我们放在root package下可以帮助程序减少手工配置来加载到我们希望被Spring加载的内容
com.example.myproject.domain包：用于定义实体映射关系与数据访问相关的接口和实现
com.example.myproject.service包：用于编写业务逻辑相关的接口与实现
com.example.myproject.web：用于编写Web层相关的实现，比如：Spring MVC的Controller等
上面的结构中，root package与应用主类的位置是整个结构的关键。由于应用主类在root package中，所以按照上面的规则定义的所有其他类都处于root package下的其他子包之后。默认情况下，Spring Boot的应用主类会自动扫描root package以及所有子包下的所有类来进行初始化。

什么意思呢？举个例子，假设我们将com.example.myproject.web包与上面所述的root package：com.example.myproject放在同一级，像下面这样：

com
  +- example
    +- myproject
      +- Application.java
      |
      +- domain
      |  +- Customer.java
      |  +- CustomerRepository.java
      |
      +- service
      |  +- CustomerService.java
      |
    +- web
    |  +- CustomerController.java
    |
这个时候，应用主类Application.java在默认情况下就无法扫描到com.example.myproject.web中的Controller定义，就无法初始化Controller中定义的接口。

非典型结构下的初始化
那么如果，我们一定要加载非root package下的内容怎么办呢？

方法一：使用@ComponentScan注解指定具体的加载包，比如：

@SpringBootApplication
@ComponentScan(basePackages="com.example")
public class Bootstrap {

    public static void main(String[] args) {
        SpringApplication.run(Bootstrap.class, args);
    }

}
这种方法通过注解直接指定要扫描的包，比较直观。如果有这样的需求也是可以用的，但是原则上还是推荐以上面的典型结构来定义，这样也可以少写一些注解，代码更加简洁。

方法二：使用@Bean注解来初始化，比如：

@SpringBootApplication
public class Bootstrap {

    public static void main(String[] args) {
        SpringApplication.run(Bootstrap.class, args);
    }

    @Bean
    public CustomerController customerController() {
        return new CustomerController();
    }

}
这种方法在业务开发的时候并不是特别推荐，更适合用于框架封装等场景，关于更多封装上的技巧，后面我们在进阶教程中详细讲解。