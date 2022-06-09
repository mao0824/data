# @Autowired 与@Resource的详解

## @Autowired
Autowired是Spring自己实现的
```java
@Target({ElementType.CONSTRUCTOR, ElementType.METHOD, ElementType.PARAMETER, ElementType.FIELD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Autowired {

	/**
         * 是否必须装载属性，默认为true（也就是说是否要求装配对象必须存在）
	 * Declares whether the annotated dependency is required.
	 * <p>Defaults to {@code true}.  
	 */
	boolean required() default true;

}
```
从源码可以看出，@Autowired可以用于构造器、方法、参数、属性和用于注解声明，而我们实际应用中，用的最多的是用于属性装配。

### @Autowired用于属性
 1. 默认优先按照类型去容器中找对应的组件:applicationContext.getBean(BookDao.class);找到就赋值
 2. 如果找到多个相同类型的组件，再将属性的名称作为组件的id去容器中查applicationContext.getBean("bookDao")
 3. 默认情况下，自动装配一定要将属性赋值，没有就会报错； 可以使用@Autowired(required=false);

### @Autowired的辅助注解，用来规范属性的注入
`@Qualifier注解`：如果当Spring上下文中存在不止一个UserDao类型的bean时，就会抛出BeanCreationException异常;
如果Spring上下文中不存在UserDao类型的bean，也会抛出BeanCreationException异常。我们可以使用@Qualifier配合@Autowired来解决这些问题

### @Autowired用于构造器，参数，方法； 都是从容器中获取参数组件的值。

1. 标注在方法位置：Spring容器创建当前对象时，会调用标注了@Autowired注解的方法。如果方法中有参数，参数值从Spring容器中获取。@Bean+方法参数；参数从容器中获取;默认不写@Autowired效果是一样的；都能自动装配
2. 标在构造器上：容器启动创建bean时，会优先调用标有@Autowired注解的构造器，如果@Autowired标注在有参构造器上，构造器中的参数必须在容器中可以找到，否则容器启动失败。
3. 放在参数位置：从Spring容器中获取参数信息，一般都省略@Autowoired注解 

## @Resource
 - @Resource注解是基于 JSR 250  规范的注解，J2EE提供的。 
 - Resource有两个重要属性，分别是name和type：
   - 两个属性都不指定时，@Resource默认按照属性名查找，如果查找不到就按照类型查找，如果找不大或找到多个会报异常。
   - 只指定name属性，则按照指定的name名查找，如果找不大或找到多个会报异常。
   - 只指定type属性，则按照属性信息查找，如果找不大或找到多个会报异常。
   - 同时指定name属性和type属性，则按照属性信息和name名查找，如果找不大或找到多个会报异常。

## @Autowired 与@Resource的区别
1. @Autowired与@Resource都可以用来装配bean. 都可以写在字段上,或写在setter方法上。
2. @Autowired默认按类型装配（这个注解是属业spring的），默认情况下必须要求依赖对象必须存在，如果要允许null值，可以设置它的required属性为false，如：@Autowired(required=false) ，如果我们想使用名称装配可以结合@Qualifier注解进行使用
3. @Resource（这个注解属于J2EE的），默认按照名称进行装配，名称可以通过name属性进行指定，如果没有指定name属性，当注解写在字段上时，默认取字段名进行安装名称查找，如果注解写在setter方法上默认取属性名进行装配。当找不到与名称匹配的bean时才按照类型进行装配。但是需要注意的是，如果name属性一旦指定，就只会按照名称进行装配