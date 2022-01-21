# ELK 자료조사 (Update: 2021-09-19)
ELK란 Elasticsearch, Logstash, Kibana의 3가지 오픈소스를 부르는 말이다.

    Elasticsearch : 분산 검색 엔진, 데이터 저장(DB)
    Logstash      : 입력 값 필터링
    Kibana        : 시각화 도구
    Beats         : 시스템 정보 및 파일 로그 전달
위 4가지 도구를 이용하여 Sysmon(+syslog) 모니터링 도구를 만들 수 있다.   
<br>

## 1. ELK 서버 구축 및 사용법
#### 참고자료
> [**[DOCS]** Elastic Stack and Product Documentation](https://www.elastic.co/guide/index.html)   
> ( ※ 영어가 너무 싫은 사람은 [한국어 버전 docs](https://www.elastic.co/guide/kr/index.html)로 대략적인 내용을 파악하고, 부족한 부분은 아래에서 참고 )
>> **elastic 사이트 guide**: 가장 업데이트도 잘 되어있고, 설명이 친절하게 잘 되어 있음   
>> 
>> < 주로 참고할 부분 >
>> - Elastic Stack > [Getting Started [7.14]](https://www.elastic.co/guide/en/elastic-stack-get-started/current/index.html)
>> - Elasticsearch: Store, Search, and Analyze > [Elasticsearch Guide [7.14]](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
>> - Kibana: Explore, Visualize, and Share > [Kibana Guide [7.14]](https://www.elastic.co/guide/en/kibana/current/index.html)
>> - Beats: Collect, Parse, and Ship > [Beats Platform Reference [7.14]](https://www.elastic.co/guide/en/beats/libbeat/current/index.html) (+참고: [[github] elastic/beats](https://github.com/elastic/beats))
>>     + Beats 중에서도 [Winlogbeat](https://www.elastic.co/guide/en/beats/winlogbeat/current/index.html) 사용 ( Windows event log 수집 - Sysmon과 함께 사용 )
>> - Logstash: Collect, Enrich, and Transport > [Logstash Reference [7.14]](https://www.elastic.co/guide/en/logstash/current/index.html)

> [ ELK 스택 구축 (Elasticsearch, Logstash, Kibana, Filebeat) - tistory 2020.02.11 ](https://liveyourit.tistory.com/46)   
>> **실제 구현 시 도움될 자료**
>> - 스택: Elasticsearch, Logstash, Kibana, **Filebeat**
>> - 환경: Ubuntu
>> - Syslog

> [ELK Stack 설치와 사용방법 gitbook](https://sanghaklee.gitbooks.io/elk/content/)   
>> **ELK 각 스택에 대한 기초 개념 이해**
>> - 인프런&Youtube영상 기반 - 데이터 분석을 위한 ELK stack 설치와 사용 방법 ( [Youtube: ELK 재생목록](https://www.youtube.com/watch?v=J2PIBQgEpC4&list=PLVNY1HnUlO24LCsgOxR_eK2Yi4sOgH9Pg) )
>> - 환경: Ubuntu ( CentOS도 있음 - 참고: [ELK Install on CentOS](https://sanghaklee.gitbooks.io/elk/content/elk-centos.html) )
<br>


## 2. Sysmon 사용
#### 참고자료
> [Microsoft Sysmon Docs](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)

> [Sysmon을 이용한 System 탐지방안](http://www.igloosec.co.kr/BLOG_Sysinternals%20System%20Monitor(Sysmon)%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20System%20%ED%83%90%EC%A7%80%EB%B0%A9%EC%95%88?searchItem=&searchWord=&bbsCateId=18&gotoPage=1)
>> Sysmon에 대한 이해를 도움   

> [Sysmon: How to Set Up, Update, And Use?](https://cqureacademy.com/blog/server-monitoring/sysmon)
>> Sysmon에 대한 이해를 도움   

> [HOW To Hunt on Sysmon Data](https://medium.com/@concanno/how-to-hunt-on-sysmon-data-67f6661fd166)

> [Sysmon and Winlogbeat on your endpoints](https://cyberwardog.blogspot.com/2017/02/setting-up-pentesting-i-mean-threat_87.html)
<br>


## 3. Sysmon + ELK Stack
#### 참고자료
> [SYSMON+ELK STACK을 이용하여 간단한 EDR 만들기](https://two2sh.tistory.com/21)
>> 비교적 간단한 실습 자료

> [3차 프로젝트(Sysmon&Elastic Stack을 활용한 EDR 기술 구현)](https://www.notion.so/3-Sysmon-Elastic-Stack-EDR-47d4075579ae4ccc878dc6e98881dfb3#08461788f6e245dd944769733e09b091)
>> **★실제 구현 시 도움될 자료★**
>> 개요, 설치+사용법, 윈도우 로그 수집(Sysmon), 시각화 등 간단한 EDR을 구현하는 과정이 상세히 설명되어 있음

> [4차 프로젝트(오픈소스 에뮬레이션 ATOMIC RED TEAM을 활용한 관제 시스템 구현)](https://www.notion.so/4-ATOMIC-RED-TEAM-0d7e01023ecf4f6abeddc023ad71a8df#3ce8f608680e496f885341d67b03afe1)
>> **★실제 구현 시 도움될 자료★**
>> - ELK + Winlogbeat + Sysmon
>> - MITRE ATT&CK
>> - ATOMIC RED TEAM
>> - 공격 시나리오 기반 침투 테스트
>> - 대응 방안 (메시징 앱 경고 알림 - Telegram API)
<br>


## 4. Syslog
#### 참고자료
> [ELK on docker + Syslog](https://darranboyd.wordpress.com/2017/04/07/elk-syslog/)
>> - 환경: Ubuntu, Docker

> [시스템 관리하기 로그 - Visual Syslog(Linux, Windows](https://itswt.tistory.com/7)
>> Visual Syslog: Windows, Linux, Network 장비의 로그를 통합으로 관리하는 오픈소스 프로그램

> [[elastic] Get System Logs and Metrics into Elasticsearch with beats System Modules](https://www.elastic.co/kr/blog/get-system-logs-and-metrics-into-elasticsearch-with-beats-system-modules)
>> Beats 활용 (Filebeat, Metricbeat)

> [Elastic NMS Part 2: Syslog 원격로깅](https://www.sauru.so/blog/elastic-nms-part2-syslog-remote-logging/)
>> Logstash 활용, 필터링에 대한 설명 위주로 읽어보기
<br>


## 5. 기타
#### 참고자료
> [[SANS] Secirity Monitoring of Windows Containers](https://sansorg.egnyte.com/dl/ZWsWg89auj/?)
>> 전체적으로 한번 쭉 읽어보면 좋을 것 같음
