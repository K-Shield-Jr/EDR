# Windows 로그 수집 (Winlogbeat, Sysmon)
> 참고: [K-Shield-Jr/EDR/issues#1](https://github.com/K-Shield-Jr/EDR/issues/1)  
  

### 설치 환경
Windows 10 환경을 직원 PC로 가정하고, 로그 수집을 위한 설치를 진행하였다.  
```
- Windows 10 Home
- Winlogbeat 7.15.0
```  

## 1. 다운로드  

### Winlogbeat  
![image](https://user-images.githubusercontent.com/90135804/152747947-617ae067-01db-481d-bf64-43935b4c937a.png) [다운로드](https://www.elastic.co/kr/downloads/beats/winlogbeat)
→ 압축풀기  
![image](https://user-images.githubusercontent.com/90135804/153632062-3d7225f6-7417-499b-b1cb-30ea6fad87de.png)

### Sysmon  
![image](https://user-images.githubusercontent.com/90135804/152745669-1da13abc-dfb8-4489-b7de-2196df0f8d8e.png) [다운로드](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
→ 압축풀기  
![image](https://user-images.githubusercontent.com/90135804/153632027-e7543453-769b-4f14-9fe3-a2370ad2c66f.png)
<br>  


## 2. 설정
> 참고: [Winlogbeat Configuration Reference](https://www.elastic.co/guide/en/beats/winlogbeat/index.html)  

### Winlogbeat  
#### 1) `winlogbeat\winlogbeat.yml`  
> sysmon 데이터 전송을 위한 설정  
```yml
winlogbeat.event_logs:
    - name: Microsoft-Windows-Sysmon/Operational

output.logstash:
    # The Logstash hosts
    hosts: ["127.0.0.1:5044"]
    index: winlogbeat
```  

<details><summary>참고</summary>
<p>

- `winlogbeat.event_logs` 모니터링할 이벤트 로그 지정 (이벤트 로그 및 이벤트 로그와 연결할 정보 등을 정의)  
  - `name` 각 이벤트 로그의 필수 필드  
- `output.logstash` winlogbeat의 output으로 사용할 logstash 설정 (수집한 이벤트 로그를 logstash에게 보냄)  

</p>
</details>  

#### 2) 설정 적용  
```sh
PS> .\winlogbeat.exe -c .\winlogbeat.yml
```  

### Sysmon  
#### 1) sysmon configuration file (sysmon-modular)  
> Sysmon 구성에 대해 더 상세한 이벤트 로그 필드를 제공하는 sysmon-modular 파일을 다운로드 후 사용  

[다운로드](https://github.com/olafhartong/sysmon-modular/archive/refs/heads/master.zip)
→ 압축풀기 → `sysmonconfig.xml` 파일을 Sysmon 폴더에 저장  
![image](https://user-images.githubusercontent.com/90135804/153648570-5e412a33-4a76-4684-8f01-1a03c09f5312.png)

(룰 업데이트/사용을 위한 참고: Github - [Generating a config](https://github.com/olafhartong/sysmon-modular#customization), [Use](https://github.com/olafhartong/sysmon-modular#use))  
<br>  


## 3. 실행  

### Sysmon  
```sh
# .\sysmon.exe -i [sysmon설정파일.xml]
.\sysmon.exe -accepteula -i sysmonconfig.xml
```  
![image](https://user-images.githubusercontent.com/90135804/153649304-7ced325f-3605-48c5-817d-e8005dd21f07.png)


### Winlogbeat  
<details><summary><b>"보안 오류 - 스크립트 실행" 문제 해결법</b></summary>
<p>

  ```sh
  # 현재 정책 확인
  ExecutionPolicy
  
  # Restrected(스크립트 실행 불가)인 경우, 허용으로 변경
  Set-ExecutionPolicy Unrestricted
  
  # 현재 정책 확인
  ExecutionPolicy
  ```  
  ![image](https://user-images.githubusercontent.com/90135804/153645035-e94c34ac-1352-4688-94eb-5c12ed43b8f8.png)  
===
  
</p>
</details>  

```sh
# winlogbeat 서비스 설치 (스크립트 실행)
.\install-service-winlogbeat.ps1

# winlogbeat 실행
start-service winlogbeat

# winlogbeat 실행(Running) 상태 확인
get-service winlogbeat
```  
![image](https://user-images.githubusercontent.com/90135804/153650380-f68bee7d-2656-4de7-a1cb-43181bf43b96.png)

