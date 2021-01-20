---
layout: default
title: 하둡 에코시스템
parent: 빅데이터
---

[TOC]

# 하둡 에코시스템 Hadoop Ecosystem











자바 기반 맵리듀스 프로그래밍이 너무 어려워 나온 것이 Pig. 하지만 Pig Latin 언어를 쓰는 스크립트 언어라 기존 DB 개발자에게 익숙한 SQL 언어를 사용하는 Hive가 나왔다.



## 1. 하이브 Hive

정형 데이터를 처리한다.

구조는 RDBMS(정형 데이터만 처리하는 이유), 데이터는 HDFS에 저장한다.

2가지 버전이 있다. 첫번째 버전 Hive는 하이브 메타 스토어 및 드라이버에 직접 접근하고, 두번째 버전 Beeline은 hiveserver2 API를 사용해 더욱 안전하다.

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

