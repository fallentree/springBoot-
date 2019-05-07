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

