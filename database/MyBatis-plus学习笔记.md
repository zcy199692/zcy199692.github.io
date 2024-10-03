# MyBatis-plus学习笔记

## 使用

引入依赖（尽量不要同时导入mybatis和mybatis-plus！ 版本的差异！）

连接数据库

```properties
spring.datasource.username=root
spring.datasource.password=123
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

编写代码

```java
@Data
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

```java
//在对应的Mapper上继承基本的类baseMapper
public interface UserMapper extends BaseMapper<User> {
    //所有的CRUD已经编写完成
    //不需要像以前的配置一些xml

}
```

```java
@SpringBootApplication
@MapperScan("com.baomidou.mybatisplus.mapper")//扫描mapper文件夹
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(QuickStartApplication.class, args);
    }

}
```

进行测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SampleTest {

    @Autowired
    private UserMapper userMapper;

    @Test
    public void testSelect() {
        System.out.println(("----- selectAll method test ------"));
        //参数是一个Wrapper，条件结构器，这里先不用 填null
        //查询所有的用户 
        List<User> userList = userMapper.selectList(null);
        Assert.assertEquals(5, userList.size());
        userList.forEach(System.out::println);
    }

}

```

注：UserMapper 中的 `selectList()` 方法的参数为 MP 内置的条件封装器 `Wrapper`，所以不填写就是无任何条件

## 常用注释

@TableName("表名")：当表名与实体类名不一致时，可以在实体类上加入@TableName（）声明

`@TableId`声明属性为表中的[主键](https://so.csdn.net/so/search?q=主键&spm=1001.2101.3001.7020)（若属性名称不为默认id）

`@TableFieId("字段")` 当实体类属性与表字段不一致时，可以用来声明

```java
@TableName("表名")
public class User{
    
    @TableId
    private Long userId;
    @TableFieId("name")
    private String realName
        
}  
```

## 增删改查

### 插入

```java
// 插入一条记录
int insert(T entity);
```

配置主键自增：实体类字段上 @TableId（type =IdType.AUTO）

### 更新

```java
// 根据 whereEntity 条件，更新记录
int update(@Param(Constants.ENTITY) T entity, @Param(Constants.WRAPPER) Wrapper<T> updateWrapper);
// 根据 ID 修改
int updateById(@Param(Constants.ENTITY) T entity);
```

### 查询

```java
// 根据 ID 查询
T selectById(Serializable id);
// 根据 entity 条件，查询一条记录
T selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 查询（根据ID 批量查询）
List<T> selectBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);
// 根据 entity 条件，查询全部记录
List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 查询（根据 columnMap 条件）
List<T> selectByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);
// 根据 Wrapper 条件，查询全部记录
List<Map<String, Object>> selectMaps(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询全部记录。注意： 只返回第一个字段的值
List<Object> selectObjs(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 根据 entity 条件，查询全部记录（并翻页）
IPage<T> selectPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询全部记录（并翻页）
IPage<Map<String, Object>> selectMapsPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询总记录数
Integer selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

### 删除

```java
// 根据 entity 条件，删除记录
int delete(@Param(Constants.WRAPPER) Wrapper<T> wrapper);
// 删除（根据ID 批量删除）
int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);
// 根据 ID 删除
int deleteById(Serializable id);
// 根据 columnMap 条件，删除记录
int deleteByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);
```

物理删除 ：从数据库中直接移出

逻辑删除：在数据库中没有被移出，而是通过一个变量来让他失效！deleted=0 ==>deleted =1(失效)

逻辑删除配置方法：

- application.yml 加入配置(如果你的默认值和mp默认的一样,该配置可无):

  ```yaml
  mybatis-plus:
    global-config:
      db-config:
        logic-delete-field: flag  #全局逻辑删除字段值 3.3.0开始支持，详情看下面。
        logic-delete-value: 1 # 逻辑已删除值(默认为 1)
        logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
  
  ```

- 注册Bean

```java
import com.baomidou.mybatisplus.core.injector.ISqlInjector;
import com.baomidou.mybatisplus.extension.injector.LogicSqlInjector;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyBatisPlusConfiguration {

    @Bean
    public ISqlInjector sqlInjector() {
        return new LogicSqlInjector();
    }
}
```

- 实体类字段上加上`@TableLogic`注解，效果: 使用mp自带方法删除和查找都会附带逻辑删除功能 (自己写的xml不会)

```java
@TableLogic
private Integer deleted;
```

```sql
example
删除时 update user set deleted=1 where id =1 and deleted=0
查找时 select * from user where deleted=0
```

- 全局逻辑删除： 3.3.0开始支持

  如果公司代码比较规范，比如统一了全局都是flag为逻辑删除字段。

  使用此配置则不需要在实体类上添加 @TableLogic。

  但如果实体类上有 @TableLogic 则以实体上的为准，忽略全局。 即先查找注解再查找全局，都没有则此表没有逻辑删除。

  ```yaml
  mybatis-plus:
    global-config:
      db-config:
        logic-delete-field: flag  #全局逻辑删除字段值
  ```

注：

- 逻辑删除是为了方便数据恢复和保护数据本身价值等等的一种方案，但实际就是删除。
- 如果你需要再查出来就不应使用逻辑删除，而是以一个状态去表示。

## 自定义sql

> 方式一：

在Mapper文件中定义一个方法

```java
@Select("select * from user ${ew.customSqlSegment}")
List<User> selectAll(@Param(Constants.WRAPPER)Wrapper<User> wrapper);
```

> 方式二：将sql写入xml中

在application中加入扫描mapper文件路径

```yaml
mybatis-plus:
 mapper-locations: com/mp/mapper/*
```

在Mapper.xml中写sql

```xml
<mapper namespace="com.mp.dao.UserMapper">
	<select id="selectAll" resultType="com.mp.entity.User">
    	select * from user ${ew.customSqlSegment}
    </select>
</mapper>
```

## 分页查询

配置拦截器组件

```java
//Spring boot方式
@EnableTransactionManagement
@Configuration
@MapperScan("com.baomidou.cloud.service.*.mapper*")
public class MybatisPlusConfig {

    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
}
```

测试

```java
//测试分页查询
@Test
pulic void testPage(){
    // 参数一：当前页
    // 参数二：页面大小
    // 使用了分页插件之后，所有的分页操作也变得简单了
    Page<User> page =new Page<>(2,5);
    userMapper.selectPage(page,null);
    
    page.getRecords().forEach(System.out::println);
    //获取总数
    page.getTotal();
}
```



## 通用service

1、创建一个service接口：

```java
public interface UserService extends IService<User>{
    
}
```

2、创建实现类

```java
public class UserServiceImpl extend ServiceImpl<UserMapper,User> implements UserService{
    
}
```

3、测试方法

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ServiceTest{
    
    @Autowired
    private UserService userService;
    
    //取一个值
    @Test
    public void getOne(){
        User one = userService.getOne(Wrapper.<User>lambdaQuery().gt(User::getAge,25),false);
        
    }
    
    //批量插入
    @Test 
    public void batch(){
        User user1= new User();
        user1.steName("shui");
        user1.setAge("28");
        
        User user2= new User();
        user1.setName("shui2");
        user1.setAge("29");
        List<User> userList =Arrays.asList(user1,user2);
        userService.saveBatch(userList);
    }
    
    @Test 
    public void chain(){
     //查询
     userService.lambdaQuery().ge(User::getAge,25).like(User::getName,"雨").list();
    }
        
}

```

## 性能更新插件

1、导入插件

```java
//Spring boot方式
@EnableTransactionManagement
@Configuration
@MapperScan("com.baomidou.cloud.service.*.mapper*")
public class MybatisPlusConfig {

    /**
     * SQL执行效率插件
     */
    @Bean
    @Profile({"dev","test"})// 设置 dev test 环境开启
    public PerformanceInterceptor performanceInterceptor() {
        PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
        performanceInterceptor.setMaxTime(100);// ms 设置sql执行的最大时间，如果超过就停止
        performanceInterceptor.setFormat(true);
        return new PerformanceInterceptor();
    }
}
```

- 记得在SpringBoot配置环境为dev或者test环境！

- 参数：maxTime SQL 执行最大时长，超过自动停止运行，有助于发现问题。
- 参数：format SQL SQL是否格式化，默认false。
- 该插件只用于开发环境，不建议生产环境使用。

## 条件构造器（Wrapper）

```java
@Test
void contextLoads(){
    // 查询name不为null的用户，并且邮箱不为null的永不，年龄大于等于20的用户
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    wrapper.isNotNull("name");
    wrapper.isNotNull("email");
    wrapper.ge("age",12);
    userMapper.selectList(wrapper).forEach(System.out::println);
}

@Test
void test2(){
    // 查询name为shuishui的用户
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    wrapper.eq("name","shuishui");
    User user=userMapper.selectList(wrapper)
    System.out.println（user）;
}

@Test
void test3(){
    // 查询年龄在20~30岁之间的用户
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    wrapper.between("age",20,30);
    Integer count =userMapper.selectCount(wrapper);//查询结果数
    System.out.println（count）;
}

//模糊查询
@Test
void test4(){
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    
    wrapper.notLike("name",“s”);//相当于NOT LIKE '%s%'
    wrapper.likeRight("email",“s”);//相当于LIKE 's%'
    List<Map<String,Object>>maps =userMapper.selectMaps(wrapper);//查询结果数
    maps.forEach(System.out::println);
}


@Test
void test5(){
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    //子查询
    wrapper.insql("id","select id from user where id<3");
    List<Object> objects =userMapper.selectobjs(wrapper);
    objects.forEach(System.out::println);
}
    
@Test
void test6(){
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    //通过id进行排序
    wrapper.orderByAsc("id");
    List<User> users =userMapper.selectList(wrapper);
    objects.forEach(System.out::println);
}

//姓王年龄大于等于25，按年龄降序，年龄相同按id升序排列
void test7(){
    QueryWrapper<User> wrapper =new QueryWrapper<>();
   wrapper.likeRoght("name","王").or().ge("age",25).ordeiByDesc("age").orderByAsc("id");
    List<User> users =userMapper.selectList(wrapper);
    objects.forEach(System.out::println);
}

//创建日期为2019年2月14日并且直属上级为姓王
void test8(){
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    wrapper.apply("date_fromat(create_time,'%Y-%m-%d')='2019-02-14'").inSql("manager_id","select id from user where name like '王%'");
    List<User> users =userMapper.selectList(wrapper);
    objects.forEach(System.out::println);
}

//姓王并且（年龄小于40或者邮箱不为空）
void test9(){
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    //lt小于，gt大于
    wrapper.likeRoght("name","王").and(wq->wa.lt("age",40).or().isNotNull("email"))
    List<User> users =userMapper.selectList(wrapper);
    objects.forEach(System.out::println);
}

//不列出所有字段
@Test
void test10(){
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    
    wrapper.select("id","name").like("name","雨").lt("age",40);
    //不显示时间和id
    //wrapper.select(User.class,info->!info.getColumn().equals("create_time")&&!info.getColumn().equals("manager_id")).like("name","雨").lt("age",40);
    List<User> users =userMapper.selectList(wrapper);
    objects.forEach(System.out::println);
}

```

## AR模式

通过实体类对象直接实现CRUD

```java
@Data
@EqualsAndHashCode(callSuper = false)
public class User extends Model<User> {

    private static final long serialVersionUID = 1L;

    @ApiModelProperty(value = "用户id")
    private String userId;
}
```

测试：

```java
// 测试插入
@Test
public void insert(){
    User user =new User();
    user.setName("水");
    user.setAge(29);
    ...
    user.insert();  //不用再调用mapper接口，直接实现
}

// 测试查询
@Test
public void select(){
    User user =new User();
    user.selectById(1);
}
@Test
public void select2(){
    User user =new User();
    user.setId(1);
    user.selectById();
}

//测试更新
@Test
public void update(){
    User user =new User();
    user.setId(1);
    user.setName("火")
    user.updateById();
}

//测试删除
@Test
public void delete(){
    User user =new User();
    user.setId(1);
    user.deleteById();
}
```

## 代码自动生成器

dao、pojo、conrtroller、service自动生成

```java
package com.kuang;
import com.baomidou.mybatisplus.annotation.DbType; 
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.IdType; 
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.po.TableFill;
import com.baomidou.mybatisplus.generator.config.rules.DateType; 
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

import java.util.ArrayList;
// 代码自动生成器 
public class KuangCode {
     public static void main(String[] args) {       
         // 需要构建一个 代码自动生成器 对象        
         AutoGenerator mpg = new AutoGenerator();   
         // 配置策略
         // 1、全局配置        
         GlobalConfig gc = new GlobalConfig();    
         String projectPath = System.getProperty("user.dir"); 
         gc.setOutputDir(projectPath+"/src/main/java");    
         gc.setAuthor("狂神说");     
         gc.setOpen(false);      
         gc.setFileOverride(false);// 是否覆盖      
         gc.setServiceName("%sService");// 去Service的I前缀      
         gc.setIdType(IdType.ID_WORKER);  
         gc.setDateType(DateType.ONLY_DATE);  
         gc.setSwagger2(true);    
         mpg.setGlobalConfig(gc);
         
          //2、设置数据源  
         DataSourceConfig dsc = new DataSourceConfig();  
         dsc.setUrl("jdbc:mysql://localhost:3306/kuang_community? useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8");       
         dsc.setDriverName("com.mysql.cj.jdbc.Driver");   
         dsc.setUsername("root");      
         dsc.setPassword("123456");  
         dsc.setDbType(DbType.MYSQL);  
         mpg.setDataSource(dsc);
 
          //3、包的配置     
         PackageConfig pc = new PackageConfig();  
         pc.setModuleName("blog");     
         pc.setParent("com.kuang");    
         pc.setEntity("entity");  
         pc.setMapper("mapper");     
         pc.setService("service");   
         pc.setController("controller");    
         mpg.setPackageInfo(pc);
 
          //4、策略配置      
         StrategyConfig strategy = new StrategyConfig();       
  strategy.setInclude("blog_tags","course","links","sys_settings","user_record"," user_say"); // 设置要映射的表名
         strategy.setNaming(NamingStrategy.underline_to_camel);   
         strategy.setColumnNaming(NamingStrategy.underline_to_camel);   
         strategy.setEntityLombokModel(true);// 自动lombok；
         strategy.setLogicDeleteFieldName("deleted");   // 自动填充配置      
         TableFill gmtCreate = new TableFill("gmt_create", FieldFill.INSERT); 
         TableFill gmtModified = new TableFill("gmt_modified", FieldFill.INSERT_UPDATE);    
         ArrayList<TableFill> tableFills = new ArrayList<>(); 
         tableFills.add(gmtCreate);    
         tableFills.add(gmtModified);     
         strategy.setTableFillList(tableFills);   
         // 乐观锁      
         strategy.setVersionFieldName("version");
         strategy.setRestControllerStyle(true); 
         strategy.setControllerMappingHyphenStyle(true);
         // localhost:8080/hello_id_2     
         mpg.setStrategy(strategy);
         mpg.execute(); 
         //执行   
     } 
}
```

