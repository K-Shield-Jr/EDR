# Fileless Attack

<b> 1. Code Injection </b>

 - Shellcode injections
    
    합법 프로세스 사이에 악성 프로세스 삽입

 - DLL injections
    
    프로세스에 실행할 DLL 경로를 삽입

 - Reflective DLL injections
    
    Shellcode 삽입과 DLL 삽입을 혼합한 공격

 - Process hollowing
    
    suspended 상태에서 합법 프로세스 생성 후 해당 프로세스 메모리해제, 악성코드로 대체

 - Memory modules

    인젝터 또는 로더가 DLL을 메모리에 매핑

    (Reflective DLL injections과 유사)

 - Module overwriting

    사용하지 않는 모듈을 프로세스에 매핑하고 악성 코드를 오버라이팅함

 - Gargoyle

    Read/Write only 메모리에서 코드를 실행할 수 있게 함

 - Process Doppelganging

    NTFS 트랜잭션을 사용해 트랜잭션 파일에 악성 프로세스(페이로드)를 배포하여 합법적인 프로세스처럼 보이도록 함

</br>

### 방법 1.

Python기반 메모리 포렌식 도구 Volatility를 사용한다. (vol.py)

> https://github.com/volatilityfoundation/volatility

</br>

Volatility 플러그인
 - pstree : 부모/자식 프로세스를 트리 구조로 확인
 - pslist : 시스템에서 사용중인 프로세스 리스트 (리스트 워킹, 가상 주소)
 - psscan : 이미 종료된 프로세스와 루트킷에 의한 hidden/unlinked 프로세스 확인 (패턴 매칭, 물리적 주소)
 - psxview : 프로세스 은닉을 탐지하기 위해 다양한 방식으로 프로세스를 조회
 - procdump : 프로세스 실행 파일 추출
 - memdump : 프로세스가 사용한 전체 메모리 영역 덤프
 - filescan : 메모리 상의 파일 오브젝트 전체 검색 (패턴 매칭)
 - hivelist : 메모리 상의 파일
 - cmdscan : cmd에서 실행한 명령어 확인
 - cmdline : cmd에서 실행한 명령어 이력 확인
 - netscan : 네트워크 연결 확인
 - malfind : 시그니처 패턴 정보

> 그 외 플러그인
> https://github.com/volatilityfoundation/volatility/wiki/Command-Reference

</br>

### 사용 예제

 - Reflective DLL injections


 (1) 악성 세션이 기존 프로세스에 삽입되었는지 검사
``` c
// ex) Metasploit 도구를 통해 침입한 Meterpreter shell 감지
vol.py netscan
```

 (2) 코드 삽입 발생 감지
``` c
// VAD TAG : PAGE_EXECUTE_READWRITE
// 메모리 조작 발생을 의미하는 태그 확인
vol.py malfind -p [PID]
```

 (3) 악성 프로세스 덤프
``` c
vol.py vaddump -p [PID] -b [MemoryAddress] -D dump/
```

 (4) 덤핑 파일 스캔 작업 수행

</br>

 - Process hollowing

 (1) 프로세스 부모-자식 일치 여부 확인
``` c
// ex) "svchost.exe"의 기본 상위 프로세스는 "services.exe"
vol.py pslist | grep -i [검색할 프로세스 명]

// 해당 [PID]가 올바른 상위 프로세스임을 확인
vol.py pslist -p [PID]
```

 (2) 이전 프로세스가 연결 해제됨을 확인
``` c
// VAD TAG : PAGE_EXECUTE_READWRITE
// 메모리 조작 발생을 의미하는 태그 확인
vol.py malfind -p [PID]
```

 (3) 악성 프로세스 덤프
``` c
//실행된 악성 프로세스 확인
vol.py shimcachemem

// 이전 작업에서 PID, 메모리 주소 확인 후 dumping
vol.py procdump -p [PID] -D dump/ 
vol.py vaddump -p [PID] -b [MemoryAddress] -D dump/ 
```

 (4) 덤핑 파일 스캔 작업 수행
```
덤핑한 프로세스 파일에서 Detection 작업 수행
(Virus Total에서 검사 가능)
```

참고
> Volatility를 이용해 제작한 DLL Injection Detection (Python)
> https://github.com/Soterball/DLLInjectionDetection

```
Volatility를 이용한 분석을 실행하기 위해서는 미리 캡쳐하여 수집된 RAM 메모리 데이터가 존재해야 함
```

</br>


### 방법 2.

API 호출 분석

이벤트 간의 연관 관계를 확인하고 Sysmon log의 실행 순서 추적한다.

공격을 탐지하기 위해 다음 함수 실행을 확인

 - Process Injection
``` c
CreateRemoteThread
SuspendThread
SetThreadContext
ResumeThread
QueueUserAPC
NtQueueApcThread

//메모리 수정 작업
VirtualAllocEx
WriteProcessMemory
```

 - Process Injection: Process Hollowing
```
CreateRemoteThread
SuspendThread
SetThreadContext
ResumeThread
VirtualAllocEx
WriteProcessMemory
```

 - Process Injection: DLL Injection
```
CreateRemoteThread
VirtualAllocEx
WriteProcessMemory
```

 - Process Injection: Process Doppelgänging
 ```
 CreateTransaction
 CreateFileTransacted
 RollbackTransaction
 NtCreateProcessEx 
 NtCreateThreadEx 
 WriteProcessMemory
 ```

### API

API 함수의 가장 낮은 계층에 위치한 래퍼 API 함수에 Hook을 설치하여 모니터링

탐지하고자 하는 특정 유형의 이벤트를 지정해 모니터링 할 수 있도록 한다.

> Win32 Api</br>
> https://docs.microsoft.com/en-us/windows/win32/api/

 - 후크 엔진

    Detours (C++)
    https://github.com/microsoft/Detours
    
    nthookengine
    https://ntcore.com/files/nthookengine.htm


1. API를 후킹하는 DLL을 작성

2. 해당 DLL을 인젝션하여 실행

 - DLL injection sample code

``` c
#include <windows.h>

HANDLE inject_DLL(const char* file_name, int PID)
{
    HANDLE h_process = OpenProcess(PROCESS_ALL_ACCESS, FALSE, PID);                   
    //retrieving a handle to the target process

    char fullDLLPath[_MAX_PATH];                                                      
    //getting the full path of the dll file
    GetFullPathName(file_name, _MAX_PATH, fullDLLPath, NULL);

    LPVOID DLLPath_addr = VirtualAllocEx(h_process, NULL, _MAX_PATH,
                          MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);                  
                          //allocating memory in the target process
    WriteProcessMemory(h_process, DLLPath_addr, fullDLLPath,
                       strlen(fullDLLPath), NULL);                                    
                       //writing the dll path into that memory

    LPVOID LoadLib_addr = GetProcAddress(GetModuleHandle("Kernel32"),                 
    //getting LoadLibraryA address (same across
                                         "LoadLibraryA");                             
                                         //  all processes) to start execution at it

    HANDLE h_rThread = CreateRemoteThread(h_process, NULL, 0,                         
    //starting a remote execution thread at LoadLibraryA
                       (LPTHREAD_START_ROUTINE)LoadLib_addr, DLLPath_addr, 0, NULL);  
                       //  and passing the dll path as an argument

    WaitForSingleObject(h_rThread, INFINITE);                                         
    //waiting for it to be finished

    DWORD exit_code;
    GetExitCodeThread(h_rThread, &exit_code);                                         
    //retrieving the return value, i.e., the module
                                                                                      
    //  handle returned by LoadLibraryA
    CloseHandle(h_rThread);                                                           
    //freeing the injected thread handle,
    VirtualFreeEx(h_process, DLLPath_addr, 0, MEM_RELEASE);                           
    //... and the memory allocated for the DLL path,
    CloseHandle(h_process);                                                           
    //... and the handle for the target process

    return (HANDLE)exit_code;
}
```

> https://en.wikipedia.org/wiki/DLL_injection

참고
> API 후킹을 통한 악성 이벤트 분석 & 엔드 포인트 모니터링 도구 Captain
> https://github.com/y3n11/Captain

<br>

---

<b> 2. <u> Living off the Land </u> </b>

시스템 내부적인 관리 도구 / 유틸리티를 이용한 공격

Fileless 공격의 일종으로 분류되기도 한다.

 - PowerShell 
    
    Windows 장치 관리를 위한 스크립트 실행 프레임워크로, 공격자는 PowerShell을 통해 악성 스크립트 실행, 권한 탈취, 백도어 설치 등의 작업을 수행할 수 있음

 - WMI(Windows Management Instrumentation) 

    스크립트 자동화, 프로세스/ 프로그램 실행 시간 설정, 설치된 응용 / HW 정보 가져오기, 변경 사항 모니터링 등 관리 작업에 필요한 Windows에 핵심 구성요소로, Fileless 공격에 악용될 수 있음

</br>

[관련 정보]

 - LtoL 기술의 바이너리, 스크립트를 문서화한 LOLBAS 프로젝트

    https://github.com/LOLBAS-Project/LOLBAS

 - Powershell과 관련된 로깅을 모두 활성화
 
    로컬 그룹 정책 편집기 (gpedit.msc) -> 컴퓨터 구성 -> Windows 구성 요소 -> Windows PowerShell -> PowerShell 스크립트 블록 로깅을 사용으로 변경

 - Sysmon

    high-quality 이벤트 추적이 포함된 Sysmon configuration

    https://github.com/SwiftOnSecurity/sysmon-config

```
Sysmon을 이용해 탐지는 가능하지만 차단은 X
```

</br>

---

<b> 3. Fileless Persistence </b>

악성 코드를 항상 실행하게 하기 위한 지속성 공격

 - System's Registry

    레지스트리에 악성 스크립트를 저장하여 시스템이 시작되거나 특정 파일을 열 때 트리거 되도록 함

     https://docs.microsoft.com/ko-kr/windows/win32/sysinfo/registry-functions?redirectedfrom=MSDN

```
[레지스트리 하이브]

Hive는 운영 체제가 시작되거나 사용자가 로그인할 때 지원 파일 집합이 메모리에 로드된 레지스트리의 키, 하위 키 및 값의 논리적 그룹

HKEY_CLASSES_ROOT
HKEY_CURRENT_USER
HKEY_LOCAL_MACHINE
HKEY_USERS
HKEY_CURRENT_CONFIG
```


``` c
//레지스트리에서 문자열을 읽는 예제

BOOL readStringFromRegistry(HKEY hKeyParent, PWCHAR subkey, PWCHAR valueName, PWCHAR *readData)
{
    HKEY hKey;
    DWORD len = TOTAL_BYTES_READ;
    DWORD readDataLen = len;
    PWCHAR readBuffer = (PWCHAR )malloc(sizeof(PWCHAR)* len);
    if (readBuffer == NULL)
        return FALSE;
    //Check if the registry exists
    DWORD Ret = RegOpenKeyEx(
                    hKeyParent,
                    subkey,
                    0,
                    KEY_READ,
                    &hKey
                );
    if (Ret == ERROR_SUCCESS)
    {
        Ret = RegQueryValueEx(
                  hKey,
                  valueName,
                  NULL,
                  NULL,
                  (BYTE*)readBuffer,
                  &readDataLen
              );
        while (Ret == ERROR_MORE_DATA)
        {
            // Get a buffer that is big enough.
            len += OFFSET_BYTES;
            readBuffer = (PWCHAR)realloc(readBuffer, len);
            readDataLen = len;
            Ret = RegQueryValueEx(
                      hKey,
                      valueName,
                      NULL,
                      NULL,
                      (BYTE*)readBuffer,
                      &readDataLen
                  );
        }
        if (Ret != ERROR_SUCCESS)
     
//참고 : https://aticleworld.com/reading-and-writing-windows-registry/
```

 - Windows Task Scheduler

    악성 스크립트를 예약 목록에 추가하여 반복적으로 실행하게 함

<br>

---

<b> 4. 기타 </b>

 - UCA 우회 및 권한 상승 탐지

1. Event ID 4672 : 특별 권한 상승 부여 탐지
2. Event ID 4624 : Event ID 4672 이후에 오는 성공적인 로그온 이벤트
3. 룰 엔진에 프로세스 탐지 규칙을 추가
.file_path=\Windows\System32\sethc.exe OR Filesystem.file_path=\Windows\System32\utilman.exe OR Filesystem.file_path=\Windows\System32\osk.exe OR Filesystem.file_path=\Windows\System32\Magnify.exe OR Filesystem.file_path=\Windows\System32\Narrator.exe OR Filesystem.file_path=\Windows\System32\DisplaySwitch.exe OR Filesystem.file_path=*\Windows\System32\AtBroker.exe
4. Event ID 4657와 윈도우 레지스트리가 변화하는 Events IDs 4624 & 4672 타임라인을 탐색





</br>


---
참고 </br>
https://www.giac.org/paper/gcfa/11563/hunting-ghosts-fileless-attacks/150888

https://sevrosecurity.com/2020/04/08/process-injection-part-1-createremotethread/

https://sevrosecurity.com/2020/04/13/process-injection-part-2-queueuserapc/#high_level_api

http://www.i3.or.kr/html/paper/2020-2/(2)2020-2.pdf

https://attack.mitre.org/techniques/enterprise/

https://wave1994.tistory.com/31

https://yum-history.tistory.com/281
