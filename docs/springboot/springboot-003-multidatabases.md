---
layout: default
title: Spring Boot Multi-Databases
parent: WEB, Spring Boot X React
nav_order: 3
last_modified_date: 2021-03-04
---
# Spring Boot 다중 데이터베이스
{: .no_toc }

본 글에선 Spring Boot에 Maria DB의 Database를 2개 이상 연결하는 법을 설명합니다.

- Ctrl + F 로 단어를 검색하면 빠르게 찾을 수 있습니다. 
- 아래 목차는 왼편 삼각형을 누르면 숨겨집니다.

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# Spring Boot 스프링부트

## 1. 환경

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