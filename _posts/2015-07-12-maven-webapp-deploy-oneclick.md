---
layout: post
title: "Maven을 활용한 웹앱 원클릭 배포"
categories: Maven Tomcat Deploy
---
#웹앱 배포
웹애플리케이션을 개발하여 배포하고자할 때 프로젝트를 war파일로 패키징하여 톰캣의 배포 폴더로 옮기는 식으로 하곤 합니다. 하지만 이런 방식을 반복하다보면 귀찮음을 느끼게 됩니다. 거기다 개발서버와 배포서버로 따로따로 구분되어 있다면 이 귀찮은 과정은 배가 됩니다. 톱캣 관리 페이지(서버주소/manager)를 통해 좀더 편리하게 배포할 수도 있지만 역시 이도 귀찮습니다.<br>
Java에게는 Maven이라는 훌륭한 빌드툴이 존재합니다. Maven에게 이런 귀찮은 작업을 대신하도록 할 수 있습니다.

#필요한 것
Maven으로 배포하는 것이니 당연히 Maven이 필요합니다. 그리고 Maven에서 자동으로 배포를 해줄 플러그인이 필요합니다. 

    <plugin>
        <artifactId>maven-war-plugin</artifactId>
        <version>{war-plugin 버전. 필자는 2.4 사용}</version>
        <configuration>
            <warSourceDirectory>{웹소스 경로}</warSourceDirectory>
            <webXml>{web.xml파일 경로}</webXml>
        </configuration>
    </plugin>
    <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>{tomcat7-maven-plugin 버전. 필자는 2.2 사용}</version>
        <configuration>
            <url>{서버 url}/manager/text</url>
            <path>{배포 경로}</path>
            <update>true</update>
            <username>{톰캣 관리자 이름(ID)}</username>
            <password>{톰캣 관리자 비밀번호}</password>
        </configuration>
    </plugin>
    
##플러그인에 대한 설명
2개의 플러그인이 필요합니다.
 - maven-war-plugin
 - tomcat7-maven-plugin
 
warSourceDirectory는 웹애플리케이션의 소스 경로를 지정하고, webXml은 web.xml파일의 경로를 지정하면 됩니다. 두 번째 tomcat7-maven-plugin으로 아파치 톰캣7을 위한 플러그인입니다. url은 자신의 배포서버 관리페이지의 인터페이스 경로로 지정합니다. 기본은 '서버주소/manager/text'입니다. path는 해당 웹앱의 배포경로를 의미합니다. 예로들어 주소가 //123.1.2.33/myWebApp 이라면 /myWebApp이 path태그에 들어갈 값이라고 할 수 있습니다. update 태그는 true와 false값을 가지는데 true일 경우에는 기존에 동일한 배포경로를 가지는 웹앱이 있을 경우 새로운 웹앱으로 업데이트한다는 의미입니다. username과 password는 manager-script권한이 있는 톰캣 계정의 유저네임과 비밀번호를 의미합니다.

##톰캣 관리자 계정
톰캣은 톰캣서버를 관리하는 관리자 계정이 존재합니다. 기본적으로는 활성화가 안되어 있기 때문에 사용자가 따로 설정을 해주어야합니다. <br>
톰캣이 설치된 디렉토리를 확인하면 conf폴더를 확인할 수 있습니다. 이 안에는 다양한 설정파일이 존재하는데 지금 필요한 것은 tomcat-users.xml파일입니다. 이 파일을 통해서 톰캣의 계정을 관리할 수 있습니다.

    ...
    <!--
        <role rolename="tomcat"/>
        <role rolename="role1"/>
        <user username="tomcat" password="tomcat" roles="tomcat"/>
        <user username="both" password="tomcat" roles="tomcat, role1"/>
        <user username="role1" password="tomcat" roles="roles1"/>
    -->

위 코드처럼 tomcat-users.xml파일을 열어보면 계정을 설정하는 부분이 주석처리된 것을 볼 수있습니다. 저 주석을 해제하거나 아래에 새로운 계정을 생성하면 됩니다. 

    <role rolename="manager-script"/>
    <user username="tomcat" password="tomcat" roles="manager-script"/>
    
텍스트 인터페이스를 통해 배포를 하기 위해서는 'manager-script'라는 권한이 필요합니다. 그렇기 때문에 위 코드처럼 'manager-script'을 생성하고 유저네임과 아이디를 설정합니다.<br>
이렇게 생성된 계정을 위에서 준비한 플러그인의 해당하는 태그를 채우면 됩니다.

#빌드 및 배포
빌드를 하기 위해서 maven build 명령을 사용합니다. 이클립스를 예로들면 Run As -> Maven Build...를 클릭하면 

![이클립스 메이븐 빌드](https://0ba12f0db07e88ab08da87dd592708b14716406a.googledrive.com/host/0B-OVDZGx-FlnX012RWVLbF9icWs)

다음과 같은 그림을 볼 수 있습니다. 여기에서 Base Directory항목에는 배포하기를 원하는 프로젝트를 선택해주고 Goals항목에는 'tomcat7:deploy'라고 입력한 후 Run를 클릭합니다. 그럼 빌드와 함께 자동으로 지정한 서버로 배포하는 것을 볼 수 있습니다.<br>
참고로 tomcat7 플러그인이지만 tomcat8에서도 동작합니다.