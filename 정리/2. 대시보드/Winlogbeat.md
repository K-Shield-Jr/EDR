# Winlogbeat 시각화(수정중)

참고 자료
<br> https://github.com/K-Shield-Jr/Research/issues/ <br> https://www.youtube.com/watch?v=Vv8nsG5q-Vs<br><br>


### 사이드바의 Analytics > Dashboard
### create new dashboard 클릭
![image](https://user-images.githubusercontent.com/51702223/148655320-10e5dac3-d850-4c4a-bf46-0a9225a5801f.png)   
       
<br><br>
## 1. 악성코드 추적 대시보드   
All Types 드롭박스 > Aggregation based > Data Table 선택
![image](https://user-images.githubusercontent.com/51702223/148655623-6224d4f0-6cba-4153-a98a-085db52c5316.png)   
  <br><br>    
         
       
- Buckets: Split rows<br>
- Aggregation: Terms<br>
- Field: winlog.event_data.Image.Keyword<br>
- Size: 15 

입력 후 Update 클릭 시 사진과 같은 표가 생성된다.<br>
Save and Return 버튼을 누르면 현재 표 저장 후 이전 화면으로 돌아간다.
<img width="694" alt="image만들기" src="https://user-images.githubusercontent.com/51702223/148655745-416023bf-c092-4b70-91e5-edef0d8ebfea.PNG">

[No Title] 영역을 누르면 세부 이름 설정이 가능하다.<br>
마우스 드래그를 통해 화면 배치 및 차트 크기를 조절 할 수 있다.<br>
Save를 눌러 대시보드를 저장한다.<br>
![image](https://user-images.githubusercontent.com/51702223/148707754-7ce455de-8581-47a9-ba0a-3169d8c376d6.png)
![10](https://user-images.githubusercontent.com/59687167/139569346-1eee608d-cc59-415b-ac56-1f425b79569d.PNG)

<br><br>
방금 한 것과 동일한 방법으로 다른 필드도 생성시킨다.<br>
발생한 이벤트 수를 확인할 수 있는 Pie차트를 만든다.<br>
All Types 드롭박스 > Aggregation based > Pie 선택<br>
- Buckets: Split slices<br>
- Aggregation: Terms<br>
- Field: winlog.event_Id.Keyword<br>
- Size: 27 (27개의 이벤트 ID 이므로) https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon - 이벤트 id는 여기서 확인 가능

다음과 같이 event id를 pie로 만들었다.
![image](https://user-images.githubusercontent.com/51702223/148708087-7b3b7386-1ec3-41ed-84c0-bd924f423c57.png)




```
Event_id : 발생한 모든 Event
Image : 프로세스 이름
Parentlmage : 부모 프로세스 이름
CommandLine : 프로세스 실행 시 입력한 인자
EventType : 이벤트 종류
ImageLoaded : 메모리에 로딩이 될 때 사용되는 이벤트
TargetFilename : 프로세스가 생성한 파일 이름
TargetObject : 프로세스가 생성한 레지스트리 오브젝트
```


![16](https://user-images.githubusercontent.com/59687167/139570050-13b75c35-e567-4f7a-92b9-3ad425407a3a.PNG)
Image loaded -> 프로세스에서 사용한 이미지가 메모리에 로딩이 될 때 사용되는 됨


![17](https://user-images.githubusercontent.com/59687167/139570220-818eb350-0edf-422a-b97a-ba3539953e48.PNG)
targetfilename -> 악성코드가 생성한 파일 이름



![18](https://user-images.githubusercontent.com/59687167/139570664-bfb1eb38-b7cf-45ec-8f53-ac0714d81953.PNG)
![19](https://user-images.githubusercontent.com/59687167/139570669-54dd3e2b-3f8c-413e-91f3-c53ed009add7.PNG)
레지스트리 관련 부분
```
Event ID 12 : RegistryEvent (Object create and delete) -> 레지스트리 오브젝트가 생성 또는 제거 되었을 때 발생
Event ID 13: RegistryEvent (Value Set) -> 레지스트리 값을 추가 했을 때 발생 (Set Value)
Event ID 14: RegistryEvent (Key and Value Rename) -> 이름이 변경된 키, 값의 새 이름을 기록
```
![20](https://user-images.githubusercontent.com/59687167/139570761-81b5f40b-965f-4b4a-87c1-1dfb7f0dc02d.PNG)
EventType 파이 차트를 클릭해서 확인 가능하다.
![21](https://user-images.githubusercontent.com/59687167/139570913-7c2755c9-1be7-407c-b87f-82760db6e682.PNG)
![22](https://user-images.githubusercontent.com/59687167/139570914-37d2e0f9-19d3-4f0e-be19-d884511c0e72.PNG)
어떤 값을 추가 했는지 확인하는 법은
![23](https://user-images.githubusercontent.com/59687167/139571330-656f129b-3dbc-4d17-98a1-f399ed9f1446.PNG)
Details에서 확인 가능(바이너리가 아닌 스트링일때 Details에서 확인 가능)


네트워크 관련
![24](https://user-images.githubusercontent.com/59687167/139571876-9a8d3d5b-5882-46f8-a263-c96fefc3df27.PNG)
포트
![26](https://user-images.githubusercontent.com/59687167/139571894-fe86c95e-805f-406e-8404-e7fca5a53f9a.PNG)
프로토콜
![27](https://user-images.githubusercontent.com/59687167/139571907-37a4da17-56e1-4668-9f37-ab7e1086e1a3.PNG)
ip
![28](https://user-images.githubusercontent.com/59687167/139571921-bad8c9c6-5a94-43b7-ad30-d852ffb4ba64.PNG)
![29](https://user-images.githubusercontent.com/59687167/139571930-a447cf36-617d-4763-a52b-dbd2b47c7a34.PNG)
dns

![31](https://user-images.githubusercontent.com/59687167/139577098-27ba43da-5131-48c9-8a0d-d0fa54433032.PNG)
TSVB를 이용해서 시간대 별 이벤트 개수 그래프

```
이벤트 ID
1 : 프로세스 생성
2 : 프로세스가 파일 생성시간을 변경
3 : 네트워크 연결
4 : Sysmon 서비스 상태 변경
5 : 프로세스 종료
6 : 드라이버 로드
7 : 이미지 로드 (“-l” 옵션으로 활성화 필요)
8 : 다른 프로세스에서 스레드 생성
9 : RawAccessRead 탐지 (프로세스가 “\\.\” 표기법 이용하여 드라이브 접근시)
10 : 특정 프로세스가 다른 프로세스 Access (ex. Mimikatz > lsass.exe)
11 : 파일 생성 (생성 및 덮어쓰기)
12 : 레지스트리 이벤트 (키 값 생성 및 삭제)
13 : 레지스트리 이벤트 (DWORD 및 QWORD 유형의 레지스트리 값 수정)
14 : 레지스트리 이벤트 (키 및 값 이름 변경)
15 : FileCreateStreamHash (파일 다운로드와 관련된 Zone.identifier 확인)
17 : PipeEvent (파이프 생성)
18 : PipeEvent (파이프 연결)
19 : WmiEventFilter 활동이 감지 됨 (악성코드 실행시 사용될 가능성 있음)
20 : WmiEventConsumer 활동이 감지 됨
21 : WmiEventConsumerToFilter 활동이 감지 됨
255 : Sysmon 오류
```

