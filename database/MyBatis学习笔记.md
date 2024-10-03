# MyBatis学习笔记

## 搭建MyBatis

- 引入依赖

- 创建核心配置文件**mybatis-config.xml**:，存放在src/main/resources下

```xml
<?xml version="1.0" encoding="UTF-8" ?> 
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd"> <configuration> 
    <!--设置连接数据库的环境--> 
    <environments default="development"> 
    	<environment id="development"> 
        	<transactionManager type="JDBC"/> 
        	<dataSource type="POOLED"> 
                <property name="driver" value="com.mysql.jdbc.Driver"/> 
                <property name="url" value="jdbc:mysql://localhost:3306/MyBatis"/> 
                <property name="username" value="root"/> 
                <property name="password" value="123456"/> </dataSource> </environment> </environments> 
    <!--引入映射文件-->
    <mappers> 
        <mapper resource="mappers/UserMapper.xml"/> 
    </mappers>
</configuration>
```

- 创建mapper接口（不需要创建实现类）

  ```java
  public interface UserMapper { 
  /*** 添加用户信息 */
  int insertUser(); 
  }
  ```

  

- 创建映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?> 
<!DOCTYPE mapper 
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
"http://mybatis.org/dtd/mybatis-3-mapper.dtd"> 
<mapper namespace="com.atguigu.mybatis.mapper.UserMapper"> 
<!--int insertUser();--> 
<insert id="insertUser"> 
insert into t_user values(null,'张三','123',23,'女') 
</insert> 
</mapper>
```

## 查询

### 查询一个实体类对象

```xml
<!--User getUserById();--> 
<select id="getUserById" resultType="com.atguigu.mybatis.bean.User"> 
    select * from t_user where id = 2 
</select>
```

### 查询集合

```xml
<!--List<User> getUserList();--> 
<select id="getUserList" resultType="com.atguigu.mybatis.bean.User"> 
    select * from t_user
</select>
```

注：1、查询的标签select必须设置属性resultType或resultMap，用于设置实体类和数据库表的映射

关系

resultType：自动映射，用于属性名和表中字段名一致的情况

resultMap：自定义映射，用于一对多或多对一或字段名和属性名不一致的情况

2、当查询的数据为多条时，不能使用实体类作为返回值，只能使用集合，否则会抛出异常

TooManyResultsException；但是若查询的数据只有一条，可以使用实体类或集合作为返回值

## 获取参数值

MyBatis获取参数值的两种方式：**${}**和**#{}**

${}的本质就是字符串拼接，#{}的本质就是占位符赋值

一般用#{}

### 单个字面量类型的参数

若mapper接口中的方法参数为单个的字面量类型

此时可以使用${}和#{}以任意的名称获取参数的值，注意${}需要手动加单引号

### 多个字面量类型的参数

若mapper接口中的方法参数为多个时

此时MyBatis会自动将这些参数放在一个map集合中，以arg0,arg1...为键，以参数为值；以

param1,param2...为键，以参数为值；因此只需要通过${}和#{}访问map集合的键就可以获取相对应的

值，注意${}需要手动加单引号

### map集合类型的参数

若mapper接口中的方法需要的参数为多个时，此时可以手动创建map集合，将这些数据放在map中

只需要通过${}和#{}访问map集合的键就可以获取相对应的值，注意${}需要手动加单引号

### 实体类类型的参数

若mapper接口中的方法参数为实体类对象时

此时可以使用${}和#{}，通过访问实体类对象中的属性名获取属性值，注意${}需要手动加单引号

### 使用@Param标识

可以通过@Param注解标识mapper接口中的方法参数

此时，会将这些参数放在map集合中，以@Param注解的value属性值为键，以参数为值；以

param1,param2...为键，以参数为值；只需要通过${}和#{}访问map集合的键就可以获取相对应的值，

注意${}需要手动加单引号



## 添加功能获取自增的主键

```java
/*** 添加用户信息 
* @param user 
* @return 
* useGeneratedKeys：设置使用自增的主键 
* keyProperty：因为增删改有统一的返回值是受影响的行数，因此只能将获取的自增的主键放在传输的参数user对象的某个属性中 */ 
int insertUser(User user);
```

```xml
<!--int insertUser(User user);--> 
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id"> 
    insert into t_user values(null,#{username},#{password},#{age},#{sex}) 
</insert>
```

## resultMap

### resultMap处理字段和属性的映射关系

若字段名和实体类中的属性名不一致，则可以通过resultMap设置自定义映射

```xml
<!--resultMap：设置自定义映射 
属性： 
id：表示自定义映射的唯一标识 
type：查询的数据要映射的实体类的类型 
子标签： 
id：设置主键的映射关系 
result：设置普通字段的映射关系 
association：设置多对一的映射关系 
collection：设置一对多的映射关系 
属性： 
property：设置映射关系中实体类中的属性名 
column：设置映射关系中表中的字段名 --> 
<resultMap id="userMap" type="User"> 
    <id property="id" column="id"></id> 
    <result property="userName" column="user_name"></result> 
    <result property="password" column="password"></result> 
    <result property="age" column="age"></result> 
    <result property="sex" column="sex"></result> </resultMap> 
<!--List<User> testMohu(@Param("mohu") String mohu);--> 
<select id="testMohu" resultMap="userMap">
    <!--select * from t_user where username like '%${mohu}%'--> 
    select id,user_name,password,age,sex from t_user where user_name like concat('%',#{mohu},'%') </select>
```

注：若字段名和实体类中的属性名不一致，但是字段名符合数据库的规则（使用_），实体类中的属性名符合Java的规则（使用驼峰）

此时也可通过以下两种方式处理字段名和实体类中的属性的映射关系：

- a>可以通过为字段起别名的方式，保证和实体类中的属性名保持一致

- b>可以在MyBatis的核心配置文件中设置一个全局配置信息mapUnderscoreToCamelCase，可以在查询表中数据时，自动将_类型的字段名转换为驼峰例如：字段名user_name，设置了mapUnderscoreToCamelCase，此时字段名就会转换为userName

### 多对一映射处理

- 级联方式处理映射关系

```xml
<resultMap id="empDeptMap" type="Emp"> 
    <id column="eid" property="eid"></id> 
    <result column="ename" property="ename"></result> 
    <result column="age" property="age"></result> 
    <result column="sex" property="sex"></result> 
    <result column="did" property="dept.did"></result> 
    <result column="dname" property="dept.dname"></result> </resultMap> 
<!--Emp getEmpAndDeptByEid(@Param("eid") int eid);--> 
<select id="getEmpAndDeptByEid" resultMap="empDeptMap"> 
    select emp.*,dept.* from t_emp emp left join t_dept dept on emp.did = dept.did where emp.eid = #{eid} </select>
```

- 使用association处理映射关系

```xml
<resultMap id="empDeptMap" type="Emp"> 
    <id column="eid" property="eid"></id> 
    <result column="ename" property="ename"></result>
    <result column="age" property="age"></result> 
    <result column="sex" property="sex"></result> 
    <association property="dept" javaType="Dept">
        <id column="did" property="did"></id> 
        <result column="dname" property="dname"></result> </association> </resultMap>
<!--Emp getEmpAndDeptByEid(@Param("eid") int eid);--> 
<select id="getEmpAndDeptByEid" resultMap="empDeptMap"> 
    select emp.*,dept.* from t_emp emp left join t_dept dept on emp.did = dept.did where emp.eid = #{eid} </select>
```

- 分步查询

### 一对多映射处理

- collection

```xml
/*** 根据部门id查新部门以及部门中的员工信息 
* @param did 
* @return 
*/ 
Dept getDeptEmpByDid(@Param("did") int did);
<resultMap id="deptEmpMap" type="Dept"> 
    <id property="did" column="did"></id> 
    <result property="dname" column="dname"></result> 
    <!--ofType：设置collection标签所处理的集合属性中存储数据的类型 --> 
    <collection property="emps" ofType="Emp"> 
        <id property="eid" column="eid"></id> 
        <result property="ename" column="ename"></result> 
        <result property="age" column="age"></result> 
        <result property="sex" column="sex"></result> </collection> </resultMap> 
<!--Dept getDeptEmpByDid(@Param("did") int did);--> 
<select id="getDeptEmpByDid" resultMap="deptEmpMap"> 
    select dept.*,emp.* from t_dept dept left join t_emp emp on dept.did = emp.did where dept.did = #{did} </select>
```

- 分步查询

## 动态sql

Mybatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能，它存在的意义是为了解决拼接SQL语句字符串时的痛点问题。

### if

if标签可通过test属性的表达式进行判断，若表达式的结果为true，则标签中的内容会执行；反之标签中的内容不会执行

```xml
<!--List<Emp> getEmpListByMoreTJ(Emp emp);--> 
<select id="getEmpListByMoreTJ" resultType="Emp"> 
select * from t_emp where 1=1 
<if test="ename != '' and ename != null"> 
and ename = #{ename} 
</if> 
<if test="age != '' and age != null"> 
and age = #{age} 
</if> 
<if test="sex != '' and sex != null">
and sex = #{sex} 
</if> 
</select>
```

### where

```xml
<select id="getEmpListByMoreTJ2" resultType="Emp"> 
    select * from t_emp 
    <where> 
        <if test="ename != '' and ename != null"> 
            ename = #{ename} 
        </if> 
        <if test="age != '' and age != null"> 
            and age = #{age} 
        </if> 
        <if test="sex != '' and sex != null"> 
            and sex = #{sex} 
        </if> 
    </where> 
</select>
```

注：where和if一般结合使用：

- a>若where标签中的if条件都不满足，则where标签没有任何功能，即不会添加where关键字

- b>若where标签中的if条件满足，则where标签会自动添加where关键字，并将条件最前方多余的and去掉

注意：where标签不能去掉条件最后多余的and

### trim

```xml
<select id="getEmpListByMoreTJ" resultType="Emp"> 
select * from t_emp 
    <trim prefix="where" suffixOverrides="and">
        <if test="ename != '' and ename != null">
            ename = #{ename} and 
        </if> 
        <if test="age != '' and age != null"> 
            age = #{age} and 
        </if> 
        <if test="sex != '' and sex != null">
            sex = #{sex} 
        </if> 
    </trim> 
</select>
```

trim用于去掉或添加标签中的内容

常用属性：

prefix：在trim标签中的内容的前面添加某些内容

prefixOverrides：在trim标签中的内容的前面去掉某些内容

suffix：在trim标签中的内容的后面添加某些内容

suffixOverrides：在trim标签中的内容的后面去掉某些内容

### choose、when、otherwise

choose、when、otherwise相当于if...else if..else

```xml
<!--List<Emp> getEmpListByChoose(Emp emp);--> 
<select id="getEmpListByChoose" resultType="Emp"> 
    select <include refid="empColumns"></include> from t_emp 
    <where> 
        <choose> 
            <when test="ename != '' and ename != null"> 
                ename = #{ename} 
            </when> 
            <when test="age != '' and age != null"> 
                age = #{age} 
            </when> 
            <when test="sex != '' and sex != null"> 
                sex = #{sex} 
            </when> 
            <when test="email != '' and email != null">
                email = #{email} 
            </when> 
        </choose> 
    </where> 
</select>
```

### foreach

```xml
<!--int insertMoreEmp(List<Emp> emps);--> 
<insert id="insertMoreEmp"> 
    insert into t_emp values 
    <foreach collection="emps" item="emp" separator=","> 
        (null,#{emp.ename},#{emp.age},#{emp.sex},#{emp.email},null) 
    </foreach> 
</insert> 
<!--int deleteMoreByArray(int[] eids);--> 
<delete id="deleteMoreByArray"> 
    delete from t_emp where 
    <foreach collection="eids" item="eid" separator="or"> 
        eid = #{eid} 
    </foreach>
    </delete> 
<!--int deleteMoreByArray(int[] eids);-->
<delete id="deleteMoreByArray"> 
    delete from t_emp where eid in 
    <foreach collection="eids" item="eid" separator="," open="(" close=")">
        #{eid} 
    </foreach> 
</delete>
```

属性：

collection：设置要循环的数组或集合

item：表示集合或数组中的每一个数据

separator：设置循环体之间的分隔符

open：设置foreach标签中的内容的开始符

close：设置foreach标签中的内容的结束符

### sql片段

sql片段，可以记录一段公共sql片段，在使用的地方通过include标签进行引入

```xml
<sql id="empColumns"> 
    eid,ename,age,sex,did 
</sql> 
select <include refid="empColumns"></include> from t_emp
```

