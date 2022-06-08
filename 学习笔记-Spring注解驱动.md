[TOC]

# 1、组件注册

@Bean注册

​	@Scope定义bean作用域

​	@Lazy定义单实例bean懒加载

@ComponentScans指定要导入的bean实例位于的包

​	includeFilters定义导入规则

​	excludeFilters定义导入排除规则

@Conditional定义bean加载规则

@Import快速将bean导入到容器

@FactoryBean创建bean


# 2、生命周期

bean的生命周期：bean创建-->初始化-->销毁。

构造（对象创建）：

​	单实例：容器启动时创建对象

​	多实例：每次获取时创建对象

初始化：

​	对象创建完成，并赋值后调用初始化方法

销毁：

​	单实例：容器关闭时进行销毁

​	多实例：容器不会管理这个实例，不会调用销毁方法



容器管理bean的生命周期：

​	1、可以由`@Bean(initMethod = "init", destroyMethod = "destroy")`进行定义初始化与销毁方法

​	2、让bean实现InitializingBean进行初始化，DisposableBean进行销毁

​	3、JSR250：

​		@PostConstruct在bean初始化完成后执行

​		@PreDestroy在容器销毁bean之前通知执行

​	4、BeanPostProcessor：bean的后置处理器，可在bean初始化前与初始化后进行处理

# 3、属性赋值

@Value：

​	1、基本数值

​	2、SpEL。#{}

​	3、${}。取出配置文件中的值（运行环境变量里面的值）

@PropertySource获取配置文件的键值对

# 4、自动装配

@Autowired：

​	默认使用类型进行装配，如果找到多个相同类型，再通过bean的id进行查询

​	通过@Qualifier进行指定bean的id

​	默认required=true，必须找到值进行装配

​	@Primary，选择首选装配的实例

@Resource（JSR250）：

​	默认byName，其次byType。

@Inject（JSR330）：

​	基本与@Autowired功能一致

​	@Inject默认根据类型进行自动装配的，如果找到多个类型相同的，会按照名称进行匹配; 如果需要指定按照名称进行装配，则需要配合@Named



自定义组件想要使用Spring容器底层的一些组件（ApplicationContext、BeanFactory），自定义组件实现xxxAware，在创建对象的时候会自动注入相关组件

@Profile:

​	根据当前环境，动态激活切换一系列组件

# 5、AOP原理

AOP即为动态代理，指在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的编程方式

AOP开启方式：

1. 将业务逻辑组件和切面类加入容器中，告诉Spring哪个是切面类（@Aspect)
2. 在切面类每一个通知方法上标注通知注解，告诉Spring何时何地运行（切入点表达式）
3. 开启基于注解的AOP模式@EnableAspectJAutoProxy

