---
layout: default
title: 오라클 SQL 에러 해결
parent: 데이터베이스
---



　

{: .no_toc }

# 오라클 Oracle SQL 에러 Error 해결 모음

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

SQLPlus 접속을 시도하면 아래와 같이 ORA-01033 에러가 발생하면

​	ORA-01033 ORACLE initialization or shutdown in progress

​	Process ID: 0

​	Session ID: 0 Serial number: 0

일부 data file에 에러가 발생한 것이다.

먼저 recover를 시도한다.

```sql
alter database datafil '경로' offline drop;
```

Database altered.

```sql
alter database open;
```

Database altered.



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











