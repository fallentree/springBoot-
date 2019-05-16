# springBoot-
脚踏实地地完成记录

为了记录过程中的每一步
------------------------------------------------------------------
maven, tomcat

## 0、maven的使用

用idea创建java项目，并添加maven管理（先创建再添加），参考：https://blog.csdn.net/xyh930929/article/details/78936611

创建 --> 修改项目结构（参考：https://blog.csdn.net/qq_27093465/article/details/52912444 ） --> 添加maven管理

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
  
  ### 3.1 涉及技术
  
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


      
      
  ### 3.2 文件结构
  
  文件结构不是为了固定化，模式化地，僵硬地进行对照，而是为了提供更加清晰的思路，明确整个项目都有什么。
  
  	---src
	   ---main
	      ---java（业务代码）
	         ---com.example.demo...
		 >projectnameApplication.java(整个项目main函数）
		    ---controller
		    ---dao
		    ---dto（data translation object，与entity类似，但是dto关注的是web和service之间的数据传输）
		    .
		    .
		    .
		    ---exception（service层的一些异常，ps：逻辑操作产生异常）
		    ---service（service接口）
	      ---resource（.yml/.xml/.poroperties）
	         >application.yml(application.properties)（很多相关配置信息，诸如数据库的用户密码）
	      ---sql
	   ---test

# 从底端开始

  ### 3.3 数据库设计和编码（简单操作）
  
  关键几点：
  
  a、多写写sql代码，主要是为了熟悉，不用每次都查阅书籍资料
  
  b、一些规范，如：DDL脚本必须保证可重复执行，操作对象必须判断是否存在，根据业务判断重复处理逻辑。DML脚本insert语句需要加入插入数据存在性判断保证可重复执行，update和delete不允许全表操作。都要去注意。
  
  由于数据库涉及的并不是特别深，所以没有特别注意的地方。
  
  
  ### 3.4 DAO层设计
  
  3.4.1 设计
  
  （在这里稍微梳理一下，以便打通这一层，dao层负责数据库的访问，这一层需要设计好数据访问对象，即dao接口（注意是接口），然后通过mybatis框架能够简单地进行对象和实体之间的映射，而mybatis可以采用注解或者xml文件的方式完成这个映射，dao方面提供有参数，由我们自己编写sql进行访问数据库）
  
  这一里明白两个问题："sql写在哪" 以及 "如何实现dao接口"
  
  dao接口需要实现，实现的方式： mapper自动实现dao接口 （不仅需要编写xml来  处理实现后sql的操作  以及将接口和xml绑定（namespace），还要有配置告诉mybatis，它帮忙自动实现的接口在哪里@MapperScan） 或者  api编程方式实现dao（=>开启connection => 创建一个statement => 得到resultSet)
  
  由于需要将数据库中的表映射到对象，所以我们还要设计相应的实体对象。
  
  考虑：
  
  	1、实体对象 entity 对象中的属性一般是private（为什么），写出get/set方法
  	2、dao接口  dao
  	3、sql的编写 xml
  
  1、在实体对象编写的过程中可以使用maven中的拓展内容：lombok
  
  2、到了dao的编写，前面一直在描述dao是什么，那么dao接口到底要写些什么
  
  DAO层所定义的接口里的方法都大同小异，这是由我们在DAO层对数据库访问的操作以及与前端定的接口一起来决定的。
  
  对数据库的操作，我们基本要用到的就是新增，更新，删除，查询等方法（CRUD，总体是这些，但还有分类，比如检索单个对象和所有对象）。因而DAO层里面基本上都应该要涵盖这些方法对应的操作。除此之外，可以定义一些自定义的特殊的对数据库访问的方法。   
  
  dao的接口需要传递什么参数呢？（自定义对象？可以，但是要求：
  1、要在对象参数前面加@Param指明对象参数在xml文件中的名字，2、xml里面sql语句用objectname.attributeName，3、sql语句可以像一般那样只处理部分字段，也可以接受null）
  
  3、编写xml：编写的xml通常会和被实现的dao同名

  可以多阅读阅读官方文档，比如mybatis的官方文档中有一段有意思的，提到：
  
  “你已经见过简单映射语句的示例了，但并没有显式指定 resultMap。比如：

	<select id="selectUsers" resultType="map">
		select id, username, hashedPassword
		from some_table
		where id = #{id}
	</select>

  上述语句只是简单地将所有的列映射到 HashMap 的键上，这由 resultType 属性指定。虽然在大部分情况下都够用，但是 HashMap 不是一个很好的领域模型。
  你的程序更可能会使用 JavaBean 或 POJO（Plain Old Java Objects，普通老式 Java 对象）作为领域模型。MyBatis 对两者都提供了支持。”
  
  这一段能更好地明白xml到dao实现中映射的一些关系（通过在resultType中指定自己编写的java bean，能够将数据库中的数据很好的映射到实体）。更多在http://www.mybatis.org/mybatis-3/zh/sqlmap-xml.html 的“结果映射”中。
  
  一些特殊的xml的编写：
  
  模糊查询：http://www.cnblogs.com/MaxElephant/p/8418276.html
  
  	select * from m_device
        where device_name like "%"#{deviceName}"%"
        or device_alias like "%"#{deviceName}"%"
  
  3.4.2 dao测试
  spring boot使用junit进行测试一直无法完成。
  
  思考： dao层工作变成  接口编写 ==> sql编写
  
  ### 3.5 Service的编写
  
  Q：（宏观上）Service层和dao层区别在哪里，我为什么要有一个service层，直接用dao？
  
  #### TASK：明白每个注解都是什么意思，是真正梳理通一个小demo的关键
  
  逻辑代码放在service编写，dao更像是对数据库的基本操作，而service针对业务逻辑，将dao中给出的基本操作进行拼接。且，service接口的编写要站在使用者（前端开发人员）的角度进行编写，而不应该站在自己的实现角度进行，而有时候，一些业务逻辑返回的数据会比较复杂，可能是几个实体各自的一些属性，也可能是一些临时生成的信息，这时候可以用dto和前端进行数据交流，很方便。再有一点就是，出现复杂业务逻辑的同时，也就出现了各种异常！！！！！
  
  ### Service的实现
  
  注意到Service要使用dao层的实现，但是dao的实现都是由spring自动完成的，所以我们需要采用@Resource将容器中的实现注入到类中。
  
  ## Spring 管理 Service 依赖
  
  理论： 详细的理论问题查看视频 https://www.imooc.com/video/11721 温故知新
  
  配置： 
  
  ### 3.6 controller的实现
  
  #### 关于api编写的问题，restful接口具体参考：https://blog.csdn.net/xiaoqiangyonghu/article/details/80664983
  
  controller实现并启动tomcat的时候出现过这样一个问题：
  
  	Error creating bean with name 'userServiceImpl': Injection of resource dependencies failed; 
	nested exception is org.springframework.beans.factory.
	NoSuchBeanDefinitionException: No qualifying bean of type 'com.example.demo.dao.userDao' available: expected at least 1 bean which qualifies as autowire candidate.
  
  并在信息中提供了warming：No MyBatis mapper was found in '[com.example.demo]' package. Please check your configuration.
  
  原因是：在应用入口类：XxxApplication.java中没有加入@MapperScan("")注解；解决问题时参考了：https://blog.csdn.net/qinxian20120/article/details/80255976 具体填写的是@MapperScan("com.example.demo.dao")。作用是：指定basePackages，扫描mybatis Mapper接口类（前面提到了mybatis通过mapper的方式帮助自动完成了dao接口的实现，这个注解就是帮助mybatis找到dao接口的位置）（有疑问）
  
  注解@controller以及@RequestMapping
  
  1、controller中要添加@Controller注释，Service实现要添加@Service注释，以告知spring容器
  
  2、@RequestMapping
  
  3、@Resource注解
  
  #### 4、@RequestParam注解和@RequestBody注解
  
  这两个注解的混淆导致了下面这个bug：
  
  	Failed to read HTTP message: org.springframework.http.converter.HttpMessageNotReadableException: Required request body is missing: public xxxxxxxx.
  
  ##### @RequestParam
  
  注解中没写，require = false 还导致了这个bug：
  
  	Required String parameter 'userName' is not present
	
  设置完后@RequestParam注解还导致过下面这个问题：
  
  	Optional long parameter 'userAccount' is present but cannot be translated into a null value due to being ...

  原因是由于设置成“非必须”的，所以这个参数有可能接受null的传递，但是原来的形参列表中，参数类型是long，不能被转化成null，改成Long（对象类型）即可。
  
  出现了问题：在通过postman post一个json数据给后端时，插入数据库的数据全是null，经过排查是postman传递的数据并没被接受。
  
  原因是@RequestParam接受的参数需要是表单类型的数据，即form-data类型，所以在postman中修改成表单即可。
  
  5、@ResponseBody注解：
  
  ##### 这里@ResponseBody(方法级别注解)和@RestController(类级别注解)都提供了将对象转化成json数据的动态代理
  
  这个注解的缺失导致了下面问题：
  
  	Unknown return value type: java.lang.Integer
	
  在此方法上写上注解@ResponseBody就没问题了。
  
  6、出现了问题：
  
  	nested exception is org.apache.ibatis.binding.BindingException: Parameter 'userName' not found. Available parameters are [arg1, arg0, param1, param2]

  需要在dao接口的参数列表中添加注释@Param来指定参数名，如：
  
  	long registNewUser(@Param("userName")String userName, @Param("password") String password);
	
  也就是说，写在注解后面的那个参数名是没用的（完全可以瞎写），这也是很好理解的，就是编译的时候把参数变成了param1, param2这样的形参，而和它原来的名字没一点关系，这时候就要用注解指定。
  
    该注解的缺失还导致过问题：
	
	org.apache.ibatis.reflection.ReflectionException: There is no getter for property named 'usex' in 'com.example.java.entity.user'
  
  7、出现了问题：
  
  	Invalid bound statement (not found): com.example.demo.dao.userDao.registNewUser
  
  就是mapper文件扫描不到，导致无法绑定，这个时候就体现了mybatis.mapper-locations配置的重要性
    
  至此，一个超级超级超级小的demo打通了
