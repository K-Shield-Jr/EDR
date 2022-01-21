Yara 룰 설정
=============================

# yara 룰 이란?

악성코드의 특성과 행위에 포함된 패턴을 이용하여 악성 파일을 분류하는데 사용되는 툴이다.   
VirusTotal에서 제작되었고, 코드 관리는 Google에서 하고 있다.   

단순 Text String, Binary값 뿐만 아니라 Entry Point, File Offset 등의 특정이 가능하다.   
Yara는 멀티 플랫폼의 지원으로 Linux, Mac OS X, Windows 시스템에서 모두 사용 가능하다.   
rule 파일 => 텍스트 파일 형태(.yar)   

> download : https://github.com/plusvic/yara

> Yara Docs : https://yara.readthedocs.io/
> > 일부 번역
https://github.com/woodonggyu/yara/blob/master/How%20to%20Writing%20YARA-Rules.md

# yara 룰 설정

```C
rule sample
	meta:
		author = "R&B"
		type = "APT"

	strings:
		&str = "abcdef"
		&hex = { A5 FF FF FF FF }
		&re = /md5: [0-9a-zA-Z]{32}

	condition:
		&str or &hex
```

## 1. meta
Rule 작성자의 추가정보 입력하는 것을 말한다.   
작성자, 작성일, 버전, 설명 등을 기록한다.

## 2. strings
변수는 &로 시작한다.   
변수 명은 알파벳과 언더 바_를 사용할 수 있다.  

### 2-1) String Type
“ ”사이 ASCII 인코딩을 기준으로 작성할 수 있다.
### 2-2) Hex String Type
{ } 사이 hex 값을 입력하게 되는데 모든 hex 값을 알지 못하더라도 와일드 카드를 통하여 대체할 수 있다.   
ex) &str_hex = {A2 FF ?? A2 ?? A2 A2 A2} 
### 2-3) Regex Type
정규표현식 사용 시 슬래시(/ /)사이에 표기하고, Yara의 정규 표현식 기준은 Perl을 따른다.   
(참조 https://github.com/woodonggyu/yara/blob/master/How%20to%20Using%20PCRE.md)

## 3. condition
:boolearn 값으로만 결과가 나타난다. (True or False)  
and, or, not, any of them, all of them, 1 or them, 3 of them 조건을 걸 수 있다.   
뿐만 아니라 기본적인 C 연산자(산술, 관계 연산자 등)을 지원한다.

### 3-1) at, in [특정 오프셋]
at 100 : 프로세스의 가상주소 100(or 파일 안의 오프셋 100에서) 있는지  
in 100..filesize : 100부터 파일의 끝(파일의 사이즈 크기만큼)까지 조회   
우선 순위 : or, and << at,in

## 4) 알아두면 유용한 키워드
### 4-1) nocase
대소문자를 구분하지 않고 탐색한다.   
ex) &str = "abcd" nocase

### 4-2) wide
바이너리 파일 내에 문자 당 2바이트로 인코딩 된 문자열을 검색하는데 유용하다.

## 5. 모듈
yara가 기능을 확장하기 위해 제공하는 방법

### 5-1) Pe
PE 파일 형식의 특성과 기능을 사용하여 PE 파일에 대한 보다 세분화된 규칙
### 5-2) Cuckkoo
Cuckoo 모듈을 사용하면 Cuckoo 샌드박스에 의해 생성된 행동 정보를 기반으로 YARA 규칙을 생성
### 5-3) hash 
파일의 일부에서 해시(MD5, SHA1, SHA256)를 계산하고 해당 해시를 기반으로 시그니처 작성 가능

## 6. yara rules를 작성하는 일을 자동화
## 6-1) Joe Sandbox
- https://www.joesandbox.com/yaraspaged/
- Joe Sandbox에서는 동적 분석 결과를 바탕으로 Windows용 Signature를 제작할 수 있음
- 무료 온라인 샌드박스 (VirusTotal, Hybrid-Analysis, Malwr 등)과 호환

## 6-2) Hyara
- https://github.com/hy00un/Hyara
- 정적 분석 단계에서의 YARA 제작의 편의성을 극대화한 강력한 IDA Plug-in
