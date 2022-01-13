# Winlogbeat 시각화(수정중)

참고 자료
<br> https://github.com/K-Shield-Jr/Research/issues/ <br> https://www.youtube.com/watch?v=Vv8nsG5q-Vs<br><br>


### 사이드바의 Analytics > Dashboard
### create new dashboard 클릭
![image](https://user-images.githubusercontent.com/51702223/148655320-10e5dac3-d850-4c4a-bf46-0a9225a5801f.png)   
       
<br><br>





## 1. 악성코드 추적 대시보드   

### Image
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


### Event_id
발생한 이벤트 수를 확인할 수 있는 Pie차트를 만든다.<br>
All Types 드롭박스 > Aggregation based > Pie 선택<br>
- Buckets: Split slices<br>
- Aggregation: Terms<br>
- Field: winlog.event_Id.Keyword<br>
- Size: 27 (27개의 이벤트 ID 이므로) https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon - 이벤트 id는 여기서 확인 가능

다음과 같이 event id를 pie로 만들었다.
![image](https://user-images.githubusercontent.com/51702223/148708087-7b3b7386-1ec3-41ed-84c0-bd924f423c57.png)



<br>
동일한 방법으로 다른 필드도 생성시킨다.

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

### ParentImage
![image](https://user-images.githubusercontent.com/51702223/148710654-61733430-52b6-4e55-b222-dc48fc5c5c6f.png)

### CommandLine
![image](https://user-images.githubusercontent.com/51702223/148711459-47997fd7-cb07-402c-9f73-d10633fdeb25.png)

### EventType
![18](https://user-images.githubusercontent.com/59687167/139570664-bfb1eb38-b7cf-45ec-8f53-ac0714d81953.PNG)

### ImageLoaded
![16](https://user-images.githubusercontent.com/59687167/139570050-13b75c35-e567-4f7a-92b9-3ad425407a3a.PNG)

### TargetFilename
![17](https://user-images.githubusercontent.com/59687167/139570220-818eb350-0edf-422a-b97a-ba3539953e48.PNG)

### TargetObject
![19](https://user-images.githubusercontent.com/59687167/139570669-54dd3e2b-3f8c-413e-91f3-c53ed009add7.PNG)

### 완성
![image](https://user-images.githubusercontent.com/51702223/148714486-0560d456-bce3-4e83-a8f2-35492185fd39.png)


## 레지스트리 확인 

```
Event ID 12 : RegistryEvent (Object create and delete) -> 레지스트리 오브젝트가 생성 또는 제거 되었을 때 발생
Event ID 13: RegistryEvent (Value Set) -> 레지스트리 값을 추가 했을 때 발생 (Set Value)
Event ID 14: RegistryEvent (Key and Value Rename) -> 이름이 변경된 키, 값의 새 이름을 기록
```
![20](https://user-images.githubusercontent.com/59687167/139570761-81b5f40b-965f-4b4a-87c1-1dfb7f0dc02d.PNG)
<br><br>EventType 파이 차트를 클릭해서 확인 가능하다.<br>
setValue 클릭하니 상단에 새 필터가 추가됨.<br> 레지스트리 오브젝트는 targetobject에 보인다.
![image](https://user-images.githubusercontent.com/51702223/148714887-f9cb2118-5cc9-4215-9bed-945281dc3de9.png)

어떤 값을 추가 했는지 확인하는 법은 Details에서 확인 가능(바이너리가 아닌 스트링일때 Details에서 확인 가능)
![23](https://user-images.githubusercontent.com/59687167/139571330-656f129b-3dbc-4d17-98a1-f399ed9f1446.PNG)


## 2. TSVB(Time Series Visual Builder) 그래프
###  TSVB 이용한 시간대 별 이벤트 개수 그래프
All Types 드롭박스 > TSVB 선택

그래프 하단에서 Data를 설정하면 시간에 따른 Event를 그래프로 볼 수 있다.<br> 
- Aggregation : count<br>
- Group by : Terms
- By : winlog.event_id.keyword
- Size: 27 
![image](https://user-images.githubusercontent.com/51702223/148717132-f2acb6c4-f2a5-4e23-9314-86028334cd31.png)

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

###  도착 IP(시간별)
TSVB 그래프를 하나 더 생성한다. 하단에서 Data를 설정하면 시간에 따른 도착 IP를 볼 수 있다.<br> 
- Aggregation : count<br>
- Group by : Terms
- By : winlog.event_data.DestinationIp.keyword
- Size: 10
![image](https://user-images.githubusercontent.com/51702223/148719389-91ee3a65-3033-4c6d-b48a-f59e2370de3b.png)


###  소스 IP(시간별)
TSVB 그래프를 하나 더 생성한다. 하단에서 Data를 설정하면 시간에 따른 소스 IP를 볼 수 있다.<br> \- Aggregation : count<br>
- Aggregation : count<br>
- Group by : Terms
- By : winlog.event_data.SourceIp.keyword
- Size: 10
![image](https://user-images.githubusercontent.com/51702223/148719667-08bea647-d0ea-4dda-b9ef-dad739e74300.png)

### 그래프 배치
![image](https://user-images.githubusercontent.com/51702223/148719778-926164a0-b949-4ebd-b455-7d5062fda993.png)


## 3. 네트워크 관련 대시보드
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



## 4. 통합 대시보드
![image](https://user-images.githubusercontent.com/51702223/148719973-ca640628-88dd-4943-b831-d545484a389f.png)

