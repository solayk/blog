---
layout: default
title: Spring Boot + React
parent: Web
nav_order: 1
last_modified_date: 2021-02-06
---
# Spring Boot + React 웹 환경설정
{: .no_toc }



<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# Spring Boot 설치

이 글은 Windows 10 Pro 기준으로 작성했으며, 자바 버전은 Open JDK 1.8이다. 

자바 설치는 다루지 않는다. 다른 블로그를 참고해 Open JDK 1.8 설치 후 환경변수 설정까지 반드시 한다.

1) [https://spring.io/tools](https://spring.io/tools )에서 OS에 맞게 Spring Tools 4 for Eclipse TS4를 다운로드 한다.

2) 설치를 원하는 폴더에 옮긴 후 Jar 파일의 압축을 푼다. 반디집 등 프로그램으로 풀 수 있다.

3) 압축을 풀면 "contents"란 압축파일이 하나 나오는데, 여기에 풀기로 압축을 푼다.

4) SpringToolSuite4를 실행한다.

![2 STS 실행파일 위치](https://user-images.githubusercontent.com/73984112/107107783-ae9ebb80-6876-11eb-99fc-a07fb77ccb51.PNG)

![3 STS 실행](https://user-images.githubusercontent.com/73984112/107107796-cd04b700-6876-11eb-8508-7621a3c2e693.PNG)

5) 프로젝트 폴더를 만들 workspace 경로를 설정한다.

![4](https://user-images.githubusercontent.com/73984112/107107888-54eac100-6877-11eb-8f70-8ea2a6a6a06b.PNG)

6) 설정에서 text encoding 옵션을 utf-8로 변경한다.

![5](https://user-images.githubusercontent.com/73984112/107108009-27eade00-6878-11eb-89fa-82d56ca99366.PNG)

"enc"로 검색해 나오는 Workspace, CSS Files, HTML Files, JSP Files, XML Files 모두 utf-8로 변경한다.

![6](https://user-images.githubusercontent.com/73984112/107108040-8dd76580-6878-11eb-939c-2b2b22b48ff6.PNG)
![7](https://user-images.githubusercontent.com/73984112/107108042-8f089280-6878-11eb-9d9a-78d159767fb9.PNG)

7) 프로젝트를 생성한다.

![8](https://user-images.githubusercontent.com/73984112/107108165-6f259e80-6879-11eb-9d53-f653a8a2a168.PNG)

8) 프로젝트 설정은 Maven 기준 Java Version 8으로 진행한다. 이름은 자유롭게 정한다.

![9](https://user-images.githubusercontent.com/73984112/107108172-79e03380-6879-11eb-8b43-bbb2e2ec0243.PNG)

9) 이 글에선 Spring Boot Version을 2.4.2로 진행한다.

버전별 차이는 [https://github.com/spring-projects/spring-boot/wiki](https://github.com/spring-projects/spring-boot/wiki)에서 볼 수 있다. 2.4.x 버전으로 진행 시 특이사항은 

![10](https://user-images.githubusercontent.com/73984112/107108224-a8f6a500-6879-11eb-977b-a81d2e3767b9.PNG)

10) Dependency를 추가한다. 

아래 사진의 5가지를 설치한다. 추가로 필요한 기능은 나중에 pom.xml에 추가할 수 있다.

- Spring Boot DevTools: STS 작업 후 저장 시 자동으로 서버 재부팅
- Lombok: 자바 클래스 생성 시 Getter, Setter, 기타 함수를 Annotation@ 하나로 자동 생성
- MyBatis Framework: DB 제어, 관리
- MySQL Driver, MariaDB Driver: DB 드라이버 (MariaDB 사용 예정)
- Spring Web: 웹

![10](https://user-images.githubusercontent.com/73984112/107109660-9aae8600-6885-11eb-809e-6360c4b0a09c.PNG)

11) 오른쪽 하단 녹색 바가 멈출때까지 기다린다.

![12](https://user-images.githubusercontent.com/73984112/107108693-312a7980-687d-11eb-99a1-8a1d51f69be9.PNG)

12) 설치가 완료되면 프로젝트 폴더 이름 옆에 [boot] [devtools]가 표시된다. pom.xml 파일을 더블클릭해 설치한 dependency가 모두 존재하는지 확인한다.

![13](https://user-images.githubusercontent.com/73984112/107109819-a189c880-6886-11eb-8edd-f3ca7a7ac055.PNG)

13) Java Compiler 버전이 1.8인지 확인한다. 추가로 pom.xml에도 1.8로 되어 있는지 확인한다. 이 작업은 구글에 검색하면 잘 나와 있어 자세한 설명은 생략한다.



# React 설치

아래 설명은 Windows 10 Pro, VS Code (Visual Studio Code) 기준으로 작성했다.

1) Node.js 설치: [https://nodejs.org/ko/](https://nodejs.org/ko/)에서 최신 버전을 받아 설치한다.

2) React 프로젝트를 저장할 폴더를 만들고, VS Code 실행 후 아래 사진과 같이 폴더 경로를 열어준다.

![1](https://user-images.githubusercontent.com/73984112/107110079-58d30f00-6888-11eb-8f8a-c5ab04d13614.PNG)

3)  yarn 설치

VS Code에서 Ctrl + ` 를 누르면 하단에 터미널이 보인다. 

![2](https://user-images.githubusercontent.com/73984112/107110285-0a267480-688a-11eb-9264-560824174b0c.PNG)

여기에 아래 명령어 기입 후 엔터로 node.js 설치가 잘 되었는지 확인한다.

```bash
npm --version
```

버전이 나오면, 아래 명령어로 yarn을 설치한다.

```bash
npm install --global yarn
```

설치가 완료되면 yarn 버전도 확인한다.

```bash
yarn --version
```

4) React 프로젝트 설치

먼저 create-react-app 라이브러리를 설치한다. 리액트 세팅이 된 boilerplate를 만들기 쉽게 돕는 라이브러리다.

```bash
yarn global add create-react-app
create-react-app -V
```

그리고 리액트 프로젝트를 생성한다. 점(.)은 이 경로에 설치한다는 의미이다.

```bash
create-react-app .
```

완료되면 아래 사진처럼 리액트 프로젝트 파일이 나타난다.

![3](https://user-images.githubusercontent.com/73984112/107110553-127faf00-688c-11eb-81f7-d1a2844b1a83.PNG)

리액트 웹 페이지를 실행해보자.

```bash
yarn start
```

아래 화면이 나오면 성공이다.

![4](https://user-images.githubusercontent.com/73984112/107110593-6e4a3800-688c-11eb-8590-2cb07e575584.PNG)





# Spring Boot 다중 데이터베이스 설정

 Spring Boot에 Maria DB의 Database를 2개 이상 연결하는 법을 설명한다.



## 1. 작업 환경

- Spring Boot 2.4.2
- Maven
- MyBatis
- MariaDB



## 2. Pom.xml

아래와 같이 dependency를 추가한다.

```xml
<!-- DB -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <scope>provided</scope>
</dependency>	
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-configuration-processor</artifactId>
  <optional>true</optional>
</dependency>
<!-- DB -->
```



## 3. config

본인의 Java 파일 경로에 .config 이름의 package를 추가하고 연결하길 원하는 Database 개수 만큼 파일을 만든다. 대표 database에 해당하는 config 파일에만 @Primary 어노테이션을 추가한다.

### DBConfig_1.java (primary)

```java
package com.ant.config;

import javax.sql.DataSource;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@Configuration
@MapperScan(basePackages="com.ant.mapper.springboot",sqlSessionFactoryRef="db1SqlSessionFactory") // 다중 Database 사용 시 mapper 클래스 파일 scan 목적 basePackages를 DB별로 따로 설정
@EnableTransactionManagement
public class DBConfig_1 {
	
	@Bean(name="db1DataSource") // db1
	@Primary
	@ConfigurationProperties(prefix="spring.db1.datasource") // appliction.properties와 맞춰야 함
	public DataSource db1DataSource() { // db1
		return DataSourceBuilder.create().build();
	}
	
	@Bean(name="db1SqlSessionFactory") // db1
	@Primary
	public SqlSessionFactory sqlSessionFactory(@Qualifier("db1DataSource") DataSource db1DataSource, ApplicationContext applicationContext) throws Exception{
		final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
		sessionFactory.setDataSource(db1DataSource); // db1
		sessionFactory.setMapperLocations(applicationContext.getResources("classpath:mybatis-mapper-springboot/*.xml")); // 쿼리 작성용 mapper.xml 위치 설정
		return sessionFactory.getObject();
	}
	
	@Bean(name="db1SqlSessionTemplate")
	@Primary
	public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory db1sqlSessionFactory) throws Exception{ // db1
		return new SqlSessionTemplate(db1sqlSessionFactory); // db1
	}
	
    @Bean(name = "db1transactionManager") // db1
	@Primary
    public PlatformTransactionManager transactionManager(@Qualifier("db1DataSource") DataSource db1DataSource) { // db1
        return new DataSourceTransactionManager(db1DataSource); // db1
    }
}
```



### DBConfig_2.java

```java
package com.ant.config;

import javax.sql.DataSource;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@Configuration
@MapperScan(basePackages="com.ant.mapper.indicators",sqlSessionFactoryRef="db2SqlSessionFactory") // 다중 Database 사용 시 mapper 클래스 파일 scan 목적 basePackages를 DB별로 따로 설정
@EnableTransactionManagement
public class DBConfig_2 {
	
	@Bean(name="db2DataSource") // db2
	@ConfigurationProperties(prefix="spring.db2.datasource") // appliction.properties와 맞춰야 함
	public DataSource db2DataSource() { // db2
		return DataSourceBuilder.create().build();
	}
	
	@Bean(name="db2SqlSessionFactory") // db2
	public SqlSessionFactory sqlSessionFactory(@Qualifier("db2DataSource") DataSource db2DataSource, ApplicationContext applicationContext) throws Exception{ // db2
		final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
		sessionFactory.setDataSource(db2DataSource); // db2
		sessionFactory.setMapperLocations(applicationContext.getResources("classpath:mybatis-mapper-indicators/*.xml")); // 쿼리 작성용 mapper.xml 위치 설정
		return sessionFactory.getObject();
	}
	
	@Bean(name="db2SqlSessionTemplate") // db2
	public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory db2sqlSessionFactory) throws Exception{ // db2
		return new SqlSessionTemplate(db2sqlSessionFactory); // db2
	}
	
    @Bean(name = "db2transactionManager") // db2
    public PlatformTransactionManager transactionManager(@Qualifier("db2DataSource") DataSource db2DataSource) { // db2
        return new DataSourceTransactionManager(db2DataSource); // db2
    }
}
```



## 4. Mapper

### UserMapper.java (interface)

mapper interface 파일은 database 별로 package를 분리한다. 이 글에선 Mariadb의 데이터베이스를 springboot와 indicators 2개를 쓴다.

```java
package com.ant.mapper.springboot;

import java.util.List;
import org.apache.ibatis.annotations.Mapper;

import com.ant.vo.UserVO;
 
@Mapper
public interface UserMapper {
    public int updateUser(UserVO user);
}
```

### UserMapper.xml

mapper xml 파일도 database 별로 package를 분리한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTDMapper3.0//EN" 
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="com.ant.mapper.springboot.UserMapper">
     <update id="updateUser" parameterType="com.ant.vo.UserVO">
         UPDATE user
         SET nickname = #{nickname}, email = #{email} 
         WHERE userid = #{userid}
     </update>
  </mapper>
```

다른 database를 위한 Mapper 파일도 만든다.



## 5. application.properties 설정

```properties
spring.db1.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.db1.datasource.jdbc-url=jdbc:mariadb://아이피주소:포트번호/springboot?characterEncoding=UTF-8&serverTimezone=UTC
spring.db1.datasource.username=아이디
spring.db1.datasource.password=비밀번호

spring.db2.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.db2.datasource.jdbc-url=jdbc:mariadb://아이피주소:포트번호/indicators?characterEncoding=UTF-8&serverTimezone=UTC
spring.db2.datasource.username=아이디
spring.db2.datasource.password=비밀번호
```



Controller, Service 등 다른 파일은 하나의 파일을 공유할 수 있다. 단, Mapper 파일을 잘 구분해 쓰고 각각 @Autowired 어노테이션을 추가해야한다.