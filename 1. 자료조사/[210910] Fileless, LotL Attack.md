# Fileless Attack
악성 파일을 저장시키지 않고 시스템 메모리에 바로 로드되어 실행하는 공격<br>
그렇기에 저장되어 있는 파일을 스캔하는 방식으로는 탐지하지 못한다.

# 공격 방법
1) 사용자의 웹 페이지 방문 유도
2) 플래시 로드
3) 플래시가 PowerShell 을 이용해서 특정 명령어 실행
4) 공격자가 C&C 서버에 접속해 스크립트를 다운받아 실행한다.
   암호화된 상태이기 때문에 트래픽 분석 방법으로는 탐지가 힘들다.

# 공격 탐지
동작 기반 탐지 도구를 사용하면 malware 가 침투한 이후에 탐지 가능하다.

참고:
https://blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=aepkoreanet&logNo=220934328454

# Living off the Land (LotL Attack)
대상 컴퓨터에 이미 설치된 도구를 사용하거나 간단한 스크립트와 쉘 코드를 메모리에서 직접 실행하는 공격이다.<br>
기존 보안 도구로 탐지될 가능성이 적으며 궁극적으로 공격 차단 위험이 줄어든다.

# 공격 방법
Fileless Attack 과 마찬가지로 플래시가 PowerShell 을 호출하고 stealth 명령 및 제어 서버에 연결하여 중요한 데이터를 찾아 공격자에게 보내는 악의적인 스크립트를 다운로드한다.

# 공격 탐지
화이트 리스트를 활용한다.<br>
서명된 스크립트만 허용하도록 PowerShell 실행 정책을 설정한다.<br>
모듈 로깅, PowerShell Transcription 에 대한 정책을 설정해 실행 중에 발생하는 작업의 보안을 높인다.

참고:
https://bankyy.tistory.com/entry/Living-off-the-LandLotL-기법의-바이너리-스크립트-등을-리스트업-하는-LOLBAS-프로젝트 <br>
https://www.itworld.co.kr/news/147078
