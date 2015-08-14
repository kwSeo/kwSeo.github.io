---
layout: post
title: "지금까지의 NHN Ent. CodeCamp@PlayMuseum - 1"
categories: NHN_Ent. NHN_Ent._CodeCamp@PlayMuseum
---

#Summary
NHN CodeCamp@PlayMuseum에 참가하여 프로젝트를 진행한지 벌써 두달이 되어갑니다. 앞으로 남은 최종발표를 앞두고 잠시 지금까지의 일을 돌아보고자 합니다.(후기는 따로 작성할 예정)

#NHN Ent. CodeCamp@PlayMuseum
6월 말부터 NHN Ent. CodeCamp@PlayMuseum 1기로 참여하여 프로젝트를 진행해왔습니다. NHN Ent. CodeCamp@PlayMuseum은 NHN 엔터테인먼트에서 대학생을 대상으로 주최하였으며, 선발된 인원은 NHN 엔터테인먼트의 지원을 받아 자유주제로 프로젝트를 진행합니다. 저도 이번 1기에 선발되어 RDF(SPARQL)-Object Mapping Library라는 주제도 프로젝트를 진행해 왔습니다.(Github: [RDF(SPARQL)-Object Mapping Library](https://github.com/kwSeo/RDF-Object-Mapper)) 지금은 이제 8월 말의 최종발표를 앞두고 프로젝트를 완성하기위해 최선을 다하고 있죠.

#자유주제
NHN Ent. CodeCamp@PlayMuseum(이하 NHN CodeCamp)에 참석하게 진행하는 프로젝트는 완전히 자유주제입니다. 웹서비스, 모바일앱, 심지어 라이브러리까지 모든 분야에 걸쳐 어떤 주제로하던 상관없습니다. 사실 저희팀이 처음 CodeCamp에 지원하기 위하여 프로젝트 계획서를 작성할 때 주제가 라이브러리란 것에 걱정을 많이 했었습니다. 아무리 자유주제라도 일단 일단 사용자에게 서비스를 제공하는 애플리케이션 같은 것이어야 하지 않을까 하는 걱정이었습니다.(지금 쓰면서 생각해보니 라이브러리도 어쨋든 '사용자'에게 제공되는 서비스의 범주에 들어가나 하는 생각이 드네요...) 하지만 NHN CodeCamp 참가자로 선발되었고 프로젝트를 진행할 수 있께 되었습니다. **'완전 자유주제'**이었으니까요.  

#NHN Ent.에서 진행해준 간단한 사전 교육
이번 NHN CodeCamp에서 진행하는 프로젝트는 모두 Github릁 통해 공개하도록 했습니다. 저는 이전에 Github를 사용해본 경험이 있었는데... 그저 코드 검색, 클론 정도가 다였습니다. 그냥 코드 구경 정도로만 썼던거 같네요. 그런데 마침 NHN Ent.에서 간단한 Github, 라이센스 그리고 TOAST Cloud에 대한 교육을 해주었습니다. 이 교육은 짧았지만 정말 알차고 보람있으며 심지어 감사함을 느낄 정도였습니다. 너무나도 사용이 간편한 TOAST Cloud와 지금껏 별 생각없이 넘어갔던 라이센드, 그리고 무엇보다 저에게는 Github의 진짜 사용법을 알려주셨던 것이 너무나 감사했습니다. 덕분에 무료로 클라우드 서비스를 이용할 수 있었고 Github를 통해 코드관리, 이슈 및 마일스톤관리, 브랜치 등의 Github의 진면목을 깨달을 수 있었습니다. 처음에는 지행상황과 같은 개발 진행 상황들을 주기적으로 보고서를 작성하려고 했었는데 지금은 모두 Github를 통해서 정리하고 있습니다.^^

##블로그의 시작
지금 운영하는 이 블로그도 NHN CodeCamp에서 자극을 받아 시작하게 되었습니다. 이전부터 기술을 공유하는 블로그에 대해서 생각을 하고 있었는데 마침 NHN CodeCamp의 블로그 이야기와 교육 중에 들은 블로그에 대한 이야기가 자극이 되어 이 블로그가 탄생하게 되었습니다ㅎㅎㅎㅎㅎ;

 
#원정 개발
저희 팀은 팀원이 2명인데 서로 멀리 떨어져있습니다(100km 이상). 그덕에 서로 만나기가 힘들어 많은 어려움이 있었지요. 하지만 큰 문제가 되지는 않았습니다! 이미 처음 지원할때부터 그렇게할 예정이었으니까요! 그런데 지금와서 생각해보면 그때 당시에는 Github의 사용법을 제대로 알고있는 상황은 아니었는데 무슨 자신감이었는지 모르겠습니다ㅎㅎㅎ;;;

#주제 소개
제가 속한 팀에서 진행하는 프로젝트 주제는 RDF(SPARQL)-Object Mapping Library입니다. RDF(Resource Description Framework)-Object-Mapper는 SPARQL 쿼리 결과를 Java 객체에 매핑해주는 라이브러리입니다. 정의된 XML을 통해 설정을 하고 SPARQL을 관리해서 소스코드로부터 SPARQL을 독립시키도록 하고, 쿼리 결과를 지정한 클래스나 ResultMap 설정에 따라서 매핑을 하여 클래스 객체를 제공합니다. 즉, 가장 핵심은 Java에서 SPARQL를 편리하게 사용할 수 있게 하는 것입니다. 그런데 아직 이렇다 할 이름을 정해주지 못해서 RDF-Object-Mapper라는 이름을 사용하고 있습니다.(이름 정하는게 제일 힘든 듯) 아직 임시로 사용되는 이름이고 추후에 이 라이브러리만의 이름을 지어줄 것입니다. 배포 전까지는 정해야죠! 그리고 현재 스펙을 정리해서 위키를 만들고 있습니다. 자세한 사항은 GitHub로....

 - Github: [RDF(SPARQL)-Object Mapping Library](https://github.com/kwSeo/RDF-Object-Mapper)



