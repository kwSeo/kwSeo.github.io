---
layout: post
title: "TOAST Cloud로 Hadoop 구축하기까지"
---

#TOAST Cloud
---
**NHN 엔터테인먼트에서 제공하는 클라우드 서비스입니다.**

- 누구나 쉽게 개발할 수 있는 서비스를 제공하는 퍼블릭 클라우드
- 개발에만 전념할 수 있또록 인프라에서 플랫폼까지 다양한 솔루션 제공
- 웹 브라우저 상에서 인프라에서 플랫폼까지 모두 관리할 수 있는 콘솔 제공
- 합리적 비용으로 사업화에 기여

#TOAST Cloud를 통해 Hadoop 구축해보기
---
우연찮게 NHN의 CodeCamp@PlayMuseum에 선발되어 TOAST Cloud를 무료로 이용할 수 있는 기회를 가지게 되었습니다.<br>
기존에 아는 형에게서 ncloud를 빌려서 사용하고 있었는데 마침 기한이 다되갈 무렵 새로운 클라우드 서비스를 이용할 수 있게 되었네요.ㅎㅎㅎ<br>
위에서 설명했듯이 TOAST Cloud는 NHN 엔터테인먼트에서 제공하는 AWS(Amazon Web Services) 같은 클라우스 서비스입니다.<br>
NHN CodeCamp@PlayMuseum에 참가해게 되면서 프로젝트 개발에 사용할 수 있도록 NHN 엔터테인먼트 측에서 무료로 서비스를 제공받았죠.<br>
그런데 저희 팀이 진행하는 프로젝트 주제는 별로 서버를 필요로하지 않기에 별로 사용할 일이 없었습니다....<br>
하지만 이 기회를 무의미하게 날려버리고 싶진 않았고, 마침 Hadoop을 조금씩 보던 터라 인프라를 마음 껏 이용할 수 있는 이번 기회에 한번 Hadoop 분판 파일 시스템을 구축해보고자 합니다!

##TOAST Cloud 콘솔 & 인스턴스 생성 및 설정

![TOASTCloudConsole](http://googledrive.com/host/0B-OVDZGx-Flnai03YWlyZWk3b0U)

위 화면이 TOAST Cloud의 콘솔 메인화면입니다. 제가 속한 팀의 프로젝트가 보이네요. 그리고 TOAST Cloud의 메인 색상은 파란색인 모양입니다. 저도 파란색 좋아하는데ㅎㅎㅎ

![Instances](http://googledrive.com/host/0B-OVDZGx-FlnVHN5V3RNdXBRa2M)

이미 생성된 인스턴스들이 보이네요. 하지만 이번에 저 인스턴스들은 사용되지 않습니다. 저희는 이번에 Hadoop을 위한 새로운 인스턴스를 생성할 것입니다.
(참고로 인스턴스 하나가 컴퓨터 한대라고 생각하시면 됩니다)

![CreateInstances1](http://googledrive.com/host/0B-OVDZGx-Flnbm5kUGxjUUVnTVk)

![CreateInstances2](http://googledrive.com/host/0B-OVDZGx-FlncU1fU3BJbzdqZUE)

이제 인스턴스를 생성합시다. 먼저 master노드를 위한 인스턴스를 생성합니다. 이 인스턴스가 hadoop의 namenode를 가질 것입니다. 그리고 TOAST Cloud에서는 SSH 접속 로그인시 키파일을 사용합니다. 그러니 기존의 키를 선택하거나 새로 키를 생성합니다. 이때 어떤 키를 선택하였는지 기억하고 있어야합니다. 뒤에서 생성할 slave 노드 인스턴스들도 동일한 키를 가져야합니다.

![CreateInstanceSlave](http://googledrive.com/host/0B-OVDZGx-FlnSlROR2FCSWh0UmM)

slave노드 인트턴스도 생성합니다. 'instance 수'라는 항목을 통해 동일한 조건의 인스턴스를 동시에 여러개 생성할 수 있습니다. 그리고 이전에 생성한 master노드와 동일한 키와 네트워크를 선택합니다.

자 모두 생성되었으니 이제 Hadoop을 이용하기 위한 포트를 설정합니다. TOAST Cloud에서는 Infrastructure-Network&Security-Security Groups라는 서비스를 제공합니다. 이 서비스를 이용하면 따로 인스턴스 내에서 복잡하게 iptables같은 것을 건드릴 필요가 없어집니다! 새로운 Security Group을 생성하여 포트를 Hadoop을 위한 포트를 설정해줍니다. 

![SecurityGroup](http://googledrive.com/host/0B-OVDZGx-FlnTDByWHNnSENJbVE)

새로운 Security Group을 생성하였으면 앞에서 생성한 인스턴스들에게 적용하여 줍니다. Infrastructure-Compute-Instances에서 적용할 수 있습니다. 마지막으로 Master노드에 Floating IP를 할당해줍니다. 할당받은 Floating IP를 통해서만 외부에서 인스턴스에 접근할 수 있습니다. Slave노드는 Floating IP를 할당하지 않아도 됩니다. 

##JDK 설치
다운받은 키를 이용하여 생성된 인스턴스에 접속합니다. 이제 Hadoop을 설치하여야합니다. 먼저 Hadoop을 사용하기 위해서는 Java가 필요하니 JDK를 설치합니다. 지금부터의 명령어들은 CentOS6.5를 기준으로 합니다. 전 편리하게 구축하기 위해 root 개정으로 접속해서 사용하였습니다. Slave노드에도 설치해 줍니다.

	#yum install java-1.8.0-openjdk-devel

##환경설정
호스트네임을 바꿔줍니다. 아직 이유는 정확하게 모르겠는데 인스턴스를 생성하면서 자동으로 설정된 호스트네임으로는 hadoop 실행시 오류가 발생하더군요. 전 속편하게 호스트네임을 IP와 동일하게 주었습니다. Master노드뿐만이 아니라 Slave노드도 바꾸었습니다.(아래의 명령으로 호스트네임을 바꿀시 재부팅되면 호스트네임이 원래대로 돌아옵니다.)

	#hostname 192.168.0.15
	
이제 Hadoop을 설치합니다. wget으로 가져오던 FTP로 가져오던 상관없습니다. 일단 Hadoop파일들을 서버로 가져옵니다. 전 wget을 사용하였고 Hadoop 버전은 1.2.1로 하였습니다.

	#wget "http://apache.mirror.cdnetworks.com/hadoop/common/hadoop-1.2.1/hadoop-1.2.1.tar.gz"

다운받았으면 압축을 풉니다! 그리고 자신이 원하는 디렉터리로 옮깁니다!! 전 '/usr/local'디렉터리로 옮기겠습니다.

	#tar -xvzf hadoop-1.2.1.tar.gz
	#mv hadoop-1.2.1 /usr/local

다음으로 Hadoop을 위한 환경변수을 설정합니다. 그리고 겸사겸사 아까 설치했던 JDK의 환경변수도 함께 설정합니다.

	#vim /etc/profile
		...
		export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
		export PATH=$PATH:$JAVA_HOME/bin
		export CLASSPATH="."
		export HADOOP_HOME=/usr/local/hadoop-1.2.1
		export PATH=$PATH:$HADOOP_HOME/bin
		export HADOOP_HOME_WARN_SUPPRESS=TRUE
		:wq
	#source /etc/profile
	#java -version
	#hadoop version
	
마지막 'java -version', 'hadoop version'명령에서 제대로 버전정보가 출력되면 환경변수 설정은 끝난 것입니다. Slave노드에도 동일한 설정을 해줍니다.

##키 설정
이때 로그인과 같은 인증 작업을 자동화하기 위해 키를 설정해주어야하며, 이때 앞에서 인스턴스를 생성할때 선택하고 Master 노드에 SSH 접속하기 위해 사용하였던 키파일가 필요합니다. 선택했던 키 파일을 Master노드로 가져옵니다.(FTP 등을 통해) 이 키파일을 이용하면 Slave노드 인스턴스에 SSH 접속할때 비밀번호를 입력할 필요가 없어집니다. 이것을 위해서 처음 인스턴스를 생성할때 같은 키로 선택해야한다고 한 것입니다. 또한 TOAST Cloud를 통해 인스턴스를 생성하였다면 이미 RSA가 설정되어있어 복잡한 절차없이 키파일만 가져와 이름만 바꾸어주면 설정이 끝납니다.
	
	FTP 등으로 통해 키파일 가져오고...
	#mv hadoop-test-key.pem ~/.ssh
	#mv hadoop-test-key.pem id_rsa
	#ssh 192.168.0.16
	
위와 같은 짧은 명령으로 끝입니다. 'ssh [slave node ip]'명령을 실행했을때 비밀번호를 묻지 않는다면 제대로 설정된 것입니다. Master노드 뿐만이아니라 Slave노드에도 동일한 키설정을 해줍니다. 참고로 키파일의 소유자는 root이면 안됩니다!

##Hadoop 설정
이제 드디어 Hadoop을 건드리는 타임이 왔습니다. 일단 먼저 Hadoop이 설치된 디렉터리로 이동합니다.

	#cd $HADOOP_HOME

이제부터 conf디렉터리 내의 설정파일들을 수정할 것입니다. 먼저 핵심인 core부터 수정하겠습니다.

	#vim conf/core-site.xml
		<configuration>
			<property>
				<name>fs.default.name</name>
				<value>hdfs://192.168.0.15:9000</value>
			</property>
		</configuration>

위와 같이 'core-site.xml'파일 수정해 줍니다. 'value'태그에는 Master노드의 주소를 입력해줍니다. 다음은 env파일입니다.

	#vim hadoop-env.sh
		...
		export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
		...

'hadoop-env.sh'파일 열어 JAVA_HOME부분의 주석을 제거하고 설치된 경로를 입력합니다. 다음은 HDFS설정입니다.
	
	#vim conf/hdfs-site.xml
		<configuration>
		    <property>
		            <name>dfs.name.dir</name>
		            <value>/usr/local/hadoop-1.2.1/filesystem/name</value>
		    </property>
		    <property>
		            <name>dfs.data.dir</name>
		            <value>/usr/local/hadoop-1.2.1/filesystem/data</value>
		    </property>
		</configuration>
	
'hdfs-site.xml'파일을 열러 저장소를 설정합니다. dis.name.dir은 name노드를, dfs.data.dir은 data노드를 의미하며 각각 디렉터리를 지정하여 줍니다.
다음은 맵리듀스(Job Tracker) 설정입니다.

	#vim conf/mapred-site.xml
		<configuration>
		     <property>
	                <name>mapred.job.tracker</name>
	                <value>192.168.0.15:9001</value>
	        </property>
	        <property>
	                <name>mapred.local.dir</name>
	                <value>/usr/local/hadoop-1.2.1/filesystem/local</value>
	        </property>
	   </configuration>

'hdfs-site.xml'파일을 수정하듯이 Master노드의 주소와 local 디렉터리 폴더를 지정합니다. 그리고 'masters'파일에는 Master노드의 주소를, 'slaves'파일에는 Salve노드의 주소를 입력해줍니다.

	#vim conf/masters
		192.168.0.15
		:wq
	#vim conf/slaves
		192.168.0.15
		192.168.0.16
		192.168.0.17
		:wq

다음은 HDFS의 포맷입니다.

	#hadoop namenode -format

그럼 'hdfs-site.xml'에서 입력했던 경로대로 Name노드 경로가 생성된 것을 확인할 수 있을 것입니다. 이제 이 디렉터리에 'hdfs-site.xml'과 'mapred-site.xml'에서 입력했던 data경로와 local경로를 만듭니다.

	#mkdir filesystem/data
	#mkdir filesystem/local
	
##동기화
복잡한 Hadoop 설정일 모든 Slave노드에 일일이 해주는 것은 여간 힘든일이 아닙니다. 그래서 동기화를 위해 rsync라는 도구를 사용하고자 합니다.

	#yum install rsync.x86_64
	#rsync -a 192.168.0.15:/usr/local/hadoop-1.2.1 /usr/local

위와 같은 명령을 모든 Slave노드에서 수행합니다.

##실행 및 확인
**실행**

	#start-all.sh

**종료**
	
	#stop-all.sh
	
위 스크립트파일은 $HADOOP_HOME/bin에 있으며 각각 실행과 종료를 합니다. 실행된 Hadoop은 jps라는 명령을통해 확인할 수 있습니다.

	#jps
	
위 명령을 실행하면 Master노드에서는 

- NameNode
- JobTracker
- Jps
- DataNode
- TaskTracker
- SecondaryNameNode

를 확인할 수 있으며, Slave노드에서는

- DataNode
- TaskTracker
- Jps

를 확인할 수 있습니다.
