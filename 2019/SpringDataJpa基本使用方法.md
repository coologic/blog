---
title: "SpringDataJpa基本使用方法"
tags:
  - Java
categories:
  - Java
date: 2019-05-04 23:46:08
---
整理，非原创

## 例子

gradle

```
dependencies {
    runtime('mysql:mysql-connector-java')
    compile('org.springframework.boot:spring-boot-starter-web')
    testCompile('org.springframework.boot:spring-boot-starter-test')
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    compile group: 'org.flywaydb', name: 'flyway-core', version: '5.2.4'
    compile group: 'javax.persistence', name: 'javax.persistence-api', version: '2.2'
}
```

resources/application.yml

```
server:
  port: 8083
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/employee_db?characterEncoding=utf-8&useSSL=false
    username: root
    password: root
    driveClassName: com.mysql.jdbc.Driver
  jpa:
    show-sql: true
    hibernate:
      naming:
        implicitStrategy: org.hibernate.boot.model.naming.ImplicitNamingStrategyLegacyJpaImpl
        physicalStrategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
```

> 注意高版本的mysql要加上?characterEncoding=utf-8&useSSL=false，ssl根据情况确定true和false

EmployeeAPIApplication

```
package com.example.employee;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class EmployeeAPIApplication {
   public static void main(String[] args) {
      SpringApplication.run(EmployeeAPIApplication.class, args);
   }
}
```

employee

```
package com.example.employee.entity;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity   //这个要有
public class Employee {
    @Id
    private Integer id;
    private String name;
    private Integer age;
    private String gender;
    private Integer companyId;
    private Integer salary;

    public Employee() {  //这个默认构造要有，如果没有自定义构造可以都不写

    }

    public Employee(Integer id, String name, Integer age, String gender, Integer companyId, Integer salary) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.gender = gender;
        this.companyId = companyId;
        this.salary = salary;
    }

    //getter setter
}
```

EmployeeRepository

```
package com.example.employee.repository;

import com.example.employee.entity.Employee;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    Employee findByName(String name);

    @Query(value = "select e.* from employee e, (select d.salary from employee d where d.name = :name) c where e.name like CONCAT('%',:namelike,'%') and e.salary > c.salary limit 1",
            nativeQuery=true)
    Employee findOneNameContainAndSalaryGreaterThan(@Param("namelike") String nameLike, @Param("name") String nameOfCompare);
}
```

> 注意emplyee和对应的EmployeeRepository要在和EmployeeAPIApplication同级目录下，否则可能出错
>
> -EmployeeAPIApplication
>
> -model
>
> —-emplyee
>
> -Repository
>
> —-EmployeeRepository

## Repository

### 继承

https://blog.csdn.net/qq_22172133/article/details/81192040

- Repository： 最顶层的接口，是一个空接口，目的是为了统一所有的Repository的类型，且能让组件扫描时自动识别
- CrudRepository: Repository的子接口，提供CRUD 的功能。
- PagingAndSortingRepository:CrudRepository的子接口, 添加分页排序。
- JpaRepository: PagingAndSortingRepository的子接口,增加批量操作等。
- JpaSpecificationExecutor: 用来做复杂查询的接口。

可以继承上面几个，自带功能逐级增加

## CURD操作

### 直接操作

见https://blog.csdn.net/qq_22172133/article/details/81192040

### 解析方法名查询

通过方法名自动解析生成

```
全局修饰： Distinct， Top， First
关键词： IsNull， IsNotNull， Like， NotLike， Containing， In， NotIn，
IgnoreCase， Between， Equals， LessThan， GreaterThan， After， Before…
排序方向： Asc， Desc
连接词： And， Or
And — 等价于 SQL 中的 and 关键字，比如 findByUsernameAndPassword(String user, Striang pwd)；
Or — 等价于 SQL 中的 or 关键字，比如 findByUsernameOrAddress(String user, String addr)；
Between — 等价于 SQL 中的 between 关键字，比如 findBySalaryBetween(int max, int min)；
LessThan — 等价于 SQL 中的 “<”，比如 findBySalaryLessThan(int max)；
GreaterThan — 等价于 SQL 中的”>”，比如 findBySalaryGreaterThan(int min)；
IsNull — 等价于 SQL 中的 “is null”，比如 findByUsernameIsNull()；
IsNotNull — 等价于 SQL 中的 “is not null”，比如 findByUsernameIsNotNull()；
NotNull — 与 IsNotNull 等价；
Like — 等价于 SQL 中的 “like”，比如 findByUsernameLike(String user)；
NotLike — 等价于 SQL 中的 “not like”，比如 findByUsernameNotLike(String user)；
OrderBy — 等价于 SQL 中的 “order by”，比如 findByUsernameOrderBySalaryAsc(String user)；
Not — 等价于 SQL 中的 “！ =”，比如 findByUsernameNot(String user)；
In — 等价于 SQL 中的 “in”，比如 findByUsernameIn(Collection userList) ，方法的参数可以是 Collection 类型，也可以是数组或者不定长参数；
NotIn — 等价于 SQL 中的 “not in”，比如 findByUsernameNotIn(Collection userList) ，方法的参数可以是 Collection 类型，也可以是数组或者不定长参数；
```

### JPQL及SQL查询

通过增加@Query(value = sql, nativeQuery=true)实现对ql语句按照sql进行查询，为false按照jpql查询。不写第二参数则默认为jpql查询

如果语句有修改需要增加 @Modifying 否则无法正确修改内容

### LIKE查询

a like ‘%xxx%’在Query中无法正常解析，需要用 a like CONCAT(‘%’,?1,’%’)，可能是在字符串你替换时的问题，也可以用:xxx和param关键字

@Query(value = “select * from a where time like CONCAT(‘%’,:time,’%’) “, nativeQuery = true)
 Object findByCreateTimeLike(@Param(“time”) String time);

### JPA中的*

select x.* from nnn x;应写为select x from nnn x，因为jpql中都以对象进行操作将每个结果视为对象

### JPQL差异

JPQL只能在WHERE 和 HAVING 条件中进行子查询select o from Order as o where o.ower  in(select p from Person as p where p.age =26)，原生sql可在select中使用

JPQL不支持limit限定