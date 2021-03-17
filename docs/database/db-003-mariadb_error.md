---
layout: default
title: MariaDB 에러 해결
parent: Database
nav_order: 2
last_modified_date: 2021-03-17
---
# MariaDB 에러 해결
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


## AWS Docker MariaDB "too many connection" 에러

### 1. max_connection, timeout 확인
{: .no_toc }

```sql
MariaDB [(none)]> show variables like 'max_connections'
MariaDB [(none)]> show variables like '%timeout%'
```



### 2. docker 설정파일 수정
{: .no_toc }

```bash
vi /etc/mysql/mariadb.conf.d/50-server.cnf
```

설정파일에 아래 내용 추가
interactive_timeout = 180
wait_timeout = 180



### 3. docker 재시작 (root 또는 sudo)
{: .no_toc }

```bash
service docker stop
service docker start
docker start mariadb
docker exec -i -t mariadb bash
mysql -u아이디 -p비밀번호
```



## AWS Docker Error response from daemon: Container ... is not running 에러

\* 컨테이너 이름 : mariadb

### 1. docker exec -i -t mariadb bash 접속 시 Container ... is not running 에러 발생
{: .no_toc }

docker ps -a (컨테이너 상태 확인)

### 2. docker 재시작, 에러 지속 여부 확인
{: .no_toc }

service docker stop
service docker start
docker start mariadb
docker exec -i -t mariadb bash

### 3. 에러 지속 시 에러 원인 확인
{: .no_toc }

docker logs mariadb

### 4. 만일 Container 내부 설정파일 오류라면 docker 내부 파일 직접 접근이 어려워 아래와 같이 파일을 복사해 온 후 수정, 다시 붙여 넣어 해결한다.
{: .no_toc }

docker container cp mariadb:/etc/mysql/mariadb.conf.d/50-server.cnf 50-server.cnf
50-server.cnf 편집 후 아래 명령어 실행
docker container cp 50-server.cnf mariadb:/etc/mysql/mariadb.conf.d/50-server.cnf

### 5. docker 재시작
{: .no_toc }

service docker stop
service docker start
docker start mariadb
docker exec -i -t mariadb bash(추가) 기존 MySQL 데이터를 새로운 컨테이너에서 사용하려면
docker container inspect -f '{{.Mounts}}' [container]
docker container run -d -v [volume_name]:/var/lib/mysql [image]참고
https://www.python2.net/questions-924172.htm
https://www.lesstif.com/docker/docker-hang-42663946.html
https://itholic.github.io/docker-copy/



