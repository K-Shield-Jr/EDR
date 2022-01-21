사용가능한 yara 오픈소스
===========
   
## 1. 기본 야라 규칙
- https://github.com/Yara-Rules/rules
- ver 3.0 이상 부터 가능   
- 분야
  - Anti-debug/Anti-VM
  -  Capabilities
  -  CVE Rules
  -  Crypto
  -  Exploit kits
  -  Malicious Documents
  -  Malware
  -  Packers
  -  WebShells
  -  Email
  -  Malware Mobile
  -  Deprecated   
- 각 분야에 해당되는 내용에 대한 rule set이 작성되어 있다.

## 2. awesome-yara
- https://github.com/InQuest/awesome-yara#tools
- 규칙, 도구 등의 리스트 적어둔 곳 => 굉장히 많음
### 2-1) sysmon-edr
- https://github.com/ion-storm/sysmon-edr
- yara 도구 리스트에 있는 것
- China Chopper이라는 웹셀 공격에 대한 도구 같음 
- yara 스캐닝 & 감지 => 응답 해주는 기능 가지고 있음 

### 3.ReversingLabs YARA 규칙
- https://github.com/reversinglabs/reversinglabs-yara-rules
- 각 공격에 대한 rule set이 작성
  - certificate
  - downloader
  - exploit
  - infostealer
  - pua
  - ransomware
  - trojan
  - virus

### 4. yextent
: Yara 통합 소프트웨어
- https://github.com/BayshoreNetworks/yextend
- 아카이브된 데이터들을 세부적으로 처리하기 위해서 나온 yara의 확장 버전
- YARA를 사용하여 압축 파일(.zip, .tar 등)을 스캔할 때 유용하다.
=> YARA의 확장판이라 하는데, 공식 사이트 외에는 잘 안 뜨는 걸 보면 사용을 잘 안하는 걸까요..? ㅠ
