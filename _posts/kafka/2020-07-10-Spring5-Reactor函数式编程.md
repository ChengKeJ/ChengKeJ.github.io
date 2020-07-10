---
layout: post
title:  "Spring5-Reactor函数式编程"
categories: Spring
tags: Spring
---

* content
{:toc}

### 前言

反应式编程是一种可以替代命令式编程的编程范式。这种可替代性存在的原因在于反应式编程解决了命令式编程中的一些限制。理解这些限制，有助于你更好地理解反应式编程模型的优点

<!--more-->

### 反应式流规范

* 对比 Java 中的流


  Java的流和反应式流Java的流和反应式流之间有很多相似之处。首先，它们的名字中都有流（Stream）这个词。

  它们还提供了用于处理数据的函数式API。事实上，正如你稍后将会在介绍Reactor时看到的那样，它们甚至可以共享许多相同的操作。

  Java的流通常都是同步的，并且只能处理有限的数据集。从本质上来说，它们只是使用函数来对集合进行迭代的一种方式。

  反应式流支持异步处理任意大小的数据集，同样也包括无限数据集。只要数据就绪，它们就能实时地处理数据，并且能够通过回压来避免压垮数据的消费者。

* 反应式流规范


  反应式流规范可以总结为4个接口：Publisher、Subscriber、Subscription和Processor。
  
  Publisher负责生成数据，并将数据发送给Subscription（每个Subscriber对应一个Subscription）。
  
  Publisher接口声明了一个方法subscribe()，Subscriber可以通过该方法向Publisher发起订阅。

```

  public interface Publisher<T> {
      void subscribe(Subscriber<? super T> var1);
  }


  public interface Publisher<T> {
      void subscribe(Subscriber<? super T> var1);
  }

  public interface Subscriber<T> {
      void onSubscribe(Subscription var1);

      void onNext(T var1);

      void onError(Throwable var1);

      void onComplete();
  }

  public interface Subscription {
      void request(long var1);

      void cancel();
  }

  public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {
  
  }

```


###  初识Reactor

Reactor项目是反应式流规范的一个实现，提供了一组用于组装反应式流的函数式API。

反应式编程要求我们采取和命令式编程不一样的思维方式。此时我们不会再描述每一步要进行的步骤，反应式编程意味着要构建数据将要流经的管道。当数据流经管道时，可以对它们进行某种形式的修改或者使用。

命令式编程：

```
 String a = "Apple";
 String s = a.toUpperCase();
 String s1 = "hello" + s + "!";
 System.out.println(s1);
```

反应式编程：

```
  Mono.just("Apple")
 .map(String::toUpperCase)
 .map(x-> "hello" + x + "!")
 .subscribe(System.out::println);

```  
控制台：

```
/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/bin/java -Dvisualvm.id=248009374930225 "-javaagent:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar=54832:/Applications/IntelliJ IDEA.app/Contents/bin" -Dfile.encoding=UTF-8 -classpath /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/deploy.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/cldrdata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/dnsns.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jaccess.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jfxrt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/localedata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/nashorn.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunec.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunjce_provider.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunpkcs11.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/zipfs.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/javaws.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jce.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfr.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfxswt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jsse.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/management-agent.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/plugin.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/resources.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/rt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/ant-javafx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/dt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/javafx-mx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/jconsole.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/packager.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/sa-jdi.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/tools.jar:/Users/c.kj/project/super-learn/super-learn-base/target/classes:/usr/local/resp/org/springframework/boot/spring-boot-starter/2.1.3.RELEASE/spring-boot-starter-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot/2.1.3.RELEASE/spring-boot-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/spring-context/5.1.5.RELEASE/spring-context-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-autoconfigure/2.1.3.RELEASE/spring-boot-autoconfigure-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-logging/2.1.3.RELEASE/spring-boot-starter-logging-2.1.3.RELEASE.jar:/usr/local/resp/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar:/usr/local/resp/ch/qos/logback/logback-core/1.2.3/logback-core-1.2.3.jar:/usr/local/resp/org/apache/logging/log4j/log4j-to-slf4j/2.11.2/log4j-to-slf4j-2.11.2.jar:/usr/local/resp/org/apache/logging/log4j/log4j-api/2.11.2/log4j-api-2.11.2.jar:/usr/local/resp/org/slf4j/jul-to-slf4j/1.7.25/jul-to-slf4j-1.7.25.jar:/usr/local/resp/javax/annotation/javax.annotation-api/1.3.2/javax.annotation-api-1.3.2.jar:/usr/local/resp/org/springframework/spring-core/5.1.5.RELEASE/spring-core-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-jcl/5.1.5.RELEASE/spring-jcl-5.1.5.RELEASE.jar:/usr/local/resp/org/yaml/snakeyaml/1.23/snakeyaml-1.23.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web/2.1.3.RELEASE/spring-boot-starter-web-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-json/2.1.3.RELEASE/spring-boot-starter-json-2.1.3.RELEASE.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-databind/2.9.8/jackson-databind-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-core/2.9.8/jackson-core-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jdk8/2.9.8/jackson-datatype-jdk8-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jsr310/2.9.8/jackson-datatype-jsr310-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/module/jackson-module-parameter-names/2.9.8/jackson-module-parameter-names-2.9.8.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-tomcat/2.1.3.RELEASE/spring-boot-starter-tomcat-2.1.3.RELEASE.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-core/9.0.16/tomcat-embed-core-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-el/9.0.16/tomcat-embed-el-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-websocket/9.0.16/tomcat-embed-websocket-9.0.16.jar:/usr/local/resp/org/hibernate/validator/hibernate-validator/6.0.14.Final/hibernate-validator-6.0.14.Final.jar:/usr/local/resp/javax/validation/validation-api/2.0.1.Final/validation-api-2.0.1.Final.jar:/usr/local/resp/org/jboss/logging/jboss-logging/3.3.2.Final/jboss-logging-3.3.2.Final.jar:/usr/local/resp/org/springframework/spring-web/5.1.5.RELEASE/spring-web-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-beans/5.1.5.RELEASE/spring-beans-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-webmvc/5.1.5.RELEASE/spring-webmvc-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-aop/5.1.5.RELEASE/spring-aop-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-expression/5.1.5.RELEASE/spring-expression-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web-services/2.1.3.RELEASE/spring-boot-starter-web-services-2.1.3.RELEASE.jar:/usr/local/resp/com/sun/xml/messaging/saaj/saaj-impl/1.5.0/saaj-impl-1.5.0.jar:/usr/local/resp/javax/xml/soap/javax.xml.soap-api/1.4.0/javax.xml.soap-api-1.4.0.jar:/usr/local/resp/org/jvnet/mimepull/mimepull/1.9.11/mimepull-1.9.11.jar:/usr/local/resp/org/jvnet/staxex/stax-ex/1.8/stax-ex-1.8.jar:/usr/local/resp/javax/xml/ws/jaxws-api/2.3.1/jaxws-api-2.3.1.jar:/usr/local/resp/javax/xml/bind/jaxb-api/2.3.1/jaxb-api-2.3.1.jar:/usr/local/resp/javax/activation/javax.activation-api/1.2.0/javax.activation-api-1.2.0.jar:/usr/local/resp/org/springframework/spring-oxm/5.1.5.RELEASE/spring-oxm-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-ws-core/3.0.6.RELEASE/spring-ws-core-3.0.6.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-xml/3.0.6.RELEASE/spring-xml-3.0.6.RELEASE.jar:/usr/local/resp/commons-io/commons-io/2.5/commons-io-2.5.jar:/usr/local/resp/io/springfox/springfox-swagger2/2.7.0/springfox-swagger2-2.7.0.jar:/usr/local/resp/io/swagger/swagger-models/1.5.13/swagger-models-1.5.13.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-annotations/2.9.0/jackson-annotations-2.9.0.jar:/usr/local/resp/io/springfox/springfox-spi/2.7.0/springfox-spi-2.7.0.jar:/usr/local/resp/io/springfox/springfox-core/2.7.0/springfox-core-2.7.0.jar:/usr/local/resp/io/springfox/springfox-schema/2.7.0/springfox-schema-2.7.0.jar:/usr/local/resp/io/springfox/springfox-swagger-common/2.7.0/springfox-swagger-common-2.7.0.jar:/usr/local/resp/io/springfox/springfox-spring-web/2.7.0/springfox-spring-web-2.7.0.jar:/usr/local/resp/org/reflections/reflections/0.9.11/reflections-0.9.11.jar:/usr/local/resp/org/javassist/javassist/3.21.0-GA/javassist-3.21.0-GA.jar:/usr/local/resp/com/google/guava/guava/18.0/guava-18.0.jar:/usr/local/resp/com/fasterxml/classmate/1.4.0/classmate-1.4.0.jar:/usr/local/resp/org/slf4j/slf4j-api/1.7.25/slf4j-api-1.7.25.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-core/1.2.0.RELEASE/spring-plugin-core-1.2.0.RELEASE.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-metadata/1.2.0.RELEASE/spring-plugin-metadata-1.2.0.RELEASE.jar:/usr/local/resp/org/mapstruct/mapstruct/1.1.0.Final/mapstruct-1.1.0.Final.jar:/usr/local/resp/io/springfox/springfox-swagger-ui/2.7.0/springfox-swagger-ui-2.7.0.jar:/usr/local/resp/io/swagger/swagger-annotations/1.5.13/swagger-annotations-1.5.13.jar:/usr/local/resp/net/bytebuddy/byte-buddy/1.9.10/byte-buddy-1.9.10.jar:/usr/local/resp/io/projectreactor/reactor-core/3.2.6.RELEASE/reactor-core-3.2.6.RELEASE.jar:/usr/local/resp/org/reactivestreams/reactive-streams/1.0.2/reactive-streams-1.0.2.jar:/usr/local/resp/io/projectreactor/reactor-test/3.2.6.RELEASE/reactor-test-3.2.6.RELEASE.jar com.ckj.superlearn.superlearn.base.ReactorStrategy
helloAPPLE!
14:36:38.685 [main] DEBUG reactor.util.Loggers$LoggerFactory - Using Slf4j logging framework
helloAPPLE!
```

这个例子中的Mono是Reactor的两种核心类型之一，另一个类型是Flux。两者都实现了反应式流的Publisher接口。Flux代表具有零个、一个或者多个（可能是无限个）数据项的管道.

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gglvncheldj31aw0jcai2.jpg)


### 添加Reactor依赖

要开始使用Reactor，请将下面的依赖项添加到项目的构建文件中：

```
        <!--reactor core-->
	<dependency>
		<groupId>io.projectreactor</groupId>
		<artifactId>reactor-core</artifactId>
	</dependency>
		<!--reactor test-->
	<dependency>
		<groupId>io.projectreactor</groupId>
		<artifactId>reactor-test</artifactId>
		<version>3.2.6.RELEASE</version>
		<scope>compile</scope>
	</dependency>
		
```

### 使用常见的反应式操作

Flux和Mono是Reactor提供的最基础的构建块，而这两种反应式类型所提供的操作符则是组合使用它们以构建数据流动管线的黏合剂。

Flux和Mono共有500多个操作，这些操作都可以大致归类为：

•创建操作；

•组合操作；

•转换操作；

•逻辑操作。

* 1 创建
		
```

       Flux<String> fruitFlux = Flux.just("Apple","Orange");

       fruitFlux.subscribe(System.out::println);

```
这里传递给subscribe()方法的lambda表达式实际上是一个java.util.Consumer，用来创建反应式流的Subscriber。在调用subscribe()之后，数据会开始流动。在这个例子中，没有中间操作，所以数据从Flux直接流向订阅者

要验证预定义的数据是否流经了fruitFlux，我们可以编写如下所示的测试代码：

```

       StepVerifier.create(fruitFlux)
       .expectNext("Apple")
       .expectNext("Orange")
       .verifyComplete();
```

在这个例子中，StepVerifier订阅了fruitFlux，然后断言Flux中的每个数据项是否与预期的水果名称相匹配。最后，它验证Flux在发布完“Strawberry”之后，整个fruitFlux正常完成。

还可以从数组 集合 Java Stream来作为Flux的源。
```
      List<String> list = Lists.newArrayList();
        list.add("Apple");
        list.add("Orange");
        Flux<String> stringFlux = Flux.fromIterable(list);
```
![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gglweh3r7uj322k0u0ay8.jpg)

例如，要创建一个每秒发布一个值的Flux，你可以使用Flux上的静态interval() 方法，如下所示：

```
   Flux<Long> take = Flux.interval(Duration.ofSeconds(1)).take(5);
   
```
通过interval()方法创建的Flux会从0开始发布值，并且后续的条目依次递增。此外，因为interval()方法没有指定最大值，所以它可能会永远运行。我们也可以使用take()方法将结果限制为前5个条目

* 2 组合反应式类型

有时候，我们会需要操作两种反应式类型，并以某种方式将它们合并在一起。或者，在其他情况下，我们可能需要将Flux拆分为多种反应式类型

合并:

```
        Flux<String> fruitFluxA = Flux.just("Apple","Orange");

        Flux<String> fruitFluxB = Flux.just("Banana","watermelon");
        
        fruitFluxA.mergeWith(fruitFluxB).subscribe(System.out::println);
```

```
/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/bin/java -Dvisualvm.id=253197935683320 "-javaagent:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar=55963:/Applications/IntelliJ IDEA.app/Contents/bin" -Dfile.encoding=UTF-8 -classpath /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/deploy.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/cldrdata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/dnsns.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jaccess.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jfxrt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/localedata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/nashorn.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunec.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunjce_provider.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunpkcs11.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/zipfs.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/javaws.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jce.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfr.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfxswt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jsse.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/management-agent.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/plugin.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/resources.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/rt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/ant-javafx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/dt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/javafx-mx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/jconsole.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/packager.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/sa-jdi.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/tools.jar:/Users/c.kj/project/super-learn/super-learn-base/target/classes:/usr/local/resp/org/springframework/boot/spring-boot-starter/2.1.3.RELEASE/spring-boot-starter-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot/2.1.3.RELEASE/spring-boot-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/spring-context/5.1.5.RELEASE/spring-context-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-autoconfigure/2.1.3.RELEASE/spring-boot-autoconfigure-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-logging/2.1.3.RELEASE/spring-boot-starter-logging-2.1.3.RELEASE.jar:/usr/local/resp/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar:/usr/local/resp/ch/qos/logback/logback-core/1.2.3/logback-core-1.2.3.jar:/usr/local/resp/org/apache/logging/log4j/log4j-to-slf4j/2.11.2/log4j-to-slf4j-2.11.2.jar:/usr/local/resp/org/apache/logging/log4j/log4j-api/2.11.2/log4j-api-2.11.2.jar:/usr/local/resp/org/slf4j/jul-to-slf4j/1.7.25/jul-to-slf4j-1.7.25.jar:/usr/local/resp/javax/annotation/javax.annotation-api/1.3.2/javax.annotation-api-1.3.2.jar:/usr/local/resp/org/springframework/spring-core/5.1.5.RELEASE/spring-core-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-jcl/5.1.5.RELEASE/spring-jcl-5.1.5.RELEASE.jar:/usr/local/resp/org/yaml/snakeyaml/1.23/snakeyaml-1.23.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web/2.1.3.RELEASE/spring-boot-starter-web-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-json/2.1.3.RELEASE/spring-boot-starter-json-2.1.3.RELEASE.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-databind/2.9.8/jackson-databind-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-core/2.9.8/jackson-core-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jdk8/2.9.8/jackson-datatype-jdk8-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jsr310/2.9.8/jackson-datatype-jsr310-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/module/jackson-module-parameter-names/2.9.8/jackson-module-parameter-names-2.9.8.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-tomcat/2.1.3.RELEASE/spring-boot-starter-tomcat-2.1.3.RELEASE.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-core/9.0.16/tomcat-embed-core-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-el/9.0.16/tomcat-embed-el-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-websocket/9.0.16/tomcat-embed-websocket-9.0.16.jar:/usr/local/resp/org/hibernate/validator/hibernate-validator/6.0.14.Final/hibernate-validator-6.0.14.Final.jar:/usr/local/resp/javax/validation/validation-api/2.0.1.Final/validation-api-2.0.1.Final.jar:/usr/local/resp/org/jboss/logging/jboss-logging/3.3.2.Final/jboss-logging-3.3.2.Final.jar:/usr/local/resp/org/springframework/spring-web/5.1.5.RELEASE/spring-web-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-beans/5.1.5.RELEASE/spring-beans-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-webmvc/5.1.5.RELEASE/spring-webmvc-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-aop/5.1.5.RELEASE/spring-aop-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-expression/5.1.5.RELEASE/spring-expression-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web-services/2.1.3.RELEASE/spring-boot-starter-web-services-2.1.3.RELEASE.jar:/usr/local/resp/com/sun/xml/messaging/saaj/saaj-impl/1.5.0/saaj-impl-1.5.0.jar:/usr/local/resp/javax/xml/soap/javax.xml.soap-api/1.4.0/javax.xml.soap-api-1.4.0.jar:/usr/local/resp/org/jvnet/mimepull/mimepull/1.9.11/mimepull-1.9.11.jar:/usr/local/resp/org/jvnet/staxex/stax-ex/1.8/stax-ex-1.8.jar:/usr/local/resp/javax/xml/ws/jaxws-api/2.3.1/jaxws-api-2.3.1.jar:/usr/local/resp/javax/xml/bind/jaxb-api/2.3.1/jaxb-api-2.3.1.jar:/usr/local/resp/javax/activation/javax.activation-api/1.2.0/javax.activation-api-1.2.0.jar:/usr/local/resp/org/springframework/spring-oxm/5.1.5.RELEASE/spring-oxm-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-ws-core/3.0.6.RELEASE/spring-ws-core-3.0.6.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-xml/3.0.6.RELEASE/spring-xml-3.0.6.RELEASE.jar:/usr/local/resp/commons-io/commons-io/2.5/commons-io-2.5.jar:/usr/local/resp/io/springfox/springfox-swagger2/2.7.0/springfox-swagger2-2.7.0.jar:/usr/local/resp/io/swagger/swagger-models/1.5.13/swagger-models-1.5.13.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-annotations/2.9.0/jackson-annotations-2.9.0.jar:/usr/local/resp/io/springfox/springfox-spi/2.7.0/springfox-spi-2.7.0.jar:/usr/local/resp/io/springfox/springfox-core/2.7.0/springfox-core-2.7.0.jar:/usr/local/resp/io/springfox/springfox-schema/2.7.0/springfox-schema-2.7.0.jar:/usr/local/resp/io/springfox/springfox-swagger-common/2.7.0/springfox-swagger-common-2.7.0.jar:/usr/local/resp/io/springfox/springfox-spring-web/2.7.0/springfox-spring-web-2.7.0.jar:/usr/local/resp/org/reflections/reflections/0.9.11/reflections-0.9.11.jar:/usr/local/resp/org/javassist/javassist/3.21.0-GA/javassist-3.21.0-GA.jar:/usr/local/resp/com/google/guava/guava/18.0/guava-18.0.jar:/usr/local/resp/com/fasterxml/classmate/1.4.0/classmate-1.4.0.jar:/usr/local/resp/org/slf4j/slf4j-api/1.7.25/slf4j-api-1.7.25.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-core/1.2.0.RELEASE/spring-plugin-core-1.2.0.RELEASE.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-metadata/1.2.0.RELEASE/spring-plugin-metadata-1.2.0.RELEASE.jar:/usr/local/resp/org/mapstruct/mapstruct/1.1.0.Final/mapstruct-1.1.0.Final.jar:/usr/local/resp/io/springfox/springfox-swagger-ui/2.7.0/springfox-swagger-ui-2.7.0.jar:/usr/local/resp/io/swagger/swagger-annotations/1.5.13/swagger-annotations-1.5.13.jar:/usr/local/resp/net/bytebuddy/byte-buddy/1.9.10/byte-buddy-1.9.10.jar:/usr/local/resp/io/projectreactor/reactor-core/3.2.6.RELEASE/reactor-core-3.2.6.RELEASE.jar:/usr/local/resp/org/reactivestreams/reactive-streams/1.0.2/reactive-streams-1.0.2.jar:/usr/local/resp/io/projectreactor/reactor-test/3.2.6.RELEASE/reactor-test-3.2.6.RELEASE.jar com.ckj.superlearn.superlearn.base.ReactorStrategy
16:03:07.343 [main] DEBUG reactor.util.Loggers$LoggerFactory - Using Slf4j logging framework
Apple
Orange
Banana
watermelon

Process finished with exit code 0
```
![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gglzay93rfj30xs0esafw.jpg)

mergeWith()方法不能完美地保证源Flux之间的先后顺序，所以我们可以考虑使用zip()方法

```

        Flux<String> fruitFluxA = Flux.just("Apple","Orange").delayElements(Duration.ofMillis(10));

        Flux<String> fruitFluxB = Flux.just("Banana","watermelon").delayElements(Duration.ofMillis(50));

        Flux<String> allFlux = fruitFluxA.mergeWith(fruitFluxB);

        allFlux.subscribe(x-> System.out.println("allFlux:"+x));

        Flux<Tuple2<String, String>> zip = Flux.zip(fruitFluxA, fruitFluxB);

        zip.subscribe(x-> System.out.println("zip:"+x));

        Thread.sleep(1000);

```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gglzc3zljuj312k0fqq76.jpg)

控制台：

```
/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/bin/java -Dvisualvm.id=255856816442692 "-javaagent:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar=64981:/Applications/IntelliJ IDEA.app/Contents/bin" -Dfile.encoding=UTF-8 -classpath /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/deploy.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/cldrdata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/dnsns.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jaccess.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jfxrt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/localedata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/nashorn.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunec.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunjce_provider.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunpkcs11.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/zipfs.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/javaws.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jce.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfr.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfxswt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jsse.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/management-agent.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/plugin.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/resources.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/rt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/ant-javafx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/dt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/javafx-mx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/jconsole.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/packager.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/sa-jdi.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/tools.jar:/Users/c.kj/project/super-learn/super-learn-base/target/classes:/usr/local/resp/org/springframework/boot/spring-boot-starter/2.1.3.RELEASE/spring-boot-starter-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot/2.1.3.RELEASE/spring-boot-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/spring-context/5.1.5.RELEASE/spring-context-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-autoconfigure/2.1.3.RELEASE/spring-boot-autoconfigure-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-logging/2.1.3.RELEASE/spring-boot-starter-logging-2.1.3.RELEASE.jar:/usr/local/resp/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar:/usr/local/resp/ch/qos/logback/logback-core/1.2.3/logback-core-1.2.3.jar:/usr/local/resp/org/apache/logging/log4j/log4j-to-slf4j/2.11.2/log4j-to-slf4j-2.11.2.jar:/usr/local/resp/org/apache/logging/log4j/log4j-api/2.11.2/log4j-api-2.11.2.jar:/usr/local/resp/org/slf4j/jul-to-slf4j/1.7.25/jul-to-slf4j-1.7.25.jar:/usr/local/resp/javax/annotation/javax.annotation-api/1.3.2/javax.annotation-api-1.3.2.jar:/usr/local/resp/org/springframework/spring-core/5.1.5.RELEASE/spring-core-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-jcl/5.1.5.RELEASE/spring-jcl-5.1.5.RELEASE.jar:/usr/local/resp/org/yaml/snakeyaml/1.23/snakeyaml-1.23.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web/2.1.3.RELEASE/spring-boot-starter-web-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-json/2.1.3.RELEASE/spring-boot-starter-json-2.1.3.RELEASE.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-databind/2.9.8/jackson-databind-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-core/2.9.8/jackson-core-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jdk8/2.9.8/jackson-datatype-jdk8-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jsr310/2.9.8/jackson-datatype-jsr310-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/module/jackson-module-parameter-names/2.9.8/jackson-module-parameter-names-2.9.8.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-tomcat/2.1.3.RELEASE/spring-boot-starter-tomcat-2.1.3.RELEASE.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-core/9.0.16/tomcat-embed-core-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-el/9.0.16/tomcat-embed-el-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-websocket/9.0.16/tomcat-embed-websocket-9.0.16.jar:/usr/local/resp/org/hibernate/validator/hibernate-validator/6.0.14.Final/hibernate-validator-6.0.14.Final.jar:/usr/local/resp/javax/validation/validation-api/2.0.1.Final/validation-api-2.0.1.Final.jar:/usr/local/resp/org/jboss/logging/jboss-logging/3.3.2.Final/jboss-logging-3.3.2.Final.jar:/usr/local/resp/org/springframework/spring-web/5.1.5.RELEASE/spring-web-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-beans/5.1.5.RELEASE/spring-beans-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-webmvc/5.1.5.RELEASE/spring-webmvc-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-aop/5.1.5.RELEASE/spring-aop-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-expression/5.1.5.RELEASE/spring-expression-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web-services/2.1.3.RELEASE/spring-boot-starter-web-services-2.1.3.RELEASE.jar:/usr/local/resp/com/sun/xml/messaging/saaj/saaj-impl/1.5.0/saaj-impl-1.5.0.jar:/usr/local/resp/javax/xml/soap/javax.xml.soap-api/1.4.0/javax.xml.soap-api-1.4.0.jar:/usr/local/resp/org/jvnet/mimepull/mimepull/1.9.11/mimepull-1.9.11.jar:/usr/local/resp/org/jvnet/staxex/stax-ex/1.8/stax-ex-1.8.jar:/usr/local/resp/javax/xml/ws/jaxws-api/2.3.1/jaxws-api-2.3.1.jar:/usr/local/resp/javax/xml/bind/jaxb-api/2.3.1/jaxb-api-2.3.1.jar:/usr/local/resp/javax/activation/javax.activation-api/1.2.0/javax.activation-api-1.2.0.jar:/usr/local/resp/org/springframework/spring-oxm/5.1.5.RELEASE/spring-oxm-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-ws-core/3.0.6.RELEASE/spring-ws-core-3.0.6.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-xml/3.0.6.RELEASE/spring-xml-3.0.6.RELEASE.jar:/usr/local/resp/commons-io/commons-io/2.5/commons-io-2.5.jar:/usr/local/resp/io/springfox/springfox-swagger2/2.7.0/springfox-swagger2-2.7.0.jar:/usr/local/resp/io/swagger/swagger-models/1.5.13/swagger-models-1.5.13.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-annotations/2.9.0/jackson-annotations-2.9.0.jar:/usr/local/resp/io/springfox/springfox-spi/2.7.0/springfox-spi-2.7.0.jar:/usr/local/resp/io/springfox/springfox-core/2.7.0/springfox-core-2.7.0.jar:/usr/local/resp/io/springfox/springfox-schema/2.7.0/springfox-schema-2.7.0.jar:/usr/local/resp/io/springfox/springfox-swagger-common/2.7.0/springfox-swagger-common-2.7.0.jar:/usr/local/resp/io/springfox/springfox-spring-web/2.7.0/springfox-spring-web-2.7.0.jar:/usr/local/resp/org/reflections/reflections/0.9.11/reflections-0.9.11.jar:/usr/local/resp/org/javassist/javassist/3.21.0-GA/javassist-3.21.0-GA.jar:/usr/local/resp/com/google/guava/guava/18.0/guava-18.0.jar:/usr/local/resp/com/fasterxml/classmate/1.4.0/classmate-1.4.0.jar:/usr/local/resp/org/slf4j/slf4j-api/1.7.25/slf4j-api-1.7.25.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-core/1.2.0.RELEASE/spring-plugin-core-1.2.0.RELEASE.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-metadata/1.2.0.RELEASE/spring-plugin-metadata-1.2.0.RELEASE.jar:/usr/local/resp/org/mapstruct/mapstruct/1.1.0.Final/mapstruct-1.1.0.Final.jar:/usr/local/resp/io/springfox/springfox-swagger-ui/2.7.0/springfox-swagger-ui-2.7.0.jar:/usr/local/resp/io/swagger/swagger-annotations/1.5.13/swagger-annotations-1.5.13.jar:/usr/local/resp/net/bytebuddy/byte-buddy/1.9.10/byte-buddy-1.9.10.jar:/usr/local/resp/io/projectreactor/reactor-core/3.2.6.RELEASE/reactor-core-3.2.6.RELEASE.jar:/usr/local/resp/org/reactivestreams/reactive-streams/1.0.2/reactive-streams-1.0.2.jar:/usr/local/resp/io/projectreactor/reactor-test/3.2.6.RELEASE/reactor-test-3.2.6.RELEASE.jar com.ckj.superlearn.superlearn.base.ReactorStrategy
16:49:44.543 [main] DEBUG reactor.util.Loggers$LoggerFactory - Using Slf4j logging framework
allFlux:Apple
allFlux:Orange
allFlux:Banana
zip:[Apple,Banana]
allFlux:watermelon
zip:[Orange,watermelon]

Process finished with exit code 0

```
* 3 转换和过滤反应式流

针对具有多个数据项的Flux，skip操作将创建一个新的Flux，它会首先跳过指定数量的数据项，然后从源Flux中发布剩余的数据项。下面的测试方法展示如何使用skip()方法：

```
       Flux<String> fruitFluxA = Flux.just("Apple","Orange","Banana","watermelon").skip(2);

        fruitFluxA.subscribe(x->{

            System.out.println(x);

        });
```

```
/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/bin/java -Dvisualvm.id=256772389676087 "-javaagent:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar=58080:/Applications/IntelliJ IDEA.app/Contents/bin" -Dfile.encoding=UTF-8 -classpath /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/deploy.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/cldrdata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/dnsns.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jaccess.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jfxrt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/localedata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/nashorn.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunec.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunjce_provider.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunpkcs11.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/zipfs.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/javaws.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jce.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfr.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfxswt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jsse.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/management-agent.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/plugin.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/resources.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/rt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/ant-javafx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/dt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/javafx-mx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/jconsole.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/packager.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/sa-jdi.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/tools.jar:/Users/c.kj/project/super-learn/super-learn-base/target/classes:/usr/local/resp/org/springframework/boot/spring-boot-starter/2.1.3.RELEASE/spring-boot-starter-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot/2.1.3.RELEASE/spring-boot-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/spring-context/5.1.5.RELEASE/spring-context-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-autoconfigure/2.1.3.RELEASE/spring-boot-autoconfigure-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-logging/2.1.3.RELEASE/spring-boot-starter-logging-2.1.3.RELEASE.jar:/usr/local/resp/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar:/usr/local/resp/ch/qos/logback/logback-core/1.2.3/logback-core-1.2.3.jar:/usr/local/resp/org/apache/logging/log4j/log4j-to-slf4j/2.11.2/log4j-to-slf4j-2.11.2.jar:/usr/local/resp/org/apache/logging/log4j/log4j-api/2.11.2/log4j-api-2.11.2.jar:/usr/local/resp/org/slf4j/jul-to-slf4j/1.7.25/jul-to-slf4j-1.7.25.jar:/usr/local/resp/javax/annotation/javax.annotation-api/1.3.2/javax.annotation-api-1.3.2.jar:/usr/local/resp/org/springframework/spring-core/5.1.5.RELEASE/spring-core-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-jcl/5.1.5.RELEASE/spring-jcl-5.1.5.RELEASE.jar:/usr/local/resp/org/yaml/snakeyaml/1.23/snakeyaml-1.23.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web/2.1.3.RELEASE/spring-boot-starter-web-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-json/2.1.3.RELEASE/spring-boot-starter-json-2.1.3.RELEASE.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-databind/2.9.8/jackson-databind-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-core/2.9.8/jackson-core-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jdk8/2.9.8/jackson-datatype-jdk8-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jsr310/2.9.8/jackson-datatype-jsr310-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/module/jackson-module-parameter-names/2.9.8/jackson-module-parameter-names-2.9.8.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-tomcat/2.1.3.RELEASE/spring-boot-starter-tomcat-2.1.3.RELEASE.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-core/9.0.16/tomcat-embed-core-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-el/9.0.16/tomcat-embed-el-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-websocket/9.0.16/tomcat-embed-websocket-9.0.16.jar:/usr/local/resp/org/hibernate/validator/hibernate-validator/6.0.14.Final/hibernate-validator-6.0.14.Final.jar:/usr/local/resp/javax/validation/validation-api/2.0.1.Final/validation-api-2.0.1.Final.jar:/usr/local/resp/org/jboss/logging/jboss-logging/3.3.2.Final/jboss-logging-3.3.2.Final.jar:/usr/local/resp/org/springframework/spring-web/5.1.5.RELEASE/spring-web-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-beans/5.1.5.RELEASE/spring-beans-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-webmvc/5.1.5.RELEASE/spring-webmvc-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-aop/5.1.5.RELEASE/spring-aop-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-expression/5.1.5.RELEASE/spring-expression-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web-services/2.1.3.RELEASE/spring-boot-starter-web-services-2.1.3.RELEASE.jar:/usr/local/resp/com/sun/xml/messaging/saaj/saaj-impl/1.5.0/saaj-impl-1.5.0.jar:/usr/local/resp/javax/xml/soap/javax.xml.soap-api/1.4.0/javax.xml.soap-api-1.4.0.jar:/usr/local/resp/org/jvnet/mimepull/mimepull/1.9.11/mimepull-1.9.11.jar:/usr/local/resp/org/jvnet/staxex/stax-ex/1.8/stax-ex-1.8.jar:/usr/local/resp/javax/xml/ws/jaxws-api/2.3.1/jaxws-api-2.3.1.jar:/usr/local/resp/javax/xml/bind/jaxb-api/2.3.1/jaxb-api-2.3.1.jar:/usr/local/resp/javax/activation/javax.activation-api/1.2.0/javax.activation-api-1.2.0.jar:/usr/local/resp/org/springframework/spring-oxm/5.1.5.RELEASE/spring-oxm-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-ws-core/3.0.6.RELEASE/spring-ws-core-3.0.6.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-xml/3.0.6.RELEASE/spring-xml-3.0.6.RELEASE.jar:/usr/local/resp/commons-io/commons-io/2.5/commons-io-2.5.jar:/usr/local/resp/io/springfox/springfox-swagger2/2.7.0/springfox-swagger2-2.7.0.jar:/usr/local/resp/io/swagger/swagger-models/1.5.13/swagger-models-1.5.13.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-annotations/2.9.0/jackson-annotations-2.9.0.jar:/usr/local/resp/io/springfox/springfox-spi/2.7.0/springfox-spi-2.7.0.jar:/usr/local/resp/io/springfox/springfox-core/2.7.0/springfox-core-2.7.0.jar:/usr/local/resp/io/springfox/springfox-schema/2.7.0/springfox-schema-2.7.0.jar:/usr/local/resp/io/springfox/springfox-swagger-common/2.7.0/springfox-swagger-common-2.7.0.jar:/usr/local/resp/io/springfox/springfox-spring-web/2.7.0/springfox-spring-web-2.7.0.jar:/usr/local/resp/org/reflections/reflections/0.9.11/reflections-0.9.11.jar:/usr/local/resp/org/javassist/javassist/3.21.0-GA/javassist-3.21.0-GA.jar:/usr/local/resp/com/google/guava/guava/18.0/guava-18.0.jar:/usr/local/resp/com/fasterxml/classmate/1.4.0/classmate-1.4.0.jar:/usr/local/resp/org/slf4j/slf4j-api/1.7.25/slf4j-api-1.7.25.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-core/1.2.0.RELEASE/spring-plugin-core-1.2.0.RELEASE.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-metadata/1.2.0.RELEASE/spring-plugin-metadata-1.2.0.RELEASE.jar:/usr/local/resp/org/mapstruct/mapstruct/1.1.0.Final/mapstruct-1.1.0.Final.jar:/usr/local/resp/io/springfox/springfox-swagger-ui/2.7.0/springfox-swagger-ui-2.7.0.jar:/usr/local/resp/io/swagger/swagger-annotations/1.5.13/swagger-annotations-1.5.13.jar:/usr/local/resp/net/bytebuddy/byte-buddy/1.9.10/byte-buddy-1.9.10.jar:/usr/local/resp/io/projectreactor/reactor-core/3.2.6.RELEASE/reactor-core-3.2.6.RELEASE.jar:/usr/local/resp/org/reactivestreams/reactive-streams/1.0.2/reactive-streams-1.0.2.jar:/usr/local/resp/io/projectreactor/reactor-test/3.2.6.RELEASE/reactor-test-3.2.6.RELEASE.jar com.ckj.superlearn.superlearn.base.ReactorStrategy
17:05:00.141 [main] DEBUG reactor.util.Loggers$LoggerFactory - Using Slf4j logging framework
Banana
watermelon

Process finished with exit code 0
```

与之对应相反的是take()

```
 Flux<String> fruitFluxA = Flux.just("Apple","Orange","Banana","watermelon").take(2);

        fruitFluxA.subscribe(x->{

            System.out.println(x);

        });
```

```

/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/bin/java -Dvisualvm.id=257731721443258 "-javaagent:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar=65410:/Applications/IntelliJ IDEA.app/Contents/bin" -Dfile.encoding=UTF-8 -classpath /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/deploy.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/cldrdata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/dnsns.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jaccess.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jfxrt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/localedata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/nashorn.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunec.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunjce_provider.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunpkcs11.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/zipfs.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/javaws.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jce.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfr.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfxswt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jsse.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/management-agent.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/plugin.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/resources.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/rt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/ant-javafx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/dt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/javafx-mx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/jconsole.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/packager.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/sa-jdi.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/tools.jar:/Users/c.kj/project/super-learn/super-learn-base/target/classes:/usr/local/resp/org/springframework/boot/spring-boot-starter/2.1.3.RELEASE/spring-boot-starter-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot/2.1.3.RELEASE/spring-boot-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/spring-context/5.1.5.RELEASE/spring-context-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-autoconfigure/2.1.3.RELEASE/spring-boot-autoconfigure-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-logging/2.1.3.RELEASE/spring-boot-starter-logging-2.1.3.RELEASE.jar:/usr/local/resp/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar:/usr/local/resp/ch/qos/logback/logback-core/1.2.3/logback-core-1.2.3.jar:/usr/local/resp/org/apache/logging/log4j/log4j-to-slf4j/2.11.2/log4j-to-slf4j-2.11.2.jar:/usr/local/resp/org/apache/logging/log4j/log4j-api/2.11.2/log4j-api-2.11.2.jar:/usr/local/resp/org/slf4j/jul-to-slf4j/1.7.25/jul-to-slf4j-1.7.25.jar:/usr/local/resp/javax/annotation/javax.annotation-api/1.3.2/javax.annotation-api-1.3.2.jar:/usr/local/resp/org/springframework/spring-core/5.1.5.RELEASE/spring-core-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-jcl/5.1.5.RELEASE/spring-jcl-5.1.5.RELEASE.jar:/usr/local/resp/org/yaml/snakeyaml/1.23/snakeyaml-1.23.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web/2.1.3.RELEASE/spring-boot-starter-web-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-json/2.1.3.RELEASE/spring-boot-starter-json-2.1.3.RELEASE.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-databind/2.9.8/jackson-databind-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-core/2.9.8/jackson-core-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jdk8/2.9.8/jackson-datatype-jdk8-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jsr310/2.9.8/jackson-datatype-jsr310-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/module/jackson-module-parameter-names/2.9.8/jackson-module-parameter-names-2.9.8.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-tomcat/2.1.3.RELEASE/spring-boot-starter-tomcat-2.1.3.RELEASE.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-core/9.0.16/tomcat-embed-core-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-el/9.0.16/tomcat-embed-el-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-websocket/9.0.16/tomcat-embed-websocket-9.0.16.jar:/usr/local/resp/org/hibernate/validator/hibernate-validator/6.0.14.Final/hibernate-validator-6.0.14.Final.jar:/usr/local/resp/javax/validation/validation-api/2.0.1.Final/validation-api-2.0.1.Final.jar:/usr/local/resp/org/jboss/logging/jboss-logging/3.3.2.Final/jboss-logging-3.3.2.Final.jar:/usr/local/resp/org/springframework/spring-web/5.1.5.RELEASE/spring-web-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-beans/5.1.5.RELEASE/spring-beans-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-webmvc/5.1.5.RELEASE/spring-webmvc-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-aop/5.1.5.RELEASE/spring-aop-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-expression/5.1.5.RELEASE/spring-expression-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web-services/2.1.3.RELEASE/spring-boot-starter-web-services-2.1.3.RELEASE.jar:/usr/local/resp/com/sun/xml/messaging/saaj/saaj-impl/1.5.0/saaj-impl-1.5.0.jar:/usr/local/resp/javax/xml/soap/javax.xml.soap-api/1.4.0/javax.xml.soap-api-1.4.0.jar:/usr/local/resp/org/jvnet/mimepull/mimepull/1.9.11/mimepull-1.9.11.jar:/usr/local/resp/org/jvnet/staxex/stax-ex/1.8/stax-ex-1.8.jar:/usr/local/resp/javax/xml/ws/jaxws-api/2.3.1/jaxws-api-2.3.1.jar:/usr/local/resp/javax/xml/bind/jaxb-api/2.3.1/jaxb-api-2.3.1.jar:/usr/local/resp/javax/activation/javax.activation-api/1.2.0/javax.activation-api-1.2.0.jar:/usr/local/resp/org/springframework/spring-oxm/5.1.5.RELEASE/spring-oxm-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-ws-core/3.0.6.RELEASE/spring-ws-core-3.0.6.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-xml/3.0.6.RELEASE/spring-xml-3.0.6.RELEASE.jar:/usr/local/resp/commons-io/commons-io/2.5/commons-io-2.5.jar:/usr/local/resp/io/springfox/springfox-swagger2/2.7.0/springfox-swagger2-2.7.0.jar:/usr/local/resp/io/swagger/swagger-models/1.5.13/swagger-models-1.5.13.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-annotations/2.9.0/jackson-annotations-2.9.0.jar:/usr/local/resp/io/springfox/springfox-spi/2.7.0/springfox-spi-2.7.0.jar:/usr/local/resp/io/springfox/springfox-core/2.7.0/springfox-core-2.7.0.jar:/usr/local/resp/io/springfox/springfox-schema/2.7.0/springfox-schema-2.7.0.jar:/usr/local/resp/io/springfox/springfox-swagger-common/2.7.0/springfox-swagger-common-2.7.0.jar:/usr/local/resp/io/springfox/springfox-spring-web/2.7.0/springfox-spring-web-2.7.0.jar:/usr/local/resp/org/reflections/reflections/0.9.11/reflections-0.9.11.jar:/usr/local/resp/org/javassist/javassist/3.21.0-GA/javassist-3.21.0-GA.jar:/usr/local/resp/com/google/guava/guava/18.0/guava-18.0.jar:/usr/local/resp/com/fasterxml/classmate/1.4.0/classmate-1.4.0.jar:/usr/local/resp/org/slf4j/slf4j-api/1.7.25/slf4j-api-1.7.25.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-core/1.2.0.RELEASE/spring-plugin-core-1.2.0.RELEASE.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-metadata/1.2.0.RELEASE/spring-plugin-metadata-1.2.0.RELEASE.jar:/usr/local/resp/org/mapstruct/mapstruct/1.1.0.Final/mapstruct-1.1.0.Final.jar:/usr/local/resp/io/springfox/springfox-swagger-ui/2.7.0/springfox-swagger-ui-2.7.0.jar:/usr/local/resp/io/swagger/swagger-annotations/1.5.13/swagger-annotations-1.5.13.jar:/usr/local/resp/net/bytebuddy/byte-buddy/1.9.10/byte-buddy-1.9.10.jar:/usr/local/resp/io/projectreactor/reactor-core/3.2.6.RELEASE/reactor-core-3.2.6.RELEASE.jar:/usr/local/resp/org/reactivestreams/reactive-streams/1.0.2/reactive-streams-1.0.2.jar:/usr/local/resp/io/projectreactor/reactor-test/3.2.6.RELEASE/reactor-test-3.2.6.RELEASE.jar com.ckj.superlearn.superlearn.base.ReactorStrategy
17:20:59.483 [main] DEBUG reactor.util.Loggers$LoggerFactory - Using Slf4j logging framework
Apple
Orange

Process finished with exit code 0

```
filter()的过滤效果

```
        Flux<String> fruitFluxA = Flux.just("Apple","Orange","Banana","watermelon").take(2);

        fruitFluxA.filter(x->x.equals("Apple")).subscribe(x->{
            System.out.println(x);
        });

```

```
/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/bin/java -Dvisualvm.id=257915486197283 "-javaagent:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar=50866:/Applications/IntelliJ IDEA.app/Contents/bin" -Dfile.encoding=UTF-8 -classpath /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/deploy.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/cldrdata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/dnsns.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jaccess.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/jfxrt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/localedata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/nashorn.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunec.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunjce_provider.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/sunpkcs11.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/ext/zipfs.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/javaws.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jce.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfr.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jfxswt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/jsse.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/management-agent.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/plugin.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/resources.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/jre/lib/rt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/ant-javafx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/dt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/javafx-mx.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/jconsole.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/packager.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/sa-jdi.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/tools.jar:/Users/c.kj/project/super-learn/super-learn-base/target/classes:/usr/local/resp/org/springframework/boot/spring-boot-starter/2.1.3.RELEASE/spring-boot-starter-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot/2.1.3.RELEASE/spring-boot-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/spring-context/5.1.5.RELEASE/spring-context-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-autoconfigure/2.1.3.RELEASE/spring-boot-autoconfigure-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-logging/2.1.3.RELEASE/spring-boot-starter-logging-2.1.3.RELEASE.jar:/usr/local/resp/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar:/usr/local/resp/ch/qos/logback/logback-core/1.2.3/logback-core-1.2.3.jar:/usr/local/resp/org/apache/logging/log4j/log4j-to-slf4j/2.11.2/log4j-to-slf4j-2.11.2.jar:/usr/local/resp/org/apache/logging/log4j/log4j-api/2.11.2/log4j-api-2.11.2.jar:/usr/local/resp/org/slf4j/jul-to-slf4j/1.7.25/jul-to-slf4j-1.7.25.jar:/usr/local/resp/javax/annotation/javax.annotation-api/1.3.2/javax.annotation-api-1.3.2.jar:/usr/local/resp/org/springframework/spring-core/5.1.5.RELEASE/spring-core-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-jcl/5.1.5.RELEASE/spring-jcl-5.1.5.RELEASE.jar:/usr/local/resp/org/yaml/snakeyaml/1.23/snakeyaml-1.23.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web/2.1.3.RELEASE/spring-boot-starter-web-2.1.3.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-json/2.1.3.RELEASE/spring-boot-starter-json-2.1.3.RELEASE.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-databind/2.9.8/jackson-databind-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-core/2.9.8/jackson-core-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jdk8/2.9.8/jackson-datatype-jdk8-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/datatype/jackson-datatype-jsr310/2.9.8/jackson-datatype-jsr310-2.9.8.jar:/usr/local/resp/com/fasterxml/jackson/module/jackson-module-parameter-names/2.9.8/jackson-module-parameter-names-2.9.8.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-tomcat/2.1.3.RELEASE/spring-boot-starter-tomcat-2.1.3.RELEASE.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-core/9.0.16/tomcat-embed-core-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-el/9.0.16/tomcat-embed-el-9.0.16.jar:/usr/local/resp/org/apache/tomcat/embed/tomcat-embed-websocket/9.0.16/tomcat-embed-websocket-9.0.16.jar:/usr/local/resp/org/hibernate/validator/hibernate-validator/6.0.14.Final/hibernate-validator-6.0.14.Final.jar:/usr/local/resp/javax/validation/validation-api/2.0.1.Final/validation-api-2.0.1.Final.jar:/usr/local/resp/org/jboss/logging/jboss-logging/3.3.2.Final/jboss-logging-3.3.2.Final.jar:/usr/local/resp/org/springframework/spring-web/5.1.5.RELEASE/spring-web-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-beans/5.1.5.RELEASE/spring-beans-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-webmvc/5.1.5.RELEASE/spring-webmvc-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-aop/5.1.5.RELEASE/spring-aop-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/spring-expression/5.1.5.RELEASE/spring-expression-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/boot/spring-boot-starter-web-services/2.1.3.RELEASE/spring-boot-starter-web-services-2.1.3.RELEASE.jar:/usr/local/resp/com/sun/xml/messaging/saaj/saaj-impl/1.5.0/saaj-impl-1.5.0.jar:/usr/local/resp/javax/xml/soap/javax.xml.soap-api/1.4.0/javax.xml.soap-api-1.4.0.jar:/usr/local/resp/org/jvnet/mimepull/mimepull/1.9.11/mimepull-1.9.11.jar:/usr/local/resp/org/jvnet/staxex/stax-ex/1.8/stax-ex-1.8.jar:/usr/local/resp/javax/xml/ws/jaxws-api/2.3.1/jaxws-api-2.3.1.jar:/usr/local/resp/javax/xml/bind/jaxb-api/2.3.1/jaxb-api-2.3.1.jar:/usr/local/resp/javax/activation/javax.activation-api/1.2.0/javax.activation-api-1.2.0.jar:/usr/local/resp/org/springframework/spring-oxm/5.1.5.RELEASE/spring-oxm-5.1.5.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-ws-core/3.0.6.RELEASE/spring-ws-core-3.0.6.RELEASE.jar:/usr/local/resp/org/springframework/ws/spring-xml/3.0.6.RELEASE/spring-xml-3.0.6.RELEASE.jar:/usr/local/resp/commons-io/commons-io/2.5/commons-io-2.5.jar:/usr/local/resp/io/springfox/springfox-swagger2/2.7.0/springfox-swagger2-2.7.0.jar:/usr/local/resp/io/swagger/swagger-models/1.5.13/swagger-models-1.5.13.jar:/usr/local/resp/com/fasterxml/jackson/core/jackson-annotations/2.9.0/jackson-annotations-2.9.0.jar:/usr/local/resp/io/springfox/springfox-spi/2.7.0/springfox-spi-2.7.0.jar:/usr/local/resp/io/springfox/springfox-core/2.7.0/springfox-core-2.7.0.jar:/usr/local/resp/io/springfox/springfox-schema/2.7.0/springfox-schema-2.7.0.jar:/usr/local/resp/io/springfox/springfox-swagger-common/2.7.0/springfox-swagger-common-2.7.0.jar:/usr/local/resp/io/springfox/springfox-spring-web/2.7.0/springfox-spring-web-2.7.0.jar:/usr/local/resp/org/reflections/reflections/0.9.11/reflections-0.9.11.jar:/usr/local/resp/org/javassist/javassist/3.21.0-GA/javassist-3.21.0-GA.jar:/usr/local/resp/com/google/guava/guava/18.0/guava-18.0.jar:/usr/local/resp/com/fasterxml/classmate/1.4.0/classmate-1.4.0.jar:/usr/local/resp/org/slf4j/slf4j-api/1.7.25/slf4j-api-1.7.25.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-core/1.2.0.RELEASE/spring-plugin-core-1.2.0.RELEASE.jar:/usr/local/resp/org/springframework/plugin/spring-plugin-metadata/1.2.0.RELEASE/spring-plugin-metadata-1.2.0.RELEASE.jar:/usr/local/resp/org/mapstruct/mapstruct/1.1.0.Final/mapstruct-1.1.0.Final.jar:/usr/local/resp/io/springfox/springfox-swagger-ui/2.7.0/springfox-swagger-ui-2.7.0.jar:/usr/local/resp/io/swagger/swagger-annotations/1.5.13/swagger-annotations-1.5.13.jar:/usr/local/resp/net/bytebuddy/byte-buddy/1.9.10/byte-buddy-1.9.10.jar:/usr/local/resp/io/projectreactor/reactor-core/3.2.6.RELEASE/reactor-core-3.2.6.RELEASE.jar:/usr/local/resp/org/reactivestreams/reactive-streams/1.0.2/reactive-streams-1.0.2.jar:/usr/local/resp/io/projectreactor/reactor-test/3.2.6.RELEASE/reactor-test-3.2.6.RELEASE.jar com.ckj.superlearn.superlearn.base.ReactorStrategy
17:24:03.242 [main] DEBUG reactor.util.Loggers$LoggerFactory - Using Slf4j logging framework
Apple

Process finished with exit code 0
```

如何使用flatMap()方法和subscribeOn()方法

```
        Flux<String> fruitFluxA = Flux.just("Apple", "Orange", "Banana", "watermelon", "Apple", "Orange", "Banana",
                "watermelon", "Apple", "Orange", "Banana", "watermelon", "Apple", "Orange", "Banana", "watermelon");

        fruitFluxA.flatMap(Mono::just).map(String::toUpperCase).subscribeOn(Schedulers.parallel());

```
![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggm1rjb3naj31580kgguh.jpg)

使用flatMap()和subscribeOn()的好处是：我们可以在多个并行线程之间拆分工作，从而增加流的吞吐量。因为工作是并行完成的，无法保证哪项工作首先完成，所以结果Flux中数据项的发布顺序是未知的


  > 原创不易，如果觉得有点用的话，请毫不留情点个赞，转发一下，这将是我持续输出优质文章的最强动力。

