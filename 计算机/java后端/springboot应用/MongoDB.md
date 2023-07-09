# 简介

## 什么是MongoDB



MongoDB是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。它支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型。

Mongo最大的特点是它支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。



## MongoDB与传统型数据库的区别

传统的关系数据库一般有数据库（database）、表（table）、记录（record）三个层次概念组成，MongoDB是由数据库（database）、集合（collection）、文档（document）三个层次组成

传统的关系型数据库的查询方式都是SQL，Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。


## MongoDB的主要特性

### 高性能

MongoDB提供高性能的数据持久化。特别是，
对嵌入式数据模型的支持减少了数据库系统上的I / O操作。
索引支持更快的查询，并且可以包含来自嵌入式文档和数组的键。

### 丰富的查询语言

MongoDB支持丰富的查询语言以支持读写操作（CRUD）以及：数据聚合文本搜索和地理空间查询。

### 高可用

MongoDB的复制工具（称为副本集）提供：_自动_故障转移、数据冗余。

### 水平扩展

MongoDB提供水平可伸缩性作为其_核心_ 功能的一部分：
分片将数据分布在一个集群的机器上。

从3.4开始，MongoDB支持基于分片键创建数据区域。在平衡群集中，MongoDB仅将区域覆盖的读写定向到区域内的那些分片。

### 支持多种存储引擎

MongoDB支持多个存储引擎：WiredTiger存储引擎（包括对静态加密的支持 ）

内存存储引擎。


# springboot整合MongoDB

## 说明

`spring-data-mongodb` 提供 `MongoTemplate` 与 `MongoRepository`两种操作方式
`MongoRepository` 操作简单 缺点是不够灵活
`MongoTemplate` 操作灵活，在项目中可以灵活使用这两种方式



## 引入

```XML
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```



## 配置

```YAML
spring:
  data:
    mongodb:
      port: 27017
      host: localhost
      # 注意: 每个库对应的用户不同 root用户默认对应admin库
      database: admin
      authentication-database: admin # 登录认证的逻辑库名，这里根据情况修改
      # 如果设置了用户名密码 需要填
      username: root
      # 如果设置了用户名密码 需要填
      password: 123456
```



## 实体类

`@Document`: 标注于实体类上表明由mongo来维护该集合，默认集合名为类名还可手动指定集合名`@Document(collection=user)`
`@Id`: 主键，自带索引由mongo生成对应mongo中的_id字段(ObjectId)
`@Indexed`: 设置该字段索引，提高查询效率，设置参数(unique=true)为唯一索引，默认为false



```Java
/**
 * 文章实体类
 **/

@Data
@Document(collection = "article") //指定要对应的文档名（表名）
@Accessors(chain = true)
public class Article {
    @Id
    private String id;//文章主键

    private String articleName; //文章名

    private String content; //文章内容
}

@Data
@Document("user")
public class User {
	@Id
	private String id;
	private String name;
	private Integer age;
	private String email;
}
```



## MongoTemplate 用法

在service层直接注入MongoTemplate就可以使用其中的方法

```Java
@SpringBootTest
public class MongoTemplateTest {

    @Autowired
    private MongoTemplate mongoTemplate;

	// 添加操作
    @Test
    public void create() {
        User user = new User();
        user.setAge(26);
        user.setName("lionli");
        user.setEmail("XXXXXXX@qq.com");
        User insert = mongoTemplate.insert(user);
        System.out.println(insert);
    }
    
    // 查询所有
	@Test
	public void findAll(){
	    List<User> userList = mongoTemplate.findAll(User.class);
	    System.out.println(userList);
	}

	//根据id查询
	@Test
	public void findId(){
	    User user = mongoTemplate.findById("6422a4ac7716000081001068", User.class);
	    System.out.println(user);
	}
	
	//条件查询
	@Test
	public void findUserList(){
	    Query query = new Query(Criteria.where("name").is("lionli").and("age").is(26));
	    List<User> userList = mongoTemplate.find(query, User.class);
	    System.out.println(userList);
	}
	
	// 模糊查询
    @Test
    public void findLikeUserList(){
        String regex = String.format("%s%s","^.*", "lion");
        Pattern pattern = Pattern.compile(regex, Pattern.CASE_INSENSITIVE);
        Query query = new Query(Criteria.where("name").regex(pattern));
        List<User> userList = mongoTemplate.find(query, User.class);
        System.out.println(userList);
    }
    
    // 分页查询
    @Test
    public void findPageUserList(){
        int pageNum = 1;
        int pageSize = 10;
        //条件构建
        String regex = String.format("%s%s","^.*", "lion");
        Pattern pattern = Pattern.compile(regex, Pattern.CASE_INSENSITIVE);
        Query query = new Query(Criteria.where("name").regex(pattern));
        //分页构建
        //查询记录数
        long count = mongoTemplate.count(query, User.class);
        //分页
        List<User> userList = mongoTemplate.find(query.skip((pageNum - 1) * pageSize).limit(pageSize), User.class);
        System.out.println(count);
        System.out.println(userList);
    }

	// 修改
    @Test
    public void updateUser(){
        //根据id查询数据
        User user = mongoTemplate.findById("6422a4ac7716000081001068", User.class);
        //设置修改的值
        user.setName("crazylionli");
        user.setAge(20);
        //调用方法实现修改操作
        Query query = new Query(Criteria.where("_id").is(user.getId()));
        Update update = new Update();
        update.set("name",user.getName());
        update.set("age",user.getAge());
        UpdateResult upsert = mongoTemplate.upsert(query, update, User.class);
        long count = upsert.getModifiedCount();
        System.out.println(count);
    }
    
    // 删除操作
    @Test
    public void deleteUser(){
        Query query = new Query(Criteria.where("_id").is("6422a4ac7716000081001068"));
        DeleteResult remove = mongoTemplate.remove(query, User.class);
        long deletedCount = remove.getDeletedCount();
        System.out.println(deletedCount);
    }
    
    // 嵌套表添加
	@Test
	public void addTag() {
		Tag tag = new Tag();
		tag.setTagId("1");
		tag.setEnable(true);
		Query query = Query.query(Criteria.where("_id").is("6422a4ac7716000081001068"));
		Update update = new Update();
		update.addToSet("tags", tag);
		mongoTemplate.upsert(query, update, User.class);
	}

	// 更新嵌套表
	@Test
	public void updateTag() {
        Query query = Query.query(Criteria.where("_id").is("6422a4ac7716000081001068")
                .and("tags.tagId").is("1"));
        Update update = new Update();
        update.set("tags.$.enable", false);
        mongoTemplate.updateFirst(query, update, User.class);
	}
	
	// 删除嵌套表
	@Test
	public void removeTag() {
        Query query = Query.query(Criteria.where("_id").is("6422a4ac7716000081001068")
                .and("tags.tagId").is("1"));
        Update update = new Update();
        update.unset("tags.$");
        mongoTemplate.updateFirst(query, update, User.class);
	}
}

```



## MongoRepository 用法

DAO层使用

```Java
/**
 * 继承 MongoRepository<实体类，主键类型>,以实现CRUD
 **/
@Repository
public interface ArticleRepository extends MongoRepository<Article,String> {
	//根据id查询文章
    List<Article> findByid(String id);
}

```



> 这里遇到一个坑，在创建查询方法时，方法名写的是findByArticleId，运行时报了一个No property articleid found for type Article!的错误，这里是因为创建自定义接口时需要遵守MongoRepository的命名规范，这里我们是根据文章id进行查询，而文章的主键名为id，所以这里接口需要起名findBy + 主键名，否则会报找不到属性的错误。



---

这里DAO使用时注入到service中就可以使用了，和mapper差不多，自定义方法的使用有待学习

---



Service接口

```Java
public interface ArticleService {

    /**
     * 添加文章
     * @param article
     * @return
     */
    int create(Article article);

    /**
     * 删除文章
     */

    int delete(List<String> ids);

    /**
     * 根据id查询
     * @param id
     * @return
     */
    List<Article> list(String id);
}

```



Service实现类

```Java
/**
 * ArticleService实现类
 **/
@Service
public class ArticleServiceImpl implements ArticleService {

    @Autowired
    private ArticleRepository articleRepository;

    /**
     * 添加文章
     *
     * @param article
     * @return
     */
    @Override
    public int create(Article article) {
        Article save = articleRepository.save(article);
        return 1;
    }

    /**
     * 删除文章
     *
     * @param ids
     */
    @Override
    public int delete(List<String> ids) {
        List<Article> deleteList = new ArrayList<>();

        for(String id:ids){
            Article article = new Article();
            article.setId(id);
            deleteList.add(article);
        }
        articleRepository.deleteAll(deleteList);

        return ids.size();
    }

    /**
     * 查询文章
     * @param id
     * @return
     */
    @Override
    public List<Article> list(String id) {
        return articleRepository.findByid(id);
    }
}

```



# 多数据源



## 配置

不需要引入额外依赖,配置可以参考

```YAML
mongodb:
  #其他mongodb数据库
  second:
    host: dds-2ze1d95e94a77ba41421-pub.mongodb.rds.aliyuncs.com #指定MongoDB服务地址
    port: 3717 #指定端口，默认就为27017
    database: haruka_second
    authentication-database: admin # 登录认证的逻辑库名
    username: root #用户名
    password: Wang200301 #密码
spring:
  #mongodb主数据库
  data:
    mongodb:
      host: dds-2ze1d95e94a77ba41421-pub.mongodb.rds.aliyuncs.com #指定MongoDB服务地址
      port: 3717 #指定端口，默认就为27017
      database: haruka_primary #指定使用的数据库(集合)
      authentication-database: admin # 登录认证的逻辑库名
      username: root #用户名
      password: Wang200301 #密码
```

要添加更多数据库参考second



## 配置类

```Java
package com.haruka.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.autoconfigure.mongo.MongoProperties;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.data.mongodb.MongoDatabaseFactory;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.SimpleMongoClientDatabaseFactory;

@Configuration
@Slf4j
public class MultipleMongoConfig {
//    Primary标签的意思就是默认使用的数据库，对于MongoTemplate不添加标签时默认使用
    @Primary
    @Bean(name = "primary")
    @ConfigurationProperties(prefix = "spring.data.mongodb")
    public MongoProperties getPrimary() {
        return new MongoProperties();
    }

    @Bean(name = "second")
    @ConfigurationProperties(prefix = "mongodb.second")
    public MongoProperties getSecondary() {
        return new MongoProperties();
    }

    @Primary
    @Bean(name = "primaryMongoTemplate")
    //这个bean的name=PrimaryMongoConfig.MONGO_TEMPLATE
    public MongoTemplate primaryMongoTemplate() throws Exception {
        return new MongoTemplate(primaryFactory(getPrimary()));
    }

    @Bean(name = "secondMongoTemplate")
    public MongoTemplate secondaryMongoTemplate() throws Exception {
        return new MongoTemplate(secondFactory(getSecondary()));
    }

    @Bean
    @Primary
    //这里就是要通过url创建数据源，其实可以在配置文件直接写数据源，看情况修改好吧QAQ
    public MongoDatabaseFactory primaryFactory(final MongoProperties mongo) throws Exception {
        String host = mongo.getHost();
        Integer port = mongo.getPort();
        String database = mongo.getDatabase();
        String authenticationDatabase = mongo.getAuthenticationDatabase();
        String username = mongo.getUsername();
        String password = String.valueOf(mongo.getPassword());
        //mongodb://root@dds-2ze1d95e94a77ba41421-pub.mongodb.rds.aliyuncs.com:3717/?authSource=admin
        String url = "mongodb://" + username + ":" + password + "@" + host + ":" + port + "/" + database + "?authSource="+authenticationDatabase;
        log.debug(url);
        return new SimpleMongoClientDatabaseFactory(url);
    }

    @Bean
    public MongoDatabaseFactory secondFactory(final MongoProperties mongo) throws Exception {
        String host = mongo.getHost();
        Integer port = mongo.getPort();
        String database = mongo.getDatabase();
        String authenticationDatabase = mongo.getAuthenticationDatabase();
        String username = mongo.getUsername();
        String password = String.valueOf(mongo.getPassword());
        //mongodb://root@dds-2ze1d95e94a77ba41421-pub.mongodb.rds.aliyuncs.com:3717/?authSource=admin
        String url = "mongodb://" + username + ":" + password + "@" + host + ":" + port + "/" + database + "?authSource="+authenticationDatabase;
        log.debug(url);
        return new SimpleMongoClientDatabaseFactory(url);
    }

}
```



```Java
package com.haruka.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.data.mongodb.repository.config.EnableMongoRepositories;

@Configuration
//配置对应的dao使用那一个数据库
@EnableMongoRepositories(basePackages = "com.haruka.mongodb.dao.primary",
        mongoTemplateRef = PrimaryMongoConfig.MONGO_TEMPLATE)
public class PrimaryMongoConfig {
    protected static final String MONGO_TEMPLATE = "primaryMongoTemplate";
}

```



```Java
package com.haruka.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.data.mongodb.repository.config.EnableMongoRepositories;

@Configuration
@EnableMongoRepositories(basePackages = "com.haruka.mongodb.dao.second",
        mongoTemplateRef = SecondMongoConfig.MONGO_TEMPLATE)
public class SecondMongoConfig {
    protected static final String MONGO_TEMPLATE = "secondMongoTemplate";
}

```



## 使用

对于MongoRepository,通过上方的配置类的basePackages就已经给其配置了对应的数据库，我们直接使用就是对应的数据库

对于MongoTemplate，我们通过配置上的名字来使用注解

```Java
    @Resource(name = "secondMongoTemplate")
    private MongoTemplate mongoTemplate;
```



