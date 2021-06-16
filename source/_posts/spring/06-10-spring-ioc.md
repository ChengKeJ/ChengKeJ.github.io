---
title: "Spring Tutorial: IoC Container"
date: 2021-06-10 10:14:37
tags: Spring
---
<img src="https://cdn.jsdelivr.net/gh/ChengKeJ/pic@master/img/%E5%9B%BE%E6%80%AA%E5%85%BD_7d43390b66abdbb57cd5a8b2c6ef0169_30946.png" style="zoom:90%;" />

#### **什么是IOC？**

通俗理解就是 POJO 原本是自己需要然后new出来，现在是IOC 容器则是 你告诉我你需要什么，我们直接给你，让别人为你服务！

**现实生活**： 1.你出门之前需要穿衣服，之前则是需要自己找对应的衣服，现在则反转成你的妻子把这些衣服找好给你穿上。

<!--more-->

当然为你服务的前提是知道你的需求：

*  第一类似于你是酒吧常客，服务员立马知道你的需求 -- 构造函数初始化 ，优点则是对象构造完成之后 对象就已经就绪可以用了

*  第二类似于你到酒吧大喊我需要啤酒，服务员知道你的需求 -- 对象set/get 方法 对象构造完成之后也不可以立马使用，但是比较解耦，类似于我告诉对应的需求，你来服务我，而不是见到我就无脑服务我

IOC 可以很好的做到解耦，正常来说 我明确我需要什么，具体服务实现你提供给我即可。


#### 看看spring 里的Ioc 容器：

#####  beanFactory &  ApplicationContext

![image-20210527161111966](https://i.loli.net/2021/05/27/fYeCTJqAKmWi1Sz.png)

![image-20210527160746029](https://i.loli.net/2021/05/27/N6eX1cO4mdLufng.png)



**defaultListableBeanFactory** 是一个比较通用的**beanFactory** ，不仅实现了 beanFactory 的getBean等方法，也实现了

**beanDefinitionRegistry**的注册管理的功能。beanDefinitionRegistry 就像图书馆的书架一样，beanFactory 则是整个图书馆，

书架上的书则是beanDefinition 。



![image-20210527161035328](https://i.loli.net/2021/05/27/vXPYDwZec9lLBt7.png)

##### 大体流程

外部配置 xml / properties --> beanDefinitionReader --->beanDefinition ---> 将beanDefinition 注册到beanDefinitionRegister，完成

bean的注册和加载 ，从而进行bean的获取。

![image-20210527164639931](https://i.loli.net/2021/05/27/VXAExpJmFtdLszO.png)



Ioc 容器 就是在了解你的需求之后 去满足你的需求，而不需要你自己去实现自己的需求。

spring 里的容器则是beanFactory  比较简单只有getBean ，高级一点的则有applicationContext ，里面不仅是getBean

##### beanFactory & factoryBean

![image-20210528103240843](https://i.loli.net/2021/05/28/yV5xhaAOqISwb8U.png)

##### 容器的整个历程

* 概览：

![image-20210528150114956](https://i.loli.net/2021/05/28/jTKN3G4xWy8mibM.png)

**容器启动阶段**： BeanDefinitionReader 加载解析 xml 配置信息 成beanDefinition ，并且注册到beanDefinitionRegister。

![image-20210528150321558](https://i.loli.net/2021/05/28/Lw1x4iqgyKvH7NX.png)

**容器实例化阶段**： getBean 尝试获取对象，如果获取不到，则根据对应注册的beanDefinition 进行实例化返回

**ps**：总的来说 第一阶段像装配生产线，酒吧服务知道你的需求，第二阶段则是进行真正的生产，酒吧服务员满足你的需求。

##### BeanFactoryPostprocessor

**BeanFactoryPostprocessor** 会插手容器启动阶段，可以修改已经加载的beanDefinition信息，常见的如:PropertyPlaceholderConfigurer 和 PropertyOverrideConfigurer.

* **PropertyOverrideConfigurer**:

![image-20210528165746359](https://i.loli.net/2021/05/28/wZYQyU29e7XMNvm.png)

* **overrideConfig**.properties: 规则 --> beanname+属性

```properties
testFactoryBeanCore.name="ckj"
```

* **结果** ：

![image-20210528170019059](https://i.loli.net/2021/05/28/AfmV5U674OD9qQy.png)



##### bean 的生命历程

经过第一阶段的 启动过程 之后，有了bean的 beanDefinitions，进入实例化阶段，这个阶段实在getBean 时 才进行触发。



![image-20210528172119044](https://i.loli.net/2021/05/28/9Z4G7NA6mrcUEHO.png)



##### beanFactoryPostprocessor & beanPostprocessor

BeanPostProcessor是存在于对象实例化阶段作用于bean，而BeanFactoryPostProcessor则是存在于容器启动阶段，作用于beanFactory 。

##### 自定义beanPostProcessor

**beanPostProcessor：**

```java
@Component
@Slf4j
public class CustomBeanPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof TestBeanPostProcessorCore) {
            ((TestBeanPostProcessorCore) bean).setAge(100);
            ((TestBeanPostProcessorCore) bean).setName("ckj");
            log.info("=== this custom bean :{}", JSON.toJSONString(bean));
            return bean;
        }
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {

        return BeanPostProcessor.super.postProcessAfterInitialization(bean, beanName);
    }
}
```

```java
@Component
@Data
public class TestBeanPostProcessorCore {

    private String name;

    private Integer age;
}
```

**运行：**

```java
2021-05-31 16:50:19.522  INFO 12652 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-05-31 16:50:19.522  INFO 12652 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1641 ms
2021-05-31 16:50:19.708  INFO 12652 --- [           main] c.c.base.spring.CustomBeanPostProcessor  : === this custom bean :{"age":100,"name":"ckj"}

```


