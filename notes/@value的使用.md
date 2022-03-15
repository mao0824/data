# @value的使用

### 不通过配置文件的注入属性的情况

通过@Value将外部的值动态注入到Bean中，使用的情况有：

- 注入普通字符串
- 注入操作系统属性
- 注入表达式结果
- 注入其他Bean属性
- 注入文件资源
- 注入URL资源

```java
　　 @Value("normal")
    private String normal; // 注入普通字符串

    @Value("#{systemProperties['os.name']}")
    private String systemPropertiesName; // 注入操作系统属性

    @Value("#{ T(java.lang.Math).random() * 100.0 }")
    private double randomNumber; //注入表达式结果

    @Value("#{beanInject.another}")
    private String fromAnotherBean; // 注入其他Bean属性

    @Value("classpath:com/hry/spring/configinject/config.txt")
    private Resource resourceFile; // 注入文件资源

    @Value("http://www.baidu.com")
    private Resource testUrl; // 注入URL资源
```

### 通过配置文件的注入属性的情况

通过@Value将外部配置文件的值动态注入到Bean中。配置文件主要有两类：

- application.properties或者application.yml,在spring boot启动时默认加载此文件
- 自定义属性文件。自定义属性文件通过@PropertySource加载。@PropertySource可以同时加载多个文件，也可以加载单个文件。如果相同第一个属性文件和第二属性文件存在相同key，则最后一个属性文件里的key启作用。加载文件的路径也可以配置变量，如下文的${anotherfile.configinject}，此值定义在第一个属性文件config.properties

第一个属性文件config.properties内容如下： 
${anotherfile.configinject}作为第二个属性文件加载路径的变量值

```
book.name=bookName
anotherfile.configinject=placeholder
```

第二个属性文件config_placeholder.properties内容如下：

```java
book.name.placeholder=bookNamePlaceholder
```

下面通过@Value(“${app.name}”)语法将属性文件的值注入bean属性值

```java
@Component
// 引入外部配置文件组：${app.configinject}的值来自config.properties。
// 如果相同
@PropertySource({"classpath:com/hry/spring/configinject/config.properties",
    "classpath:com/hry/spring/configinject/config_${anotherfile.configinject}.properties"})
public class ConfigurationFileInject{
    @Value("${app.name}")
    private String appName; // 这里的值来自application.properties，spring boot启动时默认加载此文件

    @Value("${book.name}")
    private String bookName; // 注入第一个配置外部文件属性

    @Value("${book.name.placeholder}")
    private String bookNamePlaceholder; // 注入第二个配置外部文件属性

    @Autowired
    private Environment env;  // 注入环境变量对象，存储注入的属性值

    public String toString(){
        StringBuilder sb = new StringBuilder();
        sb.append("bookName=").append(bookName).append("\r\n")
        .append("bookNamePlaceholder=").append(bookNamePlaceholder).append("\r\n")
        .append("appName=").append(appName).append("\r\n")
        .append("env=").append(env).append("\r\n")
        // 从eniroment中获取属性值
        .append("env=").append(env.getProperty("book.name.placeholder")).append("\r\n");
        return sb.toString();
    }   
}
```

## @Value("#{}")与@Value("${}")的区别

1. @Value(“#{}”) 表示SpEl[表达式](https://so.csdn.net/so/search?q=表达式&spm=1001.2101.3001.7020)通常用来获取bean的属性，或者调用bean的某个方法。当然还有可以表示常量
2. 用 @Value(“${xxxx}”)注解从配置文件读取值的用法

## 普通工具类不能使用@Value注入yml文件中的自定义参数的问题

1. Spring boot中普通工具类不能使用@Value注入yml文件中的自定义参数的问题,new会导致部分环境未加载，尽可能舍弃new的方式，交付spring管理
2. 工具类也是需要交给spring管理，需要在工具类上加上@Component注解
3. 加上set方法，且不能用static修饰，因为@value用set方法赋值
4. 启动类上加上@ServletComponentScan注解