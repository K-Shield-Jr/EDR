

# APT 공격 - EDR 탐지 시스템

· 프로젝트 기간 	: 2021.08.31 - 2021.11.17

· 프로젝트 멘토님 	: 올잇원 최진원 멘토님

· 프로젝트 수행 팀 	:

  - (PM)김은주, 
  
  - (APT팀 PL)이찬진, 김가영, 김지예, 장민경
  
  - (EDR팀 PL)이안나, 박민주, 안병휘, 이유림, 정민지
  

                      
</br>   

## 프로젝트 배경

![image](https://user-images.githubusercontent.com/37640219/185892058-a32cb685-a71d-434c-a171-ecf01534662e.png)


APT 공격은 점점 증가하는 추세이고 이에 대한 대응면에서의 필요성이 대두되고 있다. 

파일이 실행될 때의 행위를 통해 악성코드 여부를 판단하는 APT 대응 솔루션과 다르게, 

악성코드 활동이 실제로 일어나는 엔드포인트 단에서 행위 정보를 수집, 악성코드를 분석하는 방법인 EDR이 새로운 대안으로 제시되고 있다. 

이를 바탕으로 APT 공격을 실제로 수행해보고 대응 방안인 EDR을 구현해보는 프로젝트를 고안해보게 되었다. 


</br>

## 프로젝트 목표

 - <b>APT 공격</b>

    - APT 모사 시스템을 구성하여 피해자에게 악성코드를 유포한 다음 중요 데이터를 탈취하는 시나리오를 구성 후 공격 실행

 - <b>EDR 탐지</b>
 
    -	피해자 엔드포인트에서 특정 이벤트에 대한 로그를 수집 후, 분석하여 악성행위로 판단되면 관리자에게 경고 알림 전송 및 대응
  
    -	시스템 내 주요 서버들의 로그를 수집하여 실시간 모니터링 및 침해사고 발생 시, 사고 경위 조사
  
    -	대시보드를 통한 모니터링 결과 시각화, 관리자에게 경고 리포트 전송 등으로 관리자들이 관제실에 있지 않아도 원격지에서 공격 현황을 파악할 수 있는 환경 구성
    
</br>

## 프로젝트 환경

![image](https://user-images.githubusercontent.com/37640219/185893867-b44c55f3-18c3-405c-a2ed-351cf46be493.png)

</br>

## EDR 서버 구성

![image](https://user-images.githubusercontent.com/37640219/185894322-77e01a73-e2b9-41ff-a2f2-20663ce5a82d.png)

-	각각 로그 수집 대상 서버(API 서버)는 Windows 10과 Linux(Ubuntu 20.04), 로그 저장소 서버는 Docker로 구성

-	윈도우 환경에서의 로그 전송은 로그 수신기 Winlogbeat 이용

-	리눅스 환경에서의 로그 전송은 로그 수신기 Filebeat 이용

-	ELK 서버 포트는 Logstash는 5000, Elastic search는 9200, Kibana는 5601로 설정

-	ELK 서버는 Docker-compose를 이용해 빌드 함

-	EDR 관련 자료는 https://github.com/K-Shield-Jr/Research/issues 참고


</br>

## 분석 결과

</br>

### 침투 타임라인

![image](https://user-images.githubusercontent.com/37640219/185894841-5438894f-7cb9-454c-9572-5fb886847a71.png)

</br>

### 상세 분석


 #### 1. 암호화된 문서파일 발견
 
 ![image](https://user-images.githubusercontent.com/37640219/185896589-c15b3b40-b170-4678-9fc9-abaaee194651.png)

- (18:35분 경) 문서 파일이 Lock.exe에게 암호화 되었음

![image](https://user-images.githubusercontent.com/37640219/185896668-1abe605c-d5e6-4a6f-8a91-14ca5d9ef284.png)
 
- Lock.exe의 PID(=1536)와 1536의 PID인 PPID(4244)확인

![image](https://user-images.githubusercontent.com/37640219/185896854-2f279f3b-2b79-4b24-a765-33a309e53007.png)

- (18:30분 경) PID 1536으로 검색했을 때 Explorer.exe가 Lock.exe와 관련됨을 확인

![image](https://user-images.githubusercontent.com/37640219/185896909-e73abf10-cb27-430f-a731-99f80cf376b2.png)

- Lock.exe 좀 더 자세하게 분석

· PID : 5604, 7152, 664, 8784

· PPID : 1268, 4244, 664, 8784

=> 이 중 664, 8786를 제외하고 부모 프로세스는 1268, 4244(Explorer.exe)

![image](https://user-images.githubusercontent.com/37640219/185896944-a31bda55-958a-4879-b950-3ec4612be25c.png)

- (18:15경) 1268은 cmd.exe였고 부모 프로세스는 rundll2.exe이다.
 
![image](https://user-images.githubusercontent.com/37640219/185896988-9c497242-8559-46a5-b6d0-ef2ad93f2ff6.png)

- rundll2.exe의 부모 프로세스는 explorer.exe이라는 것을 알 수 있고

C:\Users\DDJ\Desktop\Lock.exe와 C:\Users\DDJ\Downloads\Lock.exe이 관련됨을 알 수 있음
 
![image](https://user-images.githubusercontent.com/37640219/185897044-19b7c832-31f0-4fe4-a859-14be60bf910e.png)

- (18:15경) rundll2.exe는 네트워크 연결을 한 것이 확인됨

· 소스ip : 172.30.1.35   	

· 도착ip : 172.30.1.25 

· 도착 포트 : 443  	

· 소스 포트 : 50804	

· TCP 연결

 
 #### 2. Explore.exe 분석

![image](https://user-images.githubusercontent.com/37640219/185897105-f5cebbbc-18ce-4d31-92db-e732a7f61e45.png)

- (15:47경) C:\Windows\Explorer.EXE(4244)

-> C:\Users\DDJ\Downloads\office\RunMe.bat 생성
 
![image](https://user-images.githubusercontent.com/37640219/185897143-536d8db2-5b14-4b2b-a33e-466bef6dd5bb.png)
 
- (15:48경) C:\Windows\System32\cmd.exe(7452)가 “C:\Windows\system32\cmd.exe /c” "C:\Users\DDJ\Downloads\office\RunMe.bat" 명령어를 통해 RunMe.bat를 실행

- C:\Users\DDJ\AppData\Local\Temp\getadmin.vbs 생성됨

![image](https://user-images.githubusercontent.com/37640219/185897179-2a226ea3-3ca3-4552-bcf6-94f9164b18e2.png)

- (15:48경) C:\Windows\system32\cmd.exe(7452)가
HKU\S-1-5-21-1908149527-2608072508-3932278328-1002\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FileExts.vbs 접근
(CurrentVersion\Explorer : IE나 Explorer가 실행될때마다 등록된 DLL 실행함) 

-> 악성코드의 지속성으로 예상된다.

![image](https://user-images.githubusercontent.com/37640219/185897210-94128cc5-1a56-4b25-93bd-c0a74f98cd7a.png)

- (15:48경) C:\Windows\System32\wscript.exe(4700)가 "C:\Windows\System32\WScript.exe" "C:\Users\DDJ\AppData\Local\Temp\getadmin.vbs" 명령어를 통해 getadmin.vbs를 실행
(wscript.exe의 부모 프로세스는 C:\Windows\System32\cmd.exe)
 
![image](https://user-images.githubusercontent.com/37640219/185897274-698db97e-ae44-4094-af3c-76dff39088cd.png)
 
- (15:48경) C:\Windows\System32\cmd.exe(2184) -> cmd /u /c echo Set UAC = CreateObject("Shell.Application") : UAC.ShellExecute "cmd.exe"

-> 배치 파일 관리자 실행을 권유하지 않고 강제로 관리자로 실행시키는 코드

 
 #### 3. 경로 순회 공격 발견

![image](https://user-images.githubusercontent.com/37640219/185897305-e9338abf-0fef-45c3-8385-3d7ab0c831a1.png)

- (16:20경) 경로순회공격 확인

![image](https://user-images.githubusercontent.com/37640219/185897361-e4e3acfd-4bdd-4c6f-a7c6-be7e1f1886e4.png)
 
- C:\Windows\SysWOW64\rundll32.exe 부모 프로세스 추적

![image](https://user-images.githubusercontent.com/37640219/185897401-0517d28a-cc2b-4340-bcc0-81d3a588bbcb.png)

- 3772 -> C:\Program Files (x86)\Microsoft Office\root\Office16\WINWORD.EXE
 
- 7332 -> C:\Program Files (x86)\Microsoft Office\root\Office16\WINWORD.EXE
 
- 9800 -> C:\Program Files (x86)\Microsoft Office\root\Office16\WINWORD.EXE

-> 악성코드는 WINWORD.exe와 관계 되어 있음을 확인 가능

