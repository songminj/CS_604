# Chapter 01 - 컴퓨터 구조 시작하기
## 01-1 컴퓨터 구조를 알아야 하는 이유

### 문제 해결
- 컴퓨터 구조를 이해하고 있다면 문제 상황을 빠르게 진단 및 해결할 수 있음

### 성능, 용량, 비용
- 상황에 맞는 컴퓨터 성능과 비용 고려 가능
- 프로그래밍 언어 문법만으로 해결할 수 없는 분야

## 01-2 컴퓨터 구조의 큰 그림
### 컴퓨터가 이해하는 정보
- 컴퓨터는 0, 1로 표현된 정보(데이터, 명령어)만을 이해함
- 데이터: 컴퓨터가 이해하는 숫자, 문자, 이미지 등 정적인 정보
- 명령어: 데이터를 움직이고 컴퓨터를 작동시키는 정보

### 컴퓨터의 4가지 핵심 부품
- 중앙처리장치(CPU), 주기억장치(메모리), 보조기억장치, 입출력장치
- 메모리: 현재 실행되는 프로그램의 명령어와 데이터를 저장하는 부품
  - 프로그램이 실행되기 위해선 반드시 메모리에 저장되어 있어야 함
  - 메모리는 현재 실행되는 프로그램의 명령어와 데이터를 저장함
  - 메모리에 저장된 값의 위치는 주소로 알 수 있음
- CPU: 컴퓨터의 두뇌. 메모리에 저장된 명령어를 읽고 해석 및 실행
  - 산술논리연산장치(ALU), 레지스터, 제어장치(CU)로 구성
  - ALU: 계산기. 계산만을 위해 존재하는 부품
  - 레지스터: CPU 내부 임시 저장 장치. 프로그램 실행에 필요한 값을 임시로 저장
  - 제어창치: 제어신호라는 전기 신호를 내보내고 명령어를 해석하는 장치
- 보조기억창치: 전원이 꺼져도 저장된 내용을 잃지 않는 기억 장치
- 입출력장치: 마이크, 스피커, 프린터, 마우스, 키보드 등

### 메인보드와 시스템 버스
- 메인보드: (= 마더보드) 여러 부품을 부착할 수 있는 슬롯과 연결 단자가 있음
- 버스: 연결된 부품끼리 정보를 주고받을 수 있는 통로
  - 시스템버스: 컴퓨터 4 가지 핵심 부품을 연결하는 가장 중요한 버스
    - 주소 버스, 데이터 버스, 제어 버스로 구성됨

# Chapter 02 - 데이터
## 02-1 0과 1로 숫자를 표현하는 방법
### 정보 단위
- 비트: 0, 1을 나타내는 가장 작은 정보 단위
- 바이트: 8비트. 비트보다 한 단계 큰 단위
- 워드: CPU가 한 번에 처리할 수 있는 데이터 크기

### 이진법
- 0과 1만으로 모든 숫자를 표현하는 방법
- 이진수: 이진법으로 표현한 수
- 혼란을 방지하기 위해 이진수 끝에 아래첨자(2)를 붙이거나 이진수 앞에 0b를 붙임

### 이진수의 음수 표현
- 2의 보수: 0, 1만으로 음수를 표현하는 방법 중 가장 널리 사용되는 방법
  - 어떤 수를 그보다 큰 2^n에서 뺀 값
- 컴퓨터 내부에선 양수인지 음수인지 구분하기 위해 플래스를 사용함

### 십육진법
- 이진법만으로는 숫자의 길이가 너무 길어진다는 단점이 있음
- 이를 해결하기 위해 십육진법을 자주 사용
- 아래첨자(16)을 붙이거나 앞에 0x를 붙여 구분
- 십육진수 - 이진수로 변환하기 쉽다는 장점이 있음

### 십육진수를 이진수로 변환하기
- 십육진수 한 글자를 4비트의 이진수로 간주
- 1A2B(16) -> 0001 1010 0010 1011(2)

### 이진수를 십육진수로 변환하기
- 이진수를 네 개씩 끊어 준 후, 16진수로 변환한 뒤 이어붙임
- 1101 0101(2) -> D5(16)

## 02-2 0과 1로 문자를 표현하는 방법
### 문자 집합과 인코딩
- 문자 집합: 컴퓨터가 인식하고 표현할 수 있는 문자의 모음
- 문자 인코딩: 문자를 0, 1로 변환하는 과정
- 문자 디코딩: 0, 1을 문자로 변환하는 과정

### 아스키 코드
- 아스키(ASCII): 초창기 문자 집합 중 하나
- 각 문자는 7비트로 표현되며, 총 128개의 문자 표현 가능

### EUC-KR
- 한글 인코딩 - 완성형 인코딩, 조합형 인코딩으로 구분
  - 완성형 인코딩: 완성된 하나의 글자에 고유한 코드를 부여하는 인코딩 방식
  - 조합형 인코딩: 초성, 중성, 종성 비트열을 할당, 조합해 하나의 글자를 완성시키는 인코딩 방식
  - EUC-KR: 완성형 인코딩 방식(한 글자에 2byte)
  - 쀍, 쀓, 믜 등은 EUC-KR으로 표현 불가(byte가 부족함)
  - 이러한 경우를 조금이나마 해결하기 위해 ms CP949 등장

### 유니코드와 UTF-8
- 유니코드: 대부분 나라의 문자, 특수문자, 화살표 등도 표현 가능한 통일된 문자 집합
- 유니코드는 글자에 부여된 값 자체를 인코딩된 값으로 삼지 않고 다양한 방법으로 인코딩함
- ex) UTF-8, UTF-16, .. 
- UTF-8: 대중적인 인코딩 방법