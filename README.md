# SpringCloudDemo #
Simple study notes for springcloud  
- 说明方式，（以eureka为例）：  
     2.3 eureka（主要的技术）    
        模块说明：XXXXXX。（简单介绍使用哪些模块）  
    2.3.1 XXX模块（下面开始对模块的详细介绍）   
      a.说明  
      （对该模块的说明）  
      b.注意事项  
        （过程中需要注意的地方。）  
      c.实现方式（具体实现方式）   
        1. XXXX  
        2. XXXX  
        。。。  
      d.测试启动（如何测试）   
       1. XXXX  
       2. XXXX  
       。。。  
      e.其他（其他额外需要的地方）  
     

## 1 目录结构 ##
    -SpringCloudDemo
    --- 1-db ：存放数据库文件
    --- 2-image ：存放图片文件
    --- common-api ：公共模块
    --- consumer-dept-8002 ：基础例子，没有使用组件
    --- provider-dept-8001 ：基础例子，没有使用组件
    --- eureka-server-7001 ：eureka服务注册中心
    --- eureka-client-8001 ：eureka服务注册
    --- eureka-server-7002 ：配合eureka-server-7001实现服务注册中心的集群
    --- eureka-server-7003 ：配合eureka-server-7001实现服务注册中心的集群
    --- consumer-ribbon-9001 ：集成ribbon
    --- eureka-client-8002 ：配合eureka-client-8001实现多个微服务的运行，可用于负载均衡的测试
    --- eureka-client-8003 ：配合eureka-client-8001实现多个微服务的运行，可用于负载均衡的测试

## 2 目录说明 ##
- ***因为牵涉到消费者，服务者等相关内容，每次测试可能会牵涉多个模块，这里分组介绍***。
### 2.1 公共模块 ###
#### 2.1.1 common-api ####
##### a.说明 ##### 
   - 公共模块，目前提供了公共实体类。
### 2.2 简单的调用 ###
- ***注意：本组未使用springcloud相关组件。***
#### 2.2.1 consumer-dept-8002 ####
- 基础模块，为以后集成springcloud组件做准备,需要时可以直接从这个模块拷贝东西，例如：service层等。
#### 2.2.2 provider-dept-8002 ####
- 基础模块，为以后集成springcloud组件做准备，需要时可以直接从这个模块拷贝东西，例如：service层等。
### 2.3 eureka ###
- ***模块说明：使用eureka进行服务注册与发现***
#### 2.3.1 eureka-server-7001:服务注册 ####
##### a.说明 #####
- 用于eureka的服务注册管理
##### b.注意事项 #####
    1. 注意版本的对应关系，建议使用父pom集中管理版本信息。
    2. 有些模块用不到有关数据库的相关信息（例如：服务消费者）,所以也不会有数据库的相关配置信息。此时需要手动关闭springboot的数据库的自动装载。并且尽量不要导入多余的包
        (例如：有关使用druid的包)，否则有些包也会自动去寻找配置文件中的相关信息，然后找不到，项目启动就会报错。
##### c.实现方式 #####
1. pom文件增加依赖
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency> 
```
2. 主启动类增加注解:`@EnableEurekaServer`
3. yml配置文件(这里只写增加的配置代码，其他的代码，如：spring的相关配置，数据库的相关配置省略不写。下同)：
```
server:
  port: 7001
eureka:
  instance:
    hostname: localhost #erueka服务端的实例名称
  client:
    register-with-eureka: false #false表示不向注册中心注册自己
    fetch-registry: false #false表示自己端就是注册中心，我的职责是维护服务实例，不需要去检索服务
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```
##### d.测试启动 #####
1. 启动本地项目
2. 然后，在浏览器中输入：`localhost:7001`
3. 然后会打开eureka的管理页面：
<img src="2-image/eureka-server-7001-1.png" width = "800" height = "500" align=center />

#### 2.3.2 eureka-client-8001 ####
##### a.说明 #####
- 模拟eureka的服务提供者，将服务注册到服务注册服务器中。
##### b.注意事项 #####

##### c.实现方法 #####
1. pom文件增加依赖
```
 <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```
2. 主启动类增加注解:`@EnableEurekaClient`
3. yml文件增加：
```
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
```
***别忘了还要在yml文件中增加：***
```
spring:
  application:
    name: eureka-client-8001
```
***这个用来标识该微服务的名称，很重要！很重要！***
##### d.测试 #####
1. 启动eureka-server-7001模块，然后启动本模块
2. 然后，在浏览器中输入：`localhost:7001`
3. 然后会打开eureka的管理页面，会增加一个消费提供者：
<img src="2-image/eureka-client-8001-1.png" width = "800" height = "100" align=center />

##### e.信息完善 #####
1. yml文件增加(为了表明具体位置，这里是全部的yml，增加的内容对比上面的自己补充)：
```
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    instance-id: eureka-8001 #可以给本服务起别名
```
作用：
<img src="2-image/eureka-client-8001-2.png" width = "800" height = "100" align=center />

2. yml文件增加(为了表明具体位置，这里是全部的yml，增加的内容对比上面的自己补充)：
```
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    instance-id: eureka-8001 #可以给本服务起别名
    prefer-ip-address: true #访问路径可以显示ip地址
```
作用：
<img src="2-image/eureka-client-8001-3.png" width = "800" height = "100" align=center />

3. 完善微服务info信息
   1. 首先在本模块pom文件增加：
   ```
    <!--actuator监控信息完善-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-actuator</artifactId>
        </dependency>
   ```
   2. 在父的pom文件添加bulid。这里简单解释一下:就是用maven的这个插件去解析src/main/resources中以$开头和结尾的东西。
   ```
   <build>
        <finalName>SpringCloudDemo</finalName>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <filtering>true</filtering>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <configuration>
                    <delimiters>
                        <delimiter>$</delimiter>
                    </delimiters>
                </configuration>
            </plugin>
        </plugins>
    </build>
   ```
   3. 在本模块的yml文件中增加：
   ```
   info:
      app.name: SpringCloudDemo~~~eureka-client-8001
      company.name: zsk
      bulid.artifactId: $project.artifactId$
      bulid.version: $project.version$
   ```
   *说明：上面的app.name和conpany.name在本例中是写死了，而bulid.artifactId和 bulid.version配合父pom文件的bulid标签内容，自动解析*
最终结果：
<img src="2-image/eureka-client-8001-4.png" width = "500" height = "200" align=center />

##### f.eureka自我保护 #####
    总结：某个时刻某一个微服务不可用了，eureka不会立刻清理，依旧会对该微服务信息进行保存。
    手动禁止(不推荐)：eureka.server.enable-self-preservation: false

***目前有的疑问：服务注册中心有了，服务注册有了（服务提供方），服务消费方怎么消费？服务提供方怎么控制服务的暴露（dubbo可以控制具体哪些方法暴露）？***

### 2.4 eureka服务注册中心集群 ###
* 使用的模块有：eureka-server-7001 ，eureka-server-7002，eureka-server-7003
  同时为了测试方便在本机的hosts文件修改了ip映射：
  添加了：
  ```
  127.0.0.1 eureka7001.com  
  127.0.0.1 eureka7002.com  
  127.0.0.1 eureka7003.com  
  ```
*
1. 修改各个模块的yml文件，以7001为例,其他的类比，port，hostname，和defaultZone需要修改：
```
#7001集群
server:
  port: 7001
eureka:
  instance:
    hostname: eureka7001.com #erueka服务端的实例名称
  client:
    register-with-eureka: false #false表示不向注册中心注册自己
    fetch-registry: false #false表示自己端就是注册中心，我的职责是维护服务实例，不需要去检索服务
    service-url:
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
```
```
#7002集群
server:
  port: 7002
eureka:
  instance:
    hostname: eureka7002.com #erueka服务端的实例名称
  client:
    register-with-eureka: false #false表示不向注册中心注册自己
    fetch-registry: false #false表示自己端就是注册中心，我的职责是维护服务实例，不需要去检索服务
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7003.com:7003/eureka/

```
```
#7003集群
server:
  port: 7003
eureka:
  instance:
    hostname: eureka7003.com #erueka服务端的实例名称
  client:
    register-with-eureka: false #false表示不向注册中心注册自己
    fetch-registry: false #false表示自己端就是注册中心，我的职责是维护服务实例，不需要去检索服务
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
```
2. 7001和7002和7003，主启动类的注解和7001一样
3. 修改eureka-client-8001模块的yml
```
defaultZone: http://erureka7001.com:7001/eureka/,http://erureka7001.com:7002/eureka/,http://erureka7001.com:7003/eureka/  #集群服务注册中心
```
4. 浏览器输入：`http://eureka7001.com`，同时也能查看其它两个运行情况，`http://eureka7002.com`或者`http://eureka7003.com`。例如：eureka7001运行结果
<img src="2-image/eureka-server-more-1.png" width = "800" height = "300" align=center />

### 2.5 ribbon的集成 ###
* 使用的模块：consumer-ribbon-9001 *
#### 2.5.1 consumer-ribbon-9001 ####
#### 使用ribbon默认负载均衡算法（轮询算法）####
- 说明：本次使用ribbon的默认轮询算法，使用模块eureka-client-8001，eureka-client-8002，eureka-client-8003。辅助测试负载均衡效果
##### a.说明 #####
1. 测试负载均衡时需要多个微服务提供者，这里仿照eureka-client-8001，新建两个服务提供者模块分别是eureka-client-8002和eureka-client-8003。
2. 同时为了区分不同的微服务，8002和8003分别访问springcloudde1和springclouddemo2数据库。（dept表中db_source字段不一样，用于区分不同的微服务查询的结果）
3. 修改8002和8003模块的相关信息。主要包括：
***端口号，数据库名称，instance-id***,按照8002为例，如图：
<img src="2-image/ribbon-01.png" width = "800" height = "300" align=center />

##### b.注意事项 #####

##### c.实现方法 #####
1. pom文件（因为需要和eureka集成，所以这里将所有的jar包都放进去了），
```
 <!--自定义的公共模块-->
        <dependency>
            <groupId>cn.zsk</groupId>
            <artifactId>common-api</artifactId>
            <version>${common-api.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>

        <!--Ribbon -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
        </dependency>
```
2. yml 文件
```
server:
  port: 9001


eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
```
3. 主启动类注解
```
@SpringBootApplication(exclude = {
        DataSourceAutoConfiguration.class,
        DataSourceTransactionManagerAutoConfiguration.class,
        HibernateJpaAutoConfiguration.class
})
@EnableEurekaClient
```
4. 在ConfigBean中getRestTemplate方法增加注解`@LoadBalanced`
```
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
```
##### d.测试 #####
1. 启动eureka-server-7001，eureka-server-7002，eureka-server-7003
2. 启动eureka-client-8001，eureka-client-8002，eureka-client-8003
此时如图
<img src="2-image/ribbon-02.png" width = "800" height = "300" align=center />

3. 启动consumer-ribbon-9001模块
4. 打开浏览器，输入`localhost:9001/consumer/dept/list`,多次刷新，可查看到返回的结果是不同数据库的内容
<img src="2-image/ribbon-03.png" width = "500" height = "300" align=center />

5. 如果多次刷新db_resouce字段依次出现不同的的值，说明查询的时候访问的不同的数据库，即访问了不同的微服务，实现了轮询方式的负载均衡
