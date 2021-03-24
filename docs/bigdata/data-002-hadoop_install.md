---
layout: default
title: Hadoop
parent: Big Data
nav_order: 1
last_modified_date: 2021-01-28
---
# 하둡 hadoop
{: .no_toc }
저비용, 대용량 데이터 처리 오픈 소스 플랫폼인 '하둡'은 Map Reduce, HDFS 기술로 적당한 가격으로 빅데이터를 분석할 수 있는 시대를 열었다. 오픈 소스이기 때문에 관련 프로그램이 다양하게 나타날 수 있었고, 빅데이터 기술의 폭 넓은 발전을 이뤄냈다. 

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>



# 1. 하둡 hadoop 설치

저비용, 대용량 데이터 처리 오픈 소스 플랫폼

Scale Out 분산처리

아파치 하둡, 맵알 하둡, 클라우데라 하둡, 호튼웍스 하둡 

(설치부터 어려워 클라우데라, 호튼웍스 같은 유료 서비스 존재)



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

이 글에선 하둡으로 분산처리를 구현하기 위해 Master 역할 1대, Slave 역할 2대 = 총 3대의 가상장치(Virtual Machine)를 설치한다. 현업에서는 20~30대의 Slave를 운영한다고 한다.



# 2. 윈도우

이 글에선 Windows 10 Home Edition 버전으로 진행한다.

## 2.1 Virtual Box 설치

Virtual Box https://www.virtualbox.org/   ⇒ Download ⇒ Vitural Box Old builds ⇒

[Downloads – Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)  6.1.16 버전 ⇒ Windows hosts 클릭



## 2.2 Vagrant, CentOS 7 설치

3대의 OS를 하나씩 설치하지 않고, 동시에 진행할 수 있다. 

https://releases.hashicorp.com/vagrant/2.2.2/ > vagrant_2.2.2_x86_64.msi

Vagrant의 Default 가상장치는 Virtual Box 이다.

명령 프롬프트(CMD)를 관리자 권한으로 실행하여 vagrant 설치 폴더로 이동, 아래 명령어를 실행한다.

```
cd C:\HashiCorp\Vagrant\
vagrant init
```

메모장을 관리자 모드로 열거나 VS Code, Notepad++ 등 편집기를 사용해 C:\HashiCorp\Vagrantfile 파일을 열고 모든 내용을 지운 후 아래 내용을 복사, 붙여넣기 한다.



```
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
 
 	config.ssh.insert_key = false
 
	 config.vm.define :nn01 do |nn01_config|
         nn01_config.vm.box = "centos/7"
         nn01_config.vm.hostname = "nn01"
         nn01_config.vm.network "private_network", ip: "192.168.56.101"
         nn01_config.vm.provider :virtualbox do |vb|
             vb.name = "nn01"
             vb.memory = "4096"
         end
      end

     config.vm.define :dn01 do |dn01_config|
         dn01_config.vm.box = "centos/7"
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
```

*주의: 웹페이지에서 복사 붙여넣기 할 경우 공백이나 보이지 않는 문자가 포함되어 오류가 발생할 수 있음

```bash
vagrant up
```

*주의: 오류가 발생할 경우 C:\HashiCorp\Vagrantfile 파일을 다시 열어 공백 제거 후 재시도, 에러메시지가 명확할 경우 해당 에러메시지 해결 시도



에러 발생





# 3. 리눅스

## 3.1 protobuf 설치

구글에서 공개한 오픈소스 직렬화 라이브러리.

프로토콜 버퍼는 데이터를 연속된 비트로 만들고, 이렇게 만들어진 비트를 해석해 원래의 데이터를 만들 수도 있다. 현재 다양한 시스템이 이기종 혹은 내부 프로세스 간의 통신에 프로토콜 버퍼를 사용하고 있으며, 하둡2도 내부 데몬 간의 데이터 통신을 위해 프로토콜 버퍼를 적용했다. ( 도서 ‘시작하세요 하둡프로그래밍’에서 언급하긴 했지만 설치 없어도 우리가 구동하는 것에는 문제가 되지 않았지만 혹시 몰라 설치해본다 )

```bash
su root
yum install -y autoconf automake libtool curl gcc-c++ unzip
cd /tmp
wget https://github.com/protocolbuffers/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
tar -zxvf protobuf-2.5.0.tar.gz
mv protobuf-2.5.0  /opt/
```

반드시 root로 변경 후 설치한다.

이동 후 잘 설치, 이동되었는지 확인한다.





## 3.2 JDK 8 설치

h. ln -s /opt/jdk/1.8.0_131 /opt/jdk/current 심볼릭 링크

심볼릭 링크를 쓰는 이유, 버전업 등 변경사항이 있을때마다 모든 설정을 바꾸지 않고 심볼릭 링크만 수정하면 된다.



## 3.3 하둡 설치(nn01 / dn01 / dn02)

편의상 MobaXterm의 MultiExec(여러개의 가상장치 동시 조작) 기능 사용을 추천한다.

```bash
$ cd /tmp
$ wget  https://archive.apache.org/dist/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz
$ tar -xvzf hadoop-2.7.7.tar.gz
$ tar -xvzf hadoop-2.7.7.tar.gz
$ mkdir -p /opt/hadoop/2.7.7
$ mv hadoop-2.7.7/* /opt/hadoop/2.7.7/
$ ln -s /opt/hadoop/2.7.7 /opt/hadoop/current

```

hadoop 사용자 추가

```bash
$ useradd hadoop
$ passwd hadoop   ( 비밀번호도 hadoop 으로 - 비밀번호 입력시는 글자표시 안됨  )
$ chown -R hadoop:hadoop /opt/hadoop/  ( 루트에서 만든 파일의 권한을 hadoop에게 권함)
$ su - hadoop
```

권한 관련 설정 변경

```bash
$ vi /etc/pam.d/su
```

아래 사진처럼 10, 11, 12 행 주석 처리한다.



이제 root 와 hadoop 간 계정 변경이 자유롭다.



자바 및 하둡 환경변수 추가

```bash
$ cd ~
$ vi ~/.bash_profile
```

vi 에디터에서 아래 내용을 추가한다.

```bash
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

```bash
$ source ~/.bash_profile
$ java -version
$ hadoop version
```



비밀번호없이 각노드를 접속할 수 있도록 공개키 공유(SSH)

*아래 작업은 가상머신에서 직접한다.

```bash
$ vi /etc/hosts
```

모든 내용 지우고, 아래 내용 복사 붙여넣기 후 :wq 명령어로 저장한다.

```
192.168.56.101 nn01
192.168.56.102 dn01
192.168.56.103 dn02
```

다시 MobaXterm의 MultiExec로 넘어와 아래를 입력하면 서로 다른 키 값이 생성된다.

```bash
$ ssh-keygen
```

이제 패스워드 없이 ssh로 계정간 이동이 가능하다. 테스트한다.

```bash
$ ssh dn01
$ ssh dn02
$ ssh nn01
```



## 3.4 하둡 설정

모든 가상장치에 동일하게 설정한다. 

MobaXterm으로 동시 작업 시 문제 발생 가능성이 있으므로 가급적 nn01에 먼저 설정한 이후 ssh 연결한 다른 가상장치에 복사한다.

```bash
$ vi /opt/hadoop/current/etc/hadoop/core-site.xml
```

```
<configuration>

<property>
<name>fs.defaultFS</name>
<value>hdfs://nn01:9000</value>
</property>

</configuration>
```

```bash
$ vi /opt/hadoop/current/etc/hadoop/hdfs-site.xml
```

```
<configuration>

<property>
<name>dfs.replication</name>
<value>1</value>
</property>

<property>
<name>dfs.namenode.http-address</name>
<value>nn01:50070</value>
</property>

<property>
<name>dfs.namenode.secondary.http-address</name>
<value>nn01:50090</value>
</property>

<property>
<name>dfs.namenode.name.dir</name>
<value>file:/home/hadoop/hadoop_data/hdfs/namenode</value>
</property>

<property>
<name>dfs.datanode.data.dir</name>
<value>file:/home/hadoop/hadoop_data/hdfs/datanode</value>
</property>

<property>
<name>dfs.namenode.checkpoint.dir</name>
<value>file:/home/hadoop/hadoop_data/hdfs/namesecondary</value>
</property>

<property>
<name>dfs.webhdfs.enabled</name>
<value>true</value>
</property>

</configuration>
```

```bash
$ vi /opt/hadoop/current/etc/hadoop/yarn-site.xml
```

```
<configuration>

<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>

<property>
<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>

<property>
<name>yarn.resourcemanager.scheduler.address</name>
<value>nn01:8030</value>
</property>

<property>
<name>yarn.resourcemanager.resource-tracker.address</name>
<value>nn01:8031</value>
</property>

<property>
<name>yarn.resourcemanager.address</name>
<value>nn01:8032</value>
</property>

<property>
<name>yarn.resourcemanager.hostname</name>
<value>nn01</value>
</property>

</configuration>
```

```bash
$ cp /opt/hadoop/current/etc/hadoop/mapred-site.xml.template /opt/hadoop/current/etc/hadoop/mapred-site.xml
```

```bash
$ vi /opt/hadoop/current/etc/hadoop/mapred-site.xml
```

```
<configuration>

<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>

<property>
<name>mapreduce.jobtracker.hosts.exclude.filename</name>
<value>$HADOOP_HOME/etc/hadoop/exclude</value>
</property>

<property>
<name>mapreduce.jobtracker.hosts.filename</name>
<value>$HADOOP_HOME/etc/hadoop/include</value>
</property>

</configuration>
```

```bash
$ vi /opt/hadoop/current/etc/hadoop/masters
```

```
nn01
```

```bash
$ vi /opt/hadoop/current/etc/hadoop/slaves
```

```
dn01
dn02
```

```bash
$ vi /opt/hadoop/current/etc/hadoop/hadoop-env.sh
```

```
# The java implementation to use.
export JAVA_HOME=/opt/jdk/current
```

```bash
$ vi /opt/hadoop/current/etc/hadoop/yarn-env.sh
```

```
# some Java parameters
export JAVA_HOME=/opt/jdk/current
```

설정을 마쳤으니 dn01, dn02에 복사한다.

nn01에서 root 계정으로 아래 명령어 실행

```bash
$ scp -r /opt/hadoop/* dn01:/opt/hadoop
```

yes 입력, 비밀번호 입력 후 기다린다. 완료되면 다시 한번 nn01에서 root 계정으로

```bash
$ scp -r /opt/hadoop/* dn02:/opt/hadoop
```

yes 입력, 비밀번호 입력 후 기다린다.

dn01에서 root 계정으로 심볼릭링크와 소유자를 다시 설정한다.

```bash
$ rm -rf /opt/hadoop/current
$ ln -s /opt/hadoop/2.7.7 /opt/hadoop/current
$ chown -R hadoop:hadoop /opt/hadoop/
```

dn02에서도 위 과정을 진행한다.

```bash
$ rm -rf /opt/hadoop/current
$ ln -s /opt/hadoop/2.7.7 /opt/hadoop/current
$ chown -R hadoop:hadoop /opt/hadoop/
```

node 디렉토리 생성

nn01(master)

```bash
$ mkdir -p ~/hadoop_data/hdfs/namenode
$ mkdir -p ~/hadoop_data/hdfs/namesecondary
```

dn01(slave)

```bash
$ mkdir -p ~/hadoop_data/hdfs/datanode
```

dn02(slave)

```bash
$ mkdir -p ~/hadoop_data/hdfs/datanode
```

nn01(master)

```bash
$ hadoop namenode -format
```

에러가 발생하지 않으면, 아래 명령어로 하둡을 기동한다.

```bash
$ start-all.sh
```





