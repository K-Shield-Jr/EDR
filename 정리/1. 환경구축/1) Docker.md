# Docker 설치

```
OS      : Windows 10
개발환경 : Docker 20.10.8, ELK 7.15.0
```
---

도커 설치를 위해 [잘 정리된 글](https://www.lainyzine.com/ko/article/a-complete-guide-to-how-to-install-docker-desktop-on-windows-10/)을 참고하였다.  
<br>

## 1. 사전 준비

### 1-1. 버전 확인 (Windows 10 Home vs Pro Edition)

먼저 Windows 10에서 Docker를 설치하기에 앞서, 본인이 사용 중인 Windows가 어떤 에디션인지 확인할 필요가 있다.  

<kbd><img src="https://user-images.githubusercontent.com/90135804/147986920-55786f01-67a9-48df-8c7f-0446960dedd5.png" width="400" height="130" /></kbd>  

나는 Windows 10 Home Edition을 사용중이다.  

Windows 10 Home과 Pro Edition에서의 차이는 **Hyper-V 기능의 지원 여부**이다.

<details><summary><b>CLICK! (Hyper-V 기능)</b></summary>
<p>

| Windows 10 Home | Windows 10 Pro |
| :---: | :---: |
| 지원 X | 지원 O |

2020년 5월부로 **Windows 10 May 2020 Update(20H1)** 업데이트가 릴리스되면서 WSL2가 정식 릴리스 되었다. 
현재 Windows 10 Home에서도 WSL2를 사용할 수 있으며 WSL2를 기반으로 Docker Desktop을 사용할 수 있다.

| Windows 10 Pro | Windows 10 Home |
| --- | --- |
| - Hyper-V 기반 Docker Engine 사용 가능<br>- WSL2 기반 Docker Engine 사용 가능 | - WSL2 기반 Docker Engine 사용 가능 |

</p>
</details>
<br>

### 1-2. WSL2 설치
> **WSL2 (Windows Subsystem for Linux 2)** : 윈도우에서 리눅스를 사용하게 해주는 기능
### Windows Terminal 설치

우선 Windows Terminal을 사용하여 WSL2를 설치할 것이므로 Windows Terminal 먼저 설치해준다. ( [여기](https://www.lainyzine.com/ko/article/how-to-install-windows-terminal-powershell-wsl2/)를 참고 )  
참고한 문서에서는 기본적으로 **Windows Terminal 정식 버전은 Microsoft Store나 `winget` 명령어로 설치**하는 것을 추천하고 있다.

나는 powershell에서 `winget` 명령어를 통해 설치하는 방법으로 진행했다.

```powershell
winget install --id=Microsoft.WindowsTerminal -e
```

<img src="https://user-images.githubusercontent.com/90135804/147989829-36266289-42d5-4cd8-b3c8-37fcd2a4ecaa.png" width="800" height="250" />  

아래와 같이 설치가 완료된 Windows Terminal을 확인할 수 있다.
![image](https://user-images.githubusercontent.com/90135804/147990620-a3dab2ff-8fce-49c1-b3ae-76b6b9657c7f.png)  

<details><summary><b>터미널 컬러 테마 설정 (Customizing)</b></summary>
<p>

![image](https://user-images.githubusercontent.com/90135804/147990765-24cd121c-b857-4051-a538-ebb300c551f4.png)  
여기서 터미널의 컬러 테마를 커스터마이징 할 수 있다.  
직접 하나하나 설정하는 것도 좋지만, 만들어져있는 예쁜 테마를 가져오는 게 편하다.  
설정 정보를 담은 `settings.json` 파일을 수정하여 변경할 수 있다.  

1. [>_TerminalSplash - Windows Terminal Themes](https://terminalsplash.com/)에서 원하는 테마를 고른다.  
2. 테마를 추가하기 위한 코드 { }를 Copy한다.  
3. JSON 설정 파일에서 `"schemes" : [ ]` 부분을 찾아서 해당 위치에 붙여넣는다.  
   (마지막에 꼭 , 도 추가해준다.)  
4. `settings.json` 파일 수정사항을 저장한다.

</p>
</details>
<br>

#### DISM으로 WSL 관련 기능 활성화

우선 가상 터미널을 **관리자 권한으로 실행**한 뒤(Windows Terminal 검색 후 마우스 오른쪽 버튼), DISM(배포 이미지 서비스 및 관리) 명령어로 Microsoft-Windows-Subsystem-Linux(WSL) 기능을 활성화한다.

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

다음으로 dism 명령어로 VirtualMachinePlatform 기능을 활성화한다.  

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```  

![image](https://user-images.githubusercontent.com/90135804/147991312-0455cbf9-b8aa-4b1e-957d-152a87cfa76c.png)  

정상적으로 작업이 완료되었다는 메시지를 확인 후, 재부팅을 한 번 해준다.  

### WSL2 Linux 커널 업데이트

다음으로 WSL2 Linux 커널 업데이트를 진행한다.  

**x64 머신용 최신 WSL2 Linux 커널 업데이트 패키지** [다운로드](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) 후, 아래 안내에 따라 설치한다.  

- [Windows 10에 WSL 설치 | Microsoft Docs](https://docs.microsoft.com/ko-kr/windows/wsl/install#step-4---download-the-linux-kernel-update-package)

(사실 패키지 다운로드 후, 실행만 하면 되는 거라 아주 간단하다)  

이후 다시 터미널을 열어서 다음 명령어를 통해 기본적으로 사용할 WSL 버전을 2로 변경한다.  

```powershell
wsl --set-default-version 2
```  

![image](https://user-images.githubusercontent.com/90135804/147991376-f9d76574-0396-4b9d-8ce0-d1807dfe658d.png)  

WSL2로 리눅스를 사용하고자 하는 경우, 리눅스 배포판을 설치하는 등 [추가 설정](https://www.lainyzine.com/ko/article/how-to-install-wsl2-and-use-linux-on-windows-10/)이 필요하지만, Docker만 사용하는 경우 여기까지만 셋업하면 된다. 그럼 이제 Docker Desktop을 설치해보자.  

## 2. Docker Desktop 설치

### 2-1. Docker Desktop 다운로드

아래 링크로 들어가서, Download for Windows를 클릭하여 설치한다.  

- [Docker Desktop for Mac and Windows | Docker](https://www.docker.com/products/docker-desktop)  

아래와 같은 설치 실행파일이 다운받아지는데, 실행 후 안내에 따라서 설치를 진행한다.  

![image](https://user-images.githubusercontent.com/90135804/147991418-e433bb0e-43bb-4f9b-b3a2-4ea71da0dbfa.png)  

중간에 Configuration이 나타나는데, 둘 다 체크하고 설치를 진행한다.  

- 옵션1: WSL 관련 옵션
- 바탕화면에 아이콘을 추가할지 여부

![image](https://user-images.githubusercontent.com/90135804/147991447-b605c40a-f618-4c27-b37f-8dadcd528573.png)  

설치가 진행되면 완료될 때까지 기다린다. (몇 분 정도 시간이 걸림)  
참고로 용량을 꽤 차지하므로, 미리 하드디스크 여유 공간을 확보해두는 게 좋다.  
![image](https://user-images.githubusercontent.com/90135804/147991479-a63fdca5-4182-4d07-bc14-01bc1e5c3c12.png)  

설치 완료. 시스템 상태에 따라 재시작이나 로그아웃을 해야하는 경우도 있다.  
![image](https://user-images.githubusercontent.com/90135804/147991498-3f840746-48c8-4acf-b158-c62a2abaeb7b.png)  

이제 바탕화면의 Docker Desktop 아이콘이나 Docker Desktop 검색으로 실행할 수 있다.  
Docker Desktop을 실행한다.  
![image](https://user-images.githubusercontent.com/90135804/147991517-9a504687-92b5-4c2a-9554-f07b194f6adb.png)  

만약 아래와 같은 약관 동의가 뜬다면, 동의하고 진행한다.  
![image](https://user-images.githubusercontent.com/90135804/147991532-eb8f9bd2-811a-4efe-8269-38dc1831b750.png)  


### 2-2. Docker Desktop 설정

### Docker Desktop 실행
![image](https://user-images.githubusercontent.com/90135804/147991549-d54e0b82-fedb-412a-875e-82064584e79a.png)  

시스템에 WSL이 활성화되어 있다면, Docker는 기본적으로 WSL2를 백엔드로 Docker Engine을 실행한다. 초기 셋업에는 몇 분 정도의 시간이 걸린다. 성공적으로 Docker가 실행되면 Tutorial이 나타난다.  
![image](https://user-images.githubusercontent.com/90135804/147991574-5ad9364a-a1fc-4bc8-bd8a-d46a973d6ced.png)  


### Docker Desktop 관련 버전 확인
Docker Desktop은 시스템 트레이에 숨겨져 있다. 숨겨진 아이콘을 활성화하고, 고래 모양 아이콘에서 오른쪽 버튼을 누르면 Docker Desktop의 상태를 관리할 수 있다.  

**About Docker Desktop** 클릭!  
![image](https://user-images.githubusercontent.com/90135804/147991592-68a2ca4e-50f8-4382-abde-5f84b1926ee3.png)  

현재 설치된 Docker Desktop 관련 도구들의 버전을 여기서 확인할 수 있다.  
![image](https://user-images.githubusercontent.com/90135804/147991626-bc7504be-26f8-4573-9280-8d473fec2612.png)  


### WSL 통합 설정
우선, WSL2 설정이 잘 되어있는지 확인하고 WSL 통합 설정을 진행한다.  
Docker 아이콘에서 오른쪽 버튼을 눌러 Settings를 선택한다.  

#### 1) General 설정  
'Use the WSL 2 based engine'에 체크가 되어 있는지 확인한다. 미리 체크가 되어있겠지만, 혹시 되어 있지 않다면 체크 후 오른쪽 아래의 Apply & Restart 버튼을 클릭한다. (클릭하면 시스템이 재부팅된다.)  
![image](https://user-images.githubusercontent.com/90135804/147991673-8f8fb597-bd22-4ffc-ab08-bfba014b6bdf.png)  


#### 2) Resources > WSL Integration  
'Enable Integration with my default WSL distro'에 체크가 되어있는지 확인한다. 체크가 되어 있지 않다면 체크 후 오른쪽 아래 Appy & Restart 버튼을 클릭해주면 도커 엔진이 재실행된다.  
(Ubuntu 관련 부분은, 내가 설치를 따라한 블로그에서는 없는 옵션이었는데 나한테는 있길래 어차피 Ubuntu 쓸 거니까 그냥 활성화했다.)  
![image](https://user-images.githubusercontent.com/90135804/147991697-2fe4a2a0-a4e5-4dbf-9f51-d78b08b19045.png)  


## 3. Docker Desktop 설치 확인 + 간단한 nginx 서버 예제 실행  
### Docker Desktop 설치 확인
Windows Terminal을 열어서 간단한 테스트를 해볼 것이다. PowerShell 탭을 하나 열고 wsl 명령어로 Docker 전용 머신이 실행중인 것을 확인할 수 있다.  

```powershell
wsl -l -v
```

흠.. 나는 왜 블로그랑은 달리 Ubuntu에 *로 체크가 되어 있는지 모르겠지만, 일단 Docker 전용 머신이 실행중인 것을 확인할 수 있다.  
![image](https://user-images.githubusercontent.com/90135804/147991733-a61c9d94-4a59-46c2-89ae-79cf08e5c627.png)  

wsl로 docker-desktop 리눅스에 명령어를 실행해볼 수 있다. docker-desktop은 BusyBox 기반의 경량 리눅스인 것을 확인해볼 수 있다.  

```powershell
wsl -d docker-desktop busybox
```  
![image](https://user-images.githubusercontent.com/90135804/147991753-8dce9910-9113-4f0b-8e66-5c9fb0c12987.png)  

docker version 명령으로 Docker 서버와 클라이언트 정보를 확인해본다.  

```powershell
docker version
```  
![image](https://user-images.githubusercontent.com/90135804/147991898-92afa149-2c46-4a8b-b833-e2981d84aa39.png)  

docker ps로 실행중인 컨테이너를 확인해본다.  

```powershell
docker ps
```  
![image](https://user-images.githubusercontent.com/90135804/147991932-1845f9ee-9dca-400c-8105-3185ad8df8ef.png)  
아직 아무것도 안 했으니까 당연히 아무것도 안 뜬다. (tutorial을 실행했다면 tutorial이 뜰 수도 있다.)  


### nginx 이미지 - 간단한 서버 테스트
nginx 이미지로 간단한 서버 테스트를 해볼 것이다.  
먼저 웹 브라우저를 열어서 127.0.0.1:4567에 접속해본다. 다음과 같이 사이트에 접속할 수 없는 상태임을 확인할 수 있다.  
![image](https://user-images.githubusercontent.com/90135804/147991963-8dbe1157-8c56-4fc5-8ec0-2f49010d3c5b.png)  

docker run 명령어로 nginx 이미지 기반 컨테이너를 하나 실행해본다.  

```powershell
docker run -p 4567:80 -d nginx:latest
```  
![image](https://user-images.githubusercontent.com/90135804/147991975-34010e95-b15e-41d2-a8db-875a121953d2.png)  

Docker에서는 이미지를 자동으로 다운받고 실행해준다. docker ps 명령어로 실행한 컨테이너를 확인한다.  

```powershell
docker ps
```  
![image](https://user-images.githubusercontent.com/90135804/147991993-2ecb0485-a80f-43a3-af32-fecfb5794d79.png)  

다시 웹 브라우저에서 아까 접속했던 127.0.0.1:4567에 접속해보면, 이제 'Welcom to nginx!' 메시지가 나타난다.  
![image](https://user-images.githubusercontent.com/90135804/147992023-31b27487-0c1c-46ad-9921-102de6642fe0.png)  

사용하지 않는 컨테이너는 docker rm 명령어로 삭제해준다.  

docker ps 명령어를 통해 컨테이너 ID를 알아낸 후, 해당 컨테이너 ID를 삭제해주면 된다.

```powershell
docker ps

docker rm -f 6edda18b6cfc
```  
![image](https://user-images.githubusercontent.com/90135804/147992043-d4b63967-e20a-4949-86f1-5d00beba8b00.png)  


이제 즐겁게 Docker를 활용하면 된다!! 관련 Docker 기능과 팁은 아래에서 참고할 수 있다.  
- [Docker 글 목록 | LainyZine](https://www.lainyzine.com/ko/subject/docker/)  

