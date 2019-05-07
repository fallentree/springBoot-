# springBoot-
脚踏实地地完成记录

为了记录过程中的每一步
------------------------------------------------------------------
maven, tomcat

## 1、创建

1.1 使用spring initializr（Create Spring Boot applications using Spring Boot Starters)初始化spring boot

1.2 填写的metadata中：Group就是groupId，Artifact就是artifactId

  a、groupId一般分为多个段，常常是域名的倒写。如org.apache.apache，artigactId是tomcat。
    
　　比如创建一个项目，将groupId设置为cn.edu.tju，cn表示域为中国，artifactId设置为demo，表示你这个项目的名称是demo
  
  b、理解Maven中的SNAPSHOT版本和正式版本：https://www.cnblogs.com/huang0925/p/5169624.html
    
  c、name会作为maven生成的project的代码前面部分，比如name='demo' 那么启动类就是'DemoApplication'
	
1.3 选择初始依赖，一般用上Web中的Web、SQL中的JPA、MySQL、MyBatis

  JPA：Java persistence API，Java持久层API，是JDK 5.0注解或XML描述对象－关系表的映射关系，并将运行期的实体对象持久化到数据库中。
	
  MySQL: MySQL的jdbc驱动
	
1.4 projectName：整个项目文件夹的名称


## 2、 运行

2.1 pom.xml中是各种配置信息以及依赖，比如关于MySQL的依赖：

	<dependency>
        	<groupId>mysql</groupId>
        	<artifactId>mysql-connector-java</artifactId>
        	<scope>runtime</scope>
    </dependency>

2.2 Spring boot内置了tomcat服务器，所以可以直接运行。不过运行之前需要先配置application.yml或者application.properties，不配置时报错如下：

  nested exception is org.springframework.boot.autoconfigure.jdbc.DataSourceProperties$DataSourceBeanCreationException: Failed to determine a suitable driver class

即被告知没找到驱动类，虽然刚创建的项目并没有使用到mysql但是spring boot依旧会帮助自动配置，所以需要加载驱动类

  yml 和 properites的配置方法在网上能容易找到，一般只需要配置：数据库驱动url、数据库驱动、用户名、密码、可以配置端口号，注意调整时区
  
## 3、开始动手吧
  
  3.1 涉及技术
  
  a、数据库：注意数据库的规范
      
  b、mybatis：dao（data access object，一个面向对象的数据库接口）层的设计与开发。（基于注解或xml完成sql语句）
      
  基于xml完成sql语句：需要使用mapper-locations，来告诉mybatis使用的xml所在文件夹，具体参考：https://blog.csdn.net/qq_27130997/article/details/81077789
      
  Mybatis在转换Result到需要的Java业务对象时做的三件事，如下:
      
      1. 解决了数据库列名到Java列名的映射。
      
      2.解决了数据库类型到Java类型的转换工作。
      
      3.在转换过程中具备一定的容错能力。
      
  具体是如何将数据库列名转换成对象属性名的参考：http://www.importnew.com/25871.html
      
  mybatis驼峰命名转换的开启参考：https://blog.csdn.net/zhao0416/article/details/78427191 以及官方文档 http://www.mybatis.org/mybatis-3/zh/configuration.html ，该设置项默认为false
      
  c、spring？：Spring IOC 帮助整合Service以及Service的所有依赖  ==> xml 依赖注入 / 基于注解依赖注入
      controller开发
      
# ssm和spring boot有什么区别呢

参考https://www.cnblogs.com/fightingting/p/10587613.html

重要的一点在于springboot 提供的 starter 依赖为我们做了很多配置功能。ssm中web.xml里面写的很多依赖，在spring boot中都不用写。
比如tomcat的配置，ssm就比spring boot复杂，spring boot自己便集成了tomcat

配置mybatis

参考 https://blog.csdn.net/weixin_43094917/article/details/85720969 ，很简单的配置


      
      
  3.2 文件结构
  
  文件结构不是为了固定化，模式化地，僵硬地进行对照，而是为了提供更加清晰的思路，明确整个项目都有什么。
  
  	---src
	   ---main
	      ---java（业务代码）
	         ---com.example.demo...
		 >projectnameApplication.java(整个项目main函数）
		    ---controller
		    ---dao
		    ---dto
		    .
		    .
		    .
		    ---service
	      ---resource（.yml/.xml/.poroperties）
	         >application.yml(application.properties)（很多相关配置信息，诸如数据库的用户密码）
	      ---sql
	   ---test

# 从底端开始

  3.3 数据库设计和编码（简单操作）
  
  关键几点：
  
  a、多写写sql代码，主要是为了熟悉，不用每次都查阅书籍资料
  
  b、一些规范，如：DDL脚本必须保证可重复执行，操作对象必须判断是否存在，根据业务判断重复处理逻辑。DML脚本insert语句需要加入插入数据存在性判断保证可重复执行，update和delete不允许全表操作。都要去注意。
  
  由于数据库涉及的并不是特别深，所以没有特别注意的地方。
  
  
  3.4 DAO层设计
  
  （在这里稍微梳理一下，以便打通这一层，dao层负责数据库的访问，这一层需要设计好数据访问对象，即dao接口（注意是接口），然后通过mybatis框架能够简单地进行对象和实体之间的映射，而mybatis可以采用注解或者xml文件的方式完成这个映射，dao方面提供有参数，由我们自己编写sql进行访问数据库）
  
  这一里明白两个问题："sql写在哪" 以及 "如何实现dao接口"
  
  dao接口需要实现，实现的方式： mapper自动实现dao接口  或者  api编程方式实现dao（=>开启connection => 创建一个statement => 得到resultSet)
  
  由于需要将数据库中的表映射到对象，所以我们还要设计相应的实体对象。
  
  考虑：
  
  	1、实体对象 entity 对象中的属性一般是private（为什么）
  	2、dao接口  dao
  	3、sql的编写 xml
  
