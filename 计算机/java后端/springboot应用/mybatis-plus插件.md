# mybatis-plus

## 逻辑删除

首先，数据库的默认值需要设置为逻辑未删除值，比如0

### 3.1.1版本以上

```yaml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: flag #这个和@TableLogic相似，3.3.0后有一个就行，3.3.0前必须有@TableLogic，这个有没有都行，建议直接使用@TableLogic，这个不用管
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

在实体类上逻辑删除属性上添加@TableLogic

### 3.1.1版本以下

除了需要配置以上版本的，还需要引入逻辑删除插件

```java
@Configuration  
@MapperScan("com.atguigu.cmsService.mapper")  
public class mybatisConfig {  
    //逻辑删除插件  
    @Bean  
    public ISqlInjector sqlInjector() {  
        return new LogicSqlInjector();  
    }  
}
```

## 分页插件

需要引入分页插件

```java
@Configuration  
@MapperScan("com.atguigu.eduService.mapper")  
public class mybatisConfig {  
    /**  
     * 分页插件  
     */  
    @Bean  
    public PaginationInterceptor paginationInterceptor() {  
        return new PaginationInterceptor();  
    }  
}
```

引入分页插件后就可以直接使用page进行查询
```java
Page<CrmBanner> pageBanner = new Page<>(page,limit);  
bannerService.page(pageBanner,null);  
return R.ok().data("item",pageBanner.getRecords()).data("total",pageBanner.getTotal());
```

不引入分页插件service的page方法就是查询全部。

## 自动填充插件

配置插件

```java
@Component  
public class MyMetaObjectHandler implements MetaObjectHandler {  
    @Override  
    public void insertFill(MetaObject metaObject) {  
        //属性名称，不是字段名称  
        this.setFieldValByName("gmtCreate", new Date(), metaObject);  
        this.setFieldValByName("gmtModified", new Date(), metaObject);  
    }  
    @Override  
    public void updateFill(MetaObject metaObject) {  
        this.setFieldValByName("gmtModified", new Date(), metaObject);  
    }  
}
```

插件使用
注意更新时间要用INSERT_UPDATE，否则第一次插入这个更新时间会因没有值报错

```java
@ApiModelProperty(value = "创建时间")  
@TableField(fill = FieldFill.INSERT)  
private Date gmtCreate;  
  
@ApiModelProperty(value = "更新时间")  
@TableField(fill = FieldFill.INSERT_UPDATE)  
private Date gmtModified;
```

