---
layout: default
title: 오라클 SQL 에러 해결 모음
parent: 데이터베이스
nav_order: 1
last_modified_date: 2021-01-28
---
# 오라클 Oracle SQL 에러 해결 모음
{: .no_toc }
Ctrl + F 또는 상단 검색창에 에러번호 또는 메시지를 입력해 검색하세요.

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


### ORA-01034, ORA-27101

에러메시지

ORA-01034 ORACLE not available

ORA-27101 shared memory realm does not exist

오라클 SQL이 알 수 없는 이유로 정상 작동하지 않아 발생한다. 아래와 같이 SQL 접속 후 startup 시도한다.

```bash
$ sqlplus /nolog
```

```sql
conn sys/ as sysdba
startup
```

만일 ORA-01157, ORA-01110 에러가 발생하면 data file 문제이다. 해당 에러 메시지를 검색해 방법을 확인한다.



### ORA-01119, ORA-2704 ...file

ORA-01119 error in creating database file

ORA-2704 file create error, unable to create file

리눅스 Sql에서 create tablespace 할 때 발생했다면, 폴더에 파일을 쓸 권한이 없어 발생한 문제다. 먼저 해당 폴더 권한을 777로 풀어준 후 create tablespace가 정상 완료되면, 권한을 이전으로 변경한다. 

참고로 권한을 777로 그대로 둔다면 아마존 EC2와 같은 클라우드 환경에서 SSH 접속이 거절될 수 있다. 이 오류 해결은 [아마존 AWS EC2 "server refused our key"오류에 대한 해결책](https://aws.amazon.com/ko/premiumsupport/knowledge-center/ec2-server-refused-our-key/)을 참고한다.



### ORA-01033, ORA-01157, ORA-01110 ... data file

만일 startup 시 ORA-01157, ORA-01110 에러가 발생하고, 

​	ORA-01157 cannot identify/lock data file ... - see ... trace file

​	ORA-01110 data file ...

SQL에 원래 있던 아이디(system 이나 본인이 만든 아이디)로  접속을 시도하면 아래와 같이 ORA-01033 에러가 발생한다.

​	ORA-01033 ORACLE initialization or shutdown in progress

​	Process ID: 0

​	Session ID: 0 Serial number: 0

일부 data file에 에러가 발생한 것이다.

먼저 아래와 같이 SQL에 접속해 shutdown 한다.

```sql
sqlplus /nolog
conn sys/ as sysdba
shutdown
```

그 다음 database recover를 시도한다.

```sql
recover database;
```

만일 ORA-00283: recovery session canceled due to errors 에러가 발생하면 아래와 같이 ORA-01110 에러에 나온 dbf 파일을 drop 한다. recover에 성공하면 아래 offline drop 명령어를 수행하지 않는다.

```sql
alter database datafile '경로' offline drop;
```

Database altered.

```sql
alter database open;
```

Database altered.

```sql
shutdown immediate;
startup
exit
```

그러면 SQL에 원래 있던 아이디(system 이나 본인이 만든 아이디)로 접속이 다시 가능해진다.

다시 SQL에 접속을 시도해 datafile 상태를 확인한다.

```sql
select status, name from v$datafile;
```

아래 사진처럼 RECOVER에 문제가 된 파일이 확인되면 데이터 파일 복원이 어려울 가능성이 높다.

![datafile 상태 확인](https://user-images.githubusercontent.com/73984112/106079849-5ae6f080-6159-11eb-8f9b-ff5868c60016.PNG)

tablespace 위치 확인은 아래 명령어로도 가능하다.

```sql
select * from DBA_DATA_FILES
```

원인은 파일 자체 손상일 수도 있고, 만일 아마존 AWS 사용자라면 폴더 위치에 따른 권한 문제일 수 있다. 이 문제를 방지하려면 반드시 datafile 기본 경로(위 사진의 다른 datafile이 저장된 위치)에 tablespace를 생성한다.

추가로 평소에 주기적으로 백업을 수행해 이와 같은 상황에 대비한다. 만일 백업 파일이 있다면 아래와 같이 database를 rename 한다.

```sql
alter database rename file '기존경로' to '새로운 경로';
alter database open;
```



### ORA-12505

접속 시 SID를 찾지 못해 발생한다.

먼저 리스너 상태를 확인한다.

```bash
$ lsnrctl services
```

만일 설정한 SID가 보이지 않는다면 SQL에 접속해 startup 시도한다.

```sql
sqlplus "/as sysdba"
startup
connect 접속계정/비밀번호
exit
```

다시 리스너 상태를 확인하면 설정할 SID가 보일 것이다. 만일 보이지 않는다면 listener.ora, tnsnames.ora 파일에서 HOST와 PORT를 다시 확인한다.



### 아마존 AWS 등 외부 접속 시 오류

1. 설정 확인

   윈도우에서 리눅스 Sql 접속 또는 리눅스에서 윈도우 Sql 접속 시 연결이 되지 않는 것은 listener.ora, tnsnames.ora 설정을 제대로 하지 않은 문제일 가능성이 높다.

   리눅스라면 $ORACLE_HOME/network/admin/에 이 파일들이 저장되어 있으며, HOST를 공인IP로 설정해야한다. 만일 아마존 AWS EC2라면, HOST를 퍼블릭 IPv4 DNS로 설정하고, SID Name을 일치시켜야 한다. (퍼블릭 IPv4 DNS는 ec2-x-xx-xx-xxx.ap-northeast-2.compute.amazonaws.com 이런 식의 주소이며, 대시보드에서 볼 수 있다)

2. 리스너 작동 확인 (설정파일 수정 이후)

   리눅스라면 lsnrctl start 실행을 해야만 리스너가 작동한다.

   윈도우라면 제어판 > 관리도구 > 서비스에서 oracleServiceORCL이 시작됨 상태인지 확인한다.

3. 방화벽 확인

   포트가 닫혀 있을 가능성이 있다. 윈도우라면 방화벽 인바운드 규칙에 포트를 추가하고, 리눅스라면 firewall-cmd --permanent --add-port=8080/tcp 등의 명령어로 포트를 열어야 한다. 아마존 AWS 라면 보안 그룹 > 인바운드 규칙에 포트를 추가해야한다.

4. SID Name 확인

   간혹 윈도우에서 SID Name이 ora 파일과 다르게 긴 경우가 있는데, CMD에서 GLOBAL_NAME을 확인해 SID 부분에 전체 이름을 입력한다. ora 파일에는 ORCL인데 GLOBAL_NAME은 ORCL.000.000.0 이런 식일 수 있다.

```sql
conn /as sysdba
select * from global_name;
```











