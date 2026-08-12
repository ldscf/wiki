---
source_title: Spring Boot 微服务极简化开发
categories:
- Develop
- Java
last_modified: '2024-10-25T02:36:27Z'
---
使用 sprint Boot 框架，结合自定义类，实现 webservice 极简化开发。

### Setup

#### Project Setting
 ```
Modules
 Source Folders: src/main/java
 Resource Folders: src/main/resources
 Test Source Folders: src/test/java
 Test Resource Folders: src/test/resources
 Excluded Folders: target
```

#### pom
 ```
<!-- spring-boot -->
    org.springframework.boot
    spring-boot-starter-parent
    3.3.2
    
    <!-- spring-boot -->
    
        org.springframework.boot
        spring-boot-starter-web
    
    
        org.springframework.boot
        spring-boot-starter-thymeleaf
    
    <!--log4j-->
    
        log4j
        log4j
        1.2.17
    
    <!--slf4j-->
    <!-- Conflicts with spring boot 
    
        org.slf4j
        slf4j-log4j12
        ${slf4j.version}
    
    -->
    
        org.slf4j
        slf4j-api
        2.0.9
    
    <!--Json-->
    
        com.google.code.gson
        gson
        2.8.9
    
    
        com.alibaba
        fastjson
        1.2.28
    
    <!--h2-->
    
        com.h2database
        h2
        2.1.212
    
    
        
            
                org.springframework.boot
                spring-boot-maven-plugin
            
        
    
```

#### application.yaml

src/main/resources
 ```
spring.application.name:
  SprintBootDemo
spring.profiles.active:
  production
server:
  port: 8080
spring.thymeleaf:
  mode: HTML
  cache: false
  encoding: UTF-8
  enabled: true
  prefix: classpath:/templates/
  suffix: .html
```

#### index.html

src/main/resources/static   // 更改需重启
 ```
  This webservice is running...
```

#### com.udef
```
 BASE/CNFYAML/DB/SQLData
```

#### etc
```
 log4j.properties/sysdb.yaml/db.yaml
```

#### config
```
 sql1.yaml   // 更改不需重启
```

### Service

src/main/java/SprintBoot/SprintBootStart.java
 ```
package SprintBoot;
import com.udf.SQLData;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class SprintBootStart {
    public static SQLData[] sql = new SQLData[10];
    public SprintBootStart() {
        sql[0] = new SQLData("h2local", "db.yaml");
    }
    public static void main(String[] args) {
        SpringApplication.run(SprintBootStart.class, args);
    }
}
```

src/main/java/SprintBoot/sql1/sql1.java
 ```
package SprintBoot;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import java.util.LinkedHashMap;
import java.util.Map;
import com.udf.SQLData;
import static SprintBoot.SprintBootStart.sql;
@RestController
@RequestMapping("/sql")
public class sql1 {
    String appName;
    public sql1() {
        appName = "sql";
    }
    @RequestMapping(value = "/get1", method = RequestMethod.GET)
    public Map getTest1() {
        //BASE.UDEFLOG = "ALL";
        SQLData sql1 = sql[0];
        sql1.setSQLYaml("config/sql1.yaml");
        Map m1 = new LinkedHashMap<>();
        // get data
        m1.put("api", "SE_PROD");
        m1.put("where.status", "1");
        m1.put("where.ca", "in ('LRL', 'adam')");
        return sql1.SQL(m1);
    }
}
```

运行调试没问题的话，就可以打 jar 包了。

### package

#### set main class

src/main/resources/META-INF/MANIFEST.MF
```
 Manifest-Version: 1.0
 Main-Class: SprintBoot.SprintBootStart
```

#### maven build
```
 maven -> XXX —> Lifecycle -> package -> build
 *```
```

[INFO] Scanning for projects...

[INFO] 

[INFO] -----------------------< org.udef:testSpringJar >-----------------------

[INFO] Building testSpringJar 1.0.0

[INFO]   from pom.xml

[INFO] --------------------------------[ jar ]---------------------------------

[INFO] 

[INFO] --- resources:3.3.1:resources (default-resources) @ testSpringJar ---

[INFO] Copying 1 resource from src/main/resources to target/classes

[INFO] Copying 4 resources from src/main/resources to target/classes

[INFO] 

[INFO] --- compiler:3.13.0:compile (default-compile) @ testSpringJar ---

[INFO] Recompiling the module because of added or removed source files.

[INFO] Compiling 7 source files with javac [debug parameters release 17] to target/classes

[INFO] 

[INFO] --- resources:3.3.1:testResources (default-testResources) @ testSpringJar ---

[INFO] skip non existing resourceDirectory /java/testSpringJar/src/test/resources

[INFO] 

[INFO] --- compiler:3.13.0:testCompile (default-testCompile) @ testSpringJar ---

[INFO] No sources to compile

[INFO] 

[INFO] --- surefire:3.2.5:test (default-test) @ testSpringJar ---

[INFO] No tests to run.

[INFO] 

[INFO] --- jar:3.4.2:jar (default-jar) @ testSpringJar ---

[INFO] Building jar: /java/testSpringJar/target/testSpringJar-1.0.0.jar

[INFO] 

[INFO] --- spring-boot:3.3.2:repackage (repackage) @ testSpringJar ---

[INFO] Replacing main artifact /java/testSpringJar/target/testSpringJar-1.0.0.jar with repackaged archive, adding nested dependencies in BOOT-INF/.

[INFO] The original artifact has been renamed to /java/testSpringJar/target/testSpringJar-1.0.0.jar.original

[INFO] ------------------------------------------------------------------------

[INFO] BUILD SUCCESS

[INFO] ------------------------------------------------------------------------

[INFO] Total time:  8.200 s

[INFO] Finished at: 2024-10-25T10:30:28+08:00

[INFO] ------------------------------------------------------------------------

Process finished with exit code 0

```*

#### RUN
```
 # 需要同步目录：etc/config/db (一般来说，src 下的文件及目录会被打包)
 java -jar XXX.jar
```

### Error

#### Artifact
```
 Project Structure -> Artifact
 Build Artifact
 java -jar XXX.jar
 -> Error: No active profile set, falling back to 1 default profile: "default"
```
```
 Solution: maven package
```

#### 没有主清单属性
```
 1. pom 中未引入 spring-boot-maven-plugin
 2. MANIFEST.MF 中启动类设置错误(若只有一个 main，倒也无妨)
 3. MF 文件也可通过 Project Setting/Artifacts 生成
```

#### 找不到或无法加载主类
```
 // 明明有，但是却无法 run/debug
 reload all maven
```
