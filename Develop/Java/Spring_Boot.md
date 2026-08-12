---
source_title: Spring Boot
categories:
- Develop
- Java
last_modified: '2025-07-29T07:57:52Z'
---
Spring Boot helps you to create Spring-powered, production-grade applications and services with absolute minimum fuss. It takes an opinionated view of the Spring platform so that new and existing users can quickly get to the bits they need.

Spring Boot 是一个基于 Spring 的框架，旨在简化 Spring 应用的配置和开发过程，通过自动配置和约定大于配置的原则，使开发者能够快速搭建独立、生产级别的应用程序。
- Environment: IntelliJ IDEA 2023.1 (Ultimate Edition)
- Features: web, mysql
- Course:
1. New project -> Spring Initializr -> type: maven: testSprintBoot
1. Dependency -> Web: Spring Web，SQL: MyBatis Framework, MySQL Driver/H2 Database
1. src/main/resources: application.properties -> application.yml
1. src/main/resources/static: index.html
1. com.example.testspringboot: bean, controller, mapper, service

### pom.xml
 ```
<!-- spring-boot -->
    org.springframework.boot
    spring-boot-starter-parent
    3.3.2
    
    <!-- spring-boot -->
    
        org.springframework.boot
        spring-boot-starter-web
    
    <!-- mybatis -->
    
        org.mybatis.spring.boot
        mybatis-spring-boot-starter
        3.0.3
    
    <!-- MySQL -->
    
        com.mysql
        mysql-connector-j
        8.0.33
    
```

### application.yml
 ```
spring.application.name:
  testSprintBoot
server:
  port: 8083
spring:
  datasource:
    url: jdbc:mysql://192.168.0.83:3306/mysql?serverTimezone=Asia/Shanghai&characterEncoding=utf-8
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
mybatis:
  type-aliases-package: com.example.testspringboot.bean
```

### index.html
 ```
<!DOCTYPE html>
    
    Title
This is a test web page.
```

### TestSprintBootApplication
 ```

# main.java/com.example.testspringboot.TestspringbootApplication
package com.example.testspringboot;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class TestspringbootApplication {
    public static void main(String[] args) {
        SpringApplication.run(TestspringbootApplication.class, args);
    }
}
```

### bean

#### SysUser
 ```
package com.example.testsprintboot.bean;
import org.springframework.format.annotation.DateTimeFormat;
import java.time.LocalDateTime;
import java.util.Date;
public class SysUser {
    private String host;
    private String user;
    private String passwordLastChanged;
    public String getHost() {
        return host;
    }
    public void setHost(String host) {
        this.host = host;
    }
    public String getUser() {
        return user;
    }
    public void setUser(String user) {
        this.user = user;
    }
    public String getPasswordLastChanged() {
        return passwordLastChanged;
    }
    public void setPasswordLastChanged(String passwordLastChanged) {
        this.passwordLastChanged = passwordLastChanged;
    }
}
```

### mapper

#### SysUserMapper
 ```
package com.example.testsprintboot.mapper;
import com.example.testsprintboot.bean.SysUser;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import java.util.List;
@Mapper
public interface SysUserMapper {
    @Select({
            "select",
            "host, user, date_format(password_last_changed, '%Y-%m-%d %H:%i:%s') password_last_changed",
            "from user"
    })
    List selectAll();
}
```

### service

#### SysUserService
 ```
package com.example.testsprintboot.service;
import com.example.testsprintboot.bean.SysUser;
import java.util.List;
public interface SysUserService {
    public List selectAll();
}

# service: SysUserServiceImpl
package com.example.testsprintboot.service;
import com.example.testsprintboot.bean.SysUser;
import com.example.testsprintboot.mapper.SysUserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;
@Service("SysUserService")
public class SysUserServiceImpl implements SysUserService {
    @Autowired
    private SysUserMapper sysUserMapper;
    @Override
    public List selectAll() {
        return sysUserMapper.selectAll();
    }
}
```

### controller

#### SysUserController
 ```
package com.example.testsprintboot.controller;
import com.example.testsprintboot.bean.SysUser;
import com.example.testsprintboot.service.SysUserService;
import com.example.testsprintboot.service.SysUserServiceImpl;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import jakarta.annotation.Resource;
import java.util.List;
@RestController
@RequestMapping("/sysuser")
public class SysUserController {
    @Resource
    private SysUserService sysUserService = new SysUserServiceImpl();
    @RequestMapping(value = "/selectAll", method = RequestMethod.GET)
    public List selectAll() {
        List list = sysUserService.selectAll();
        return list;
    }
}
```

### Test
 ```
http://127.0.0.1:8083/sysuser/selectAll
[{"host":"%","user":"root","passwordLastChanged":null},{"host":"localhost","user":"mysql.infoschema","passwordLastChanged":null},{"host":"localhost","user":"mysql.session","passwordLastChanged":null},{"host":"localhost","user":"mysql.sys","passwordLastChanged":null},{"host":"localhost","user":"root","passwordLastChanged":null}]
```

## See also
1. [IDEA搭建一个SpringBoot项目](https://www.cnblogs.com/detailNew/p/14967528.html)
1. [SpringBoot + MyBatis(注解版)，常用的SQL方法](https://www.cnblogs.com/caizhaokai/p/10982727.html)
1. [在 Spring 应用中处理 CORS 跨域](https://springdoc.cn/spring-and-cors/)
