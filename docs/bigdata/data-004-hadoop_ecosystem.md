---
layout: default
title: 하둡 에코시스템
parent: 빅데이터
nav_order: 3
last_modified_date: 2021-01-28
---
# 하둡 에코시스템 Hadoop Ecosystem
{: .no_toc }
빅데이터는 처리 단계에 따라 수집, 정제, 적재, 분석, 시각화 등의 단계로 나뉜다. 하둡이 오픈소스인 덕분에 각 단계별 기술이 발달할 수 있었고, 이 기술들을 하둡 에코시스템이라 통칭한다.

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


## 1. 하이브 HIVE

하둡의 자바 기반 맵리듀스 프로그래밍이 너무 어려워 Pig라는 기술이 나왔지만, Pig 역시 Latin 언어를 쓰는 스크립트 언어였다. 이에 기존 DB 개발자에게 익숙한 SQL 언어를 사용하는 Hive가 나왔다.

HIVE는 정형 데이터를 처리하며 테이블 구조는 RDBMS에서 가져오고, 데이터는 HDFS에 저장한다. (테이블 구조를 RDBMS에서 가져오기 때문에 정형 데이터만 처리한다)

두가지 버전이 있다. 첫번째 Hive는 하이브 메타 스토어 및 드라이버에 직접 접근하는 방식이고, 두번째 버전인 Beeline은 hiveserver2 API를 사용해 더욱 안전하지만 현재까진 첫 버전이 주로 쓰인다.

### 1.1 MariaDB 설치

meta data를 저장하기 위해 mariadb 설치한다.

namenode는 masternode이기 때문에 처리량이 많아 하이브는 datanode에 설치한다.

server: dn01

```bash
$ yum install mariadb-server mariadb
```

설치 확인 명령어 

```bash
$ rpm -qa | grep maria
```

 MariaDB 활성화

```bash
$ systemctl enable mariadb.service
$ systemctl start mariadb.service
$ mysql_secure_installation
```

password를 물어보면 enter (mariadb root의 비밀번호를 설정하지 않은 상태)

password는 간단히 1234로 설정한다.



MariaDB 설정

```bash
$ vi /etc/my.cnf
```

bind-address=192.168.56.102



서비스 재시작

```bash
$ systemctl restart mariadb.service
```



MariaDB 로그인

```bash
$ mysql -u root -p
```



hive 계정이 mariadb의 메타스토어에 접근하기 위해 권한 부여한다. 

% 는 모든 ip 주소를 의미하고, flush privileges 는 권한 설정 반영 명령어다.

```sql
grant all privileges on *.* to hive@"%" identified by "hive" with grant option;
flush privileges;
```



```sql
grant all privileges on *.* to hive@"dn01" identified by "hive" with grant option;
flush privileges;
```



mysql 데이터베이스로 변경

```sql
use mysql;
```

mysql 데이터베이스에서 user 테이블 조회 시 hive 계정을 확인할 수 있다.

```sql
select user, host from user;
```



[참고]

테이블 복사 시 구조만 복사되고, 데이터는 복사되지 않는다.

```mysql
create table dept like departments;
```



자주 쓰이는 MySQL 쿼리는 아래를 참조한다.

별도 md 파일 만들기

https://dev.mysql.com/doc/



### 1.2 Hive 설치

Hive 설정파일 복사

```bash
$ cp /opt/hive/current/conf/hive-env.sh.template /opt/hive/current/conf/hive-env.sh
$ cp /opt/hive/current/conf/hive-default.xml.template /opt/hive/current/conf/hive-site.xml
```

Hive 설정파일을 vi에디터로 열어서

```bash
$ vi /opt/hive/current/conf/hive-env.sh
```

HADOOP_HOME=/opt/hadoop/current

위 문구를 추가한다.

아래 xml 설정파일도 vi 에디터로 열어 수정한다.

*주의: 내용이 많아 잘 찾아 수정해야한다.

```bash
$ vi /opt/hive/current/conf/hive-site.xml
```

```xml
<property>
  <name>javax.jdo.option.ConnectionURL</name>
  <value>jdbc:mysql://192.168.56.102:3306/hive?createDatabaseIfNotExist=true</value>
  <description>JDBC connect string for a JDBC metastore</description>
</property>

<property>
  <name>javax.jdo.option.ConnectionDriverName</name>
  <value>com.mysql.jdbc.Driver</value>
  <description>Driver class name for a JDBC metastore</description>
</property>

<property>
  <name>javax.jdo.option.ConnectionUserName</name>
  <value>hive</value>
  <description>username to use against metastore database</description>
</property>

<property>
  <name>javax.jdo.option.ConnectionPassword</name>
  <value>hive</value>
  <description>password to use against metastore database</description>
</property>

 <property> 
 <name>hive.exec.local.scratchdir</name>
 <value>/home/hadoop/iotmp</value>
 <description>Local scratch space for Hive jobs</description>
 </property>

 <property>
 <name>hive.downloaded.resources.dir</name>
 <value>/home/hadoop/iotmp</value>
 <description>Temporary local directory for added resources in the remote file system.</description>
 </property>

<property>
    <name>hive.cli.print.current.db</name>
    <value>true</value>
    <description>Whether to include the current database in the Hive prompt.</description>
</property>
```

첫번째 property에 언급된 3306 번호는 mysql 포트번호다.

```bash
$ mkdir -p /home/hadoop/iotmp
$ chmod -R 775 /home/hadoop/iotmp/
```



MYSQL Connector 다운로드 및 hive lib로 복사

```bash
$ cd /tmp
$ wget http://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.38.tar.gz
$ tar xzvf mysql-connector-java-5.1.38.tar.gz
$ cd mysql-connector-java-5.1.38
$ mv mysql-connector-java-5.1.38-bin.jar /opt/hive/current/lib/
```



Hive 기본 디렉터리 생성 및 권한 추가

```bash
$ hdfs dfs -mkdir /tmp
$ hdfs dfs -mkdir -p /user/hive/warehouse
$ hdfs dfs -chmod -R 777 /tmp
$ hdfs dfs -chmod -R 777 /user/hive/warehouse
```



메타스토어 초기화

```bash
$ schematool -initSchema -dbType mysql
```

*에러 발생 시 xml 관련인지 확인해보고 데이터베이스 겹치는 문제라면 mariadb에 접속해 아래 명령 실행한다.

```sql
show databases에서 drop database hive;
```



hive 실행

```bash
$ hive
```

[http://192.168.56.101:50070](http://192.168.56.101:50070/)에 접속해 Utitlies > Browser Directory 에 들어가 user, hive, warehouse를 차례로 클릭한 곳에서 하둡에 잘 장착됐는지 확인 가능하다.

hive 종료 

```sql
> exit;
```



(두번째 버전) beeline 접속 위한 추가 작업

beeline은 그룹과 유저가 other이다. 모든 그룹과 호스트에게 접속하기 위한 관문역할의  proxy를 모두가 가능하도록 변경한다.

```bash
$ cd $HADOOP_HOME/etc/hadoop
$ vi core-site.xml
```

beeline은 others 권한으로 접속하므로, HDFS 권한을 수정한다.

```bash
$ hdfs dfs -chmod -R 777 /tmp
$ hdfs dfs -chmod -R 777 /user/hive/warehouse
```

core-site.xml 수정하여 nn01, dn02에 복사.

```bash
$ scp core-site.xml hadoop@nn01:/opt/hadoop/current/etc/hadoop
$ scp core-site.xml hadoop@dn02:/opt/hadoop/current/etc/hadoop
```

beeline 접속, 종료 명령어

```bash
$ beeline
```

```sql
!quit
```



hive에서 insert한 데이터는 데이터베이스가 아닌 하둡 파일 처리한 것이다.

### 1.3 hive 테스트

dn1 root 계정

unzip 설치한다.

```bash
$ yum install unzip
```

dn1 hadoop 

```bash
$ mkdir -p home/hadoop/hive_data
$ cd home/hadoop/hive_data/
$ wget http://www.grouplens.org/system/files/ml-100k.zip
$ unzip ml-100k.zip
```

영화 관련 데이터를 받았다. 데이터를 간단히 확인해본다.

```bash
$ head -3 ml-100k/u.user
```

데이터를 하둡에 저장한다.

```bash
$ hdfs dfs -mkdir -p /user/hadoop/movies
$ hdfs dfs -mkdir -p /user/hadoop/userinfo
$ hdfs dfs -put /home/hadoop/hive_data/u.item /user/hadoop/movies
$ hdfs dfs -put /home/hadoop/hive_data/u.user /user/hadoop/userinfo
```

정상적으로 저장했는지 확인한다.

http://192.168.56.101:50070/explorer.html#/

```mysql
CREATE TABLE movies (    
    movie_id INT,    
    movie_title STRING,    
    release_date STRING,    
    video_release_date STRING,    
    imdb_url STRING,
    unknown INT,
    action INT,
    adventure INT,
    animation INT,
    children INT,
    comedy INT,    
    crime INT,    
    documentary INT,    
    drama INT,    
    fantasy INT,    
    film_noir INT,    
    horror INT,    
    musical INT,    
    mystery INT,    
    romance INT,    
    sci_fi INT,    
    thriller INT,    
    war INT,    
    Western INT
	)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '|'
	STORED AS TEXTFILE;
```

하둡에 저장한 데이터를 메타스토어에 저장한다.

```mysql
load data inpath '/user/hadoop/movies' into table movies;
select * from movies limit 10;
```

load data로 데이터 inpath 시 data 경로가 달라진다. (/user/hive/warehouse ...)

달라지지 않게 할 수도 있다.

파일을 load해 메타스토어에 저장한다.

```mysql

```



## 2. sqoop

![hadoop dw](https://user-images.githubusercontent.com/73984112/105276625-27e6af00-5be5-11eb-986b-e3d552413dd3.jpg)

RDBMS에서 데이터를 수집해 hadoop에 보내는 역할.

JDBC와 호환되는 DB에서 사용 가능하다.

버전 1을 간소화한 버전 2가 있으나, 지원하는 것이 더 많은 버전 1이 아직 많이 사요된다.



### 2.1 sqoop설치

dn01 root 

다운로드

```bash
$ cd /tmp
$ wget http://mirror.apache-kr.org/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
$ mkdir -p /opt/sqoop/1.4.7
$ mv sqoop-1.4.7.bin__hadoop-2.6.0/* /opt/sqoop/1.4.7/
$ ln -s /opt/sqoop/1.4.7 /opt/sqoop/current
```

MYSQL Connector 다운로드 및 sqoop 의  lib로 복사

```bash
$ cd /tmp
$ wget http://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.38.tar.gz
$ tar xzvf mysql-connector-java-5.1.38.tar.gz
$ cd mysql-connector-java-5.1.38
$ mv mysql-connector-java-5.1.38-bin.jar /opt/sqoop/current/lib/
$ cd ~
$ ls /opt/sqoop/current/lib/
```

hadoop 계정으로 소유권 변경

```bash
$ chown -R hadoop:hadoop /opt/sqoop/
```

dn01 hadoop

```bash
$ su - hadoop
$ vi ~/.bash_profile
```

환경변수를 설정한다.

```
#### SQOOP 1.4.7 ######################
export SQOOP_HOME=/opt/sqoop/current
export PATH=$PATH:$SQOOP_HOME/bin
#### SQOOP 1.4.7 ######################
```

```bash
$ source ~/.bash_profile
```

sqoop 환경 설정

```bash
$ cd $SQOOP_HOME/conf
$ mv sqoop-env-template.sh sqoop-env.sh
$ vi sqoop-env.sh
```

```
export HADOOP_COMMON_HOME=/opt/hadoop/current
export HADOOP_MAPRED_HOME=/opt/hadoop/current
export HIVE_HOME=/opt/hive/current
```

스쿱 확인

```bash
$ sqoop-version
```

스쿱 라이브러리를 하둡에 복사

```bash
$ cd $SQOOP_HOME
$ cp sqoop-1.4.7.jar /opt/hadoop/current/share/hadoop/tools/lib/
```



### 2.2 scoop 써보기

터미널에서 mariadb에 접속하지 않고 databases 목록을 가져올 수 있다.

```bash
$ sqoop list-databases --connect jdbc:mysql://dn01 --username hive --password hive
```

테이블 조회를 위해선 포트 번호와 데이터베이스를 추가한다.

```bash
$ sqoop list-tables --connect jdbc:mysql://dn01:3306/sqoopdemo --username hive --password hive
```

터미널에서 mysql 데이터를 하둡에 바로 import 할 수 있다.

```bash
$ sqoop import --connect jdbc:mysql://dn01:3306/sqoopdemo --table departments --username hive --password hive
```

조회해보면 hadoop에 /user/hadoop 경로에 테이블 이름의 새로운 폴더가 생성되어 있다.

```bash
$ hdfs dfs -ls /user/hadoop
```

또한 하둡 데이터를 mysql에 export도 가능하다.

```bash
$ sqoop export --connect jdbc:mysql://dn01:3306/sqoopdemo --table dept --username hive --password hive --export-dir departments
```

mysql 접속해 확인해보면 데이터가 잘 들어갔다.

```mysql
select * from dept;
```







## 3 Spark

dn01 root 

```bash
$ cd /tmp
$ wget http://apache.mirror.cdnetworks.com/spark/spark-2.4.7/spark-2.4.7-bin-hadoop2.7.tgz
$ tar xzvf spark-2.4.7-bin-hadoop2.7.tgz
$ mkdir -p /opt/spark/2.4.7
$ mv spark-2.4.7-bin-hadoop2.7/* /opt/spark/2.4.7/
$ ln -s /opt/spark/2.4.7 /opt/spark/current
$ chown -R hadoop:hadoop /opt/spark/
$ su - hadoop
```

dn02 hadoop

```bash

```







## 4 Zeppelin



dn01 root
cd /tmp
wget http://apache.mirror.cdnetworks.com/zeppelin/zeppelin-0.8.2/zeppelin-0.8.2-bin-all.tgz
tar -xzvf zeppelin-0.8.2-bin-all.tgz
mkdir -p /opt/zeppelin/0.8.2
mv zeppelin-0.8.2-bin-all/* /opt/zeppelin/0.8.2/
ln -s /opt/zeppelin/0.8.2 /opt/zeppelin/current

chown -R hadoop:hadoop /opt/zeppelin
su - hadoop



dn01 hadoop

vi ~/.bash_profile

\#### zeppelin ######################
export ZEPPELIN_HOME=/opt/zeppelin/current
export PATH=$PATH:$ZEPPELIN_HOME/bin
\#### zeppelin ######################

source ~/.bash_profile


cd /opt/zeppelin/current/conf
cp zeppelin-site.xml.template zeppelin-site.xml
cp shiro.ini.template shiro.ini
cp zeppelin-env.sh.template zeppelin-env.sh


vi shiro.ini
admin = admin, admin


vi zeppelin-site.xml

```xml
<property>
<name>zeppelin.server.addr</name>
<value>192.168.56.102</value>
<description>Server address</description>
</property>

<property>
<name>zeppelin.server.port</name>
<value>8082</value>
<description>Server port.</description>
</property>

<property>
<name>zeppelin.anonymous.allowed</name>
<value>false</value>
<description>Anonymous user allowed by default</description>
</property>
```


vi zeppelin-env.sh

export HADOOP_CONF_DIR=/opt/hadoop/current/etc/hadoop
export JAVA_HOME=/opt/jdk/current

\# 스파크설치된 경우는 아래도 세팅
\#export SPARK_HOME=/opt/spark/current
\#export SPARK_CONF_DIR=/opt/spark/current/conf
\#export PYTHONPATH=/home/hadaoop/anaconda3/bin/python3
\#export PYSPARK_PYTHON=/home/hadoop/anaconda3/bin/python3

\----------------------------------------------
당연히 하둡 실행

```bash
$ start-all.sh
```

\----------------------------------------------
마리아디비 서비스 확인

```bash
$ systemctl status mariadb.service
$ systemctl restart mariadb.service
```

하이브 서버 기동

```bash
$ hive --service hiveserver2 &
```

(에러태그 만들기) Error

2021-01-22 01:21:42: Starting HiveServer2
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/opt/hive/2.3.8/lib/log4j-slf4j-impl-2.6.2.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/opt/hadoop/2.7.7/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]

에서 멈춘다.

Solution

1. jline 과거버전 삭제

   /opt/hadoop/2.7.7/share/hadoop/kms/tomcat/webapps/kms/WEB-INF/lib/jline-0.9.94.jar

   /opt/hadoop/2.7.7/share/hadoop/httpfs/tomcat/webapps/webhdfs/WEB-INF/lib/jline-0.9.94.jar

2. multiple SLF4J bindings 오류가 발생한 라이브러리 중 /opt/hive/2.3.8/lib/log4j-slf4j-impl-2.6.2.jar 삭제 (삭제 전 백업 필수)

메타 스토어 실행

```bash
$ hive --service metastore &
```

제플린 interpreter에 hive jdbc library 복사

```bash
$ cp /opt/hive/current/jdbc/hive-jdbc-2.3.8-standalone.jar  /opt/zeppelin/current/interpreter/jdbc/
```

재플린 서버 기동 (&는 백그라운드 실행 의미)

```bash
$ zeppelin.sh start & 
$ ps -ef | grep zeppelin
```

아래 링크로 zeppelin 웹 화면에 접속한다.
[http://192.168.56.102:8082](http://192.168.56.102:8082/)

아이디 admin, 비밀번호 admin으로 로그인하고 화면에서 오른쪽 'admin' > 'Interpreter' 에서 'jdbc' 검색해 아래처럼 세팅을 변경한다.
default driver = org.apache.hive.jdbc.HiveDriver
default url = jdbc:hive2://192.168.56.102:10000
default.user = hive
default.password = hive

준비가 끝났다. + Create new note 클릭하고 jdbc 선택해 작업 공간을 생성한다.

show databases 명령어 입력 후 Shift + Enter 시 (세미콜론 ; 은 뺀다) database 조회가 잘 되는지 확인한다.

