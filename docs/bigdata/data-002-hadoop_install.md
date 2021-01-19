---
layout: default
title: 하둡 Hadoop 설치
parent: 빅데이터
---





업데이트: 2021.01.19

[TOC]



# 하둡 Hadoop 설치

저비용, 대용량 데이터 처리 오픈 소스 플랫폼

Scale Out 분산처리

아파치 하둡, 맵알 하둡, 클라우데라 하둡, 호튼웍스 하둡 

(설치부터 어려워 클라우데라, 호튼웍스 같은 유료 서비스 존재)

![Hadoop-Ecosystem-2-01-1024x536](C:\Work\blog\003 Hadoop\Hadoop-Ecosystem-2-01-1024x536.jpg)

개발자 역할 데이터 시각화까지, 분석은 분석가 역할



2014.12 S*사 사례

Hive(HiveQL) 하둡 처리 좀 더 수월하게 

분석 R, Impala, Tajo(국내산, 고대)

실시간 처리 => 하드디스크 처리 but 메모리 처리 => Spark(Scala로 만들어짐) : 사용 가능 언어 Java, Python, Scala : Python으로 쓰거나 속도를 높이려면 Scala로!



예측 vs 분석

머신러닝 : 



하둡 클러스터 동작 방식

1. 독립모드
2. 의사분산모드
3. 완전분산모드 (Fully Distributed Mode): 가상장치 최소 3개

빅데이터 처리 프레임워크

하둡 1.0 : HDFS + MapReduce

하둡 2.0 : 하둡 1 + yarn (resource management)

다양한 에코 시스템

결함 허용 시스템 : 연결한 30~50대 중 1대가 고장이 나더라도 전체 시스템이 멈추지 않고 가야한다.

데이터 중복 저장: 결함을 허용하기 위해 필요

작은 규모 하둡을 구축하고, 기술력과 노하우를 쌓고 기업 스스로 해결할 수 있는 방향을 찾는 것이 하둡을 도입하는 가장 올바른 방향이다 <시작하세요 하둡 프로그래밍>



# 1. 윈도우

이 글에선 Windows 10 Home Edition 버전으로 진행한다.

## 1.1 Virtual Box 설치

분산처리 구현을 위해 Master 역할 1대, Slave 역할 2대 = 총 3대의 가상장치(Virtual Machine)를 설치한다.

Virtual Box https://www.virtualbox.org/   ⇒ Download ⇒ Vitural Box Old builds ⇒

[Downloads – Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)  6.1.16 버전 ⇒ Windows hosts 클릭



## 1.2 Vagrant, CentOS 7 설치

3대의 OS를 하나씩 설치하지 않고, 동시에 진행할 수 있다. 

https://releases.hashicorp.com/vagrant/2.2.2/ > vagrant_2.2.2_x86_64.msi

Vagrant의 Default 가상장치는 Virtual Box 이다.

명령 프롬프트(CMD)를 관리자 권한으로 실행하여 vagrant 설치 폴더로 이동, 아래 명령어를 실행한다.

```bash
cd C:\HashiCorp\Vagrant\
vagrant init
```

메모장을 관리자 모드로 열거나 VS Code, Notepad++ 등 편집기를 사용해 C:\HashiCorp\Vagrantfile 파일을 열고 모든 내용을 지운 후 아래 내용을 복사, 붙여넣기 한다.



VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

​     config.ssh.insert_key = false

​     config.vm.define :nn01 do |nn01_config|

​        nn01_config.vm.box = "centos/7"

​        nn01_config.vm.hostname = "nn01"

​        nn01_config.vm.network "private_network", ip: "192.168.56.101"

​        nn01_config.vm.provider :virtualbox do |vb|

​            vb.name = "nn01"

​            vb.memory = "4096"

​        end

​     end

​    config.vm.define :dn01 do |dn01_config|

​        dn01_config.vm.box = "centos/7"

​        dn01_config.vm.hostname = "dn01"

​        dn01_config.vm.network "private_network", ip: "192.168.56.102"

​        dn01_config.vm.provider :virtualbox do |vb|

​            vb.name = "dn01"

​            vb.memory = "4096"

​        end

​    end

config.vm.define :dn02 do |dn02_config|

​        dn02_config.vm.box = "centos/7"

​        dn02_config.vm.hostname = "dn02"

​        dn02_config.vm.network "private_network", ip: "192.168.56.103"

​        dn02_config.vm.provider :virtualbox do |vb|

​            vb.name = "dn02"

​            vb.memory = "4096"

​        end

​    end

end

*주의: 웹페이지에서 복사 붙여넣기 할 경우 공백이나 보이지 않는 문자가 포함되어 오류가 발생할 수 있음

```bash
vagrant up
```

*주의: 오류가 발생할 경우 C:\HashiCorp\Vagrantfile 파일을 다시 열어 공백 제거 후 재시도, 에러메시지가 명확할 경우 해당 에러메시지 해결 시도



에러 발생





# 2. 리눅스

## 2.1 protobuf 설치

구글에서 공개한 오픈소스 직렬화 라이브러리.

프로토콜 버퍼는 데이터를 연속된 비트로 만들고, 이렇게 만들어진 비트를 해석해 원래의 데이터를 만들 수도 있다. 현재 다양한 시스템이 이기종 혹은 내부 프로세스 간의 통신에 프로토콜 버퍼를 사용하고 있으며, 하둡2도 내부 데몬 간의 데이터 통신을 위해 프로토콜 버퍼를 적용했다. ( 도서 ‘시작하세요 하둡프로그래밍’에서 언급하긴 했지만 설치 없어도 우리가 구동하는 것에는 문제가 되지 않았지만 혹시 몰라 설치해본다 )

```BASH
su root
yum install -y autoconf automake libtool curl gcc-c++ unzip
cd /tmp
wget https://github.com/protocolbuffers/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
tar -zxvf protobuf-2.5.0.tar.gz
mv protobuf-2.5.0  /opt/
```

반드시 root로 변경 후 설치한다.

이동 후 잘 설치, 이동되었는지 확인한다.





JDK 8 설치

h. ln -s /opt/jdk/1.8.0_131 /opt/jdk/current 심볼릭 링크

심볼릭 링크를 쓰는 이유, 버전업 등 변경사항이 있을때마다 모든 설정을 바꾸지 않고 심볼릭 링크만 수정하면 된다.



하둡 설치(nn01 / dn01 / dn02)

편의상 MobaXterm의 MultiExec(여러개의 가상장치 동시 조작) 기능 사용을 추천한다.

```BASH
$ cd /tmp
$ wget  https://archive.apache.org/dist/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz
$ tar -xvzf hadoop-2.7.7.tar.gz
$ tar -xvzf hadoop-2.7.7.tar.gz
$ mkdir -p /opt/hadoop/2.7.7
$ mv hadoop-2.7.7/* /opt/hadoop/2.7.7/
$ ln -s /opt/hadoop/2.7.7 /opt/hadoop/current

```

hadoop 사용자 추가

```BASH
$ useradd hadoop
$ passwd hadoop   ( 비밀번호도 hadoop 으로 - 비밀번호 입력시는 글자표시 안됨  )
$ chown -R hadoop:hadoop /opt/hadoop/  ( 루트에서 만든 파일의 권한을 hadoop에게 권함)
$ su - hadoop
```

권한 관련 설정 변경

```BASH
$ vi /etc/pam.d/su
```

아래 사진처럼 10, 11, 12 행 주석 처리한다.



이제 root 와 hadoop 간 계정 변경이 자유롭다.



자바 및 하둡 환경변수 추가

```BASH
$ cd ~
$ vi ~/.bash_profile
```

vi 에디터에서 아래 내용을 추가한다.

```BASH
#### HADOOP 2.7.7 start ############
PATH=$PATH:$HOME/bin
export HADOOP_HOME=/opt/hadoop/current
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
#### HADOOP 2.7.7end############
#### JAVA 1.8.0 start#############
export JAVA_HOME=/opt/jdk/current
export PATH=$PATH:$JAVA_HOME/bin
#### JAVA 1.8.0 end##############
```

환경변수 설정 적용 후 자바, 하둡 버전 확인

```BASH
$ source ~/.bash_profile
$ java -version
$ hadoop version
```



