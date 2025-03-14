# Chapter 5. CPU 성능 향상 기법
## 05-1 빠른 CPU를 위한 설계 기법

### 클럭
- 클럭 속도: 헤르츠 단위로 측정됨(1초에 몇 번 반복되는가)
  - ex) 클럭이 1초에 100번 반복되면 CPU 클럭 속도는 100Hz인 것
- 오버클럭킹: 최대 클럭 속도를 강제로 더 뜰어올리는 것
  - 과한 경우 CPU에 무리가 가며 발열 문제가 발생
- 클럭 속도는 일정하지 않고, 경우에 따라 빨리지기도, 느려지기도 함

### 코어와 멀티코어
- 코어: 명령어를 실행하는 부품
  - 코어 개수를 늘리면, CPU 성능을 높일 수 있음
  - 코어를 여러 개 포함하고 있는 CPU를 멀티코어 CPU, 멀티코어 프로세서라고 부름

### 스레드와 멀티스레드
- 스레드: 실행 흐름의 단위
  - 하드웨어적 스레드: 하나의 코어가 동시에 처리하는 명령어 단위
    - 1코어 2스레드라면, 명령어를 실행하는 부품을 2개 포함하고 있다는 의미
    - 이를 멀티스레드 프로레서, 멀티스레드 CPU라고 함
    - 인텔의 경우, 멀티스레드 기술을 '하이퍼스레딩'이라고 칭함
  - 소프트웨어적 스레드: 하나의 프로그램에서 독립적으로 실행되는 단위

### 멀티스레드 프로세서
- 1코어 2스레드라면 CPU 코어 내에 레지스터 세트가 2개 있는 것
- 마치 CPU가 2개 있는 것처럼 보이기에, 하드웨어 스레드를 논리 프로세서 라고도 부름

## 05-2 명령어 병렬 처리 기법
- 명령어 파이프라이닝
- 슈퍼스칼라
- 비순차적 명령어 처리

### 명령어 파이프라인
- 명령어는 일련의 과정을 통해 수행됨
  1. 명령어 인출
  2. 명령어 해석
  3. 명령어 실행
  4. 결과 저장
- 해당 과정을 공장 생산 라인처럼 수행할 수 있으며, 이 기법을 명령어 파이프라이닝이라고 함
- 파이프라이닝이 높은 성능을 가져오기는 하지만, 성능 향상에 실패하는 경우도 있음. 이를 파이프라인 위험이라고 함

### 파이프라인 위험
- 데이터 위험: 데이터 의존성에 의해 발생
- 제어 위험: 분기 등으로 이한 PC의 갑작스러운 변화에 의해 발생
- 구조적 위험: 명령어를 동시에 수행할 때 레지스터, ALU 등 자원에 동시 접근하려고 할 때 발생(= 자원 위험)

### 슈퍼 스칼라
- 파이프라이닝을 동시에 여러 개 수행9여러 명령어를 동시에 수행)
- 마치 공장 생산 라인을 여러 개 두는 것과 같음
- 해당 기능을 지원하는 CPU를 슈퍼스칼라 프로세서, 슈퍼스칼라 CPU라고 부름
- 이를 지원하기 위해선 더욱 고도화된 문제 해결 능력이 있어야 함

### 비순차적 명령어 처리
- OoOE. 오늘날 CPU 성능 향상에 크게 기여한 기법
- 합법적인 새치기
- 연관이 없는 나중의 명령어를 미리 수행

## 05-3 CISC와 RISC
### 명령어 집합
- CPU가 이해할 수 있는 명령어들의 모음. (ISA: 명령어 집합 구조)
- CPU 제조사마다 ISA가 모두 다름
- CISC와 RISC는 ISA의 주요한 특징

### CISC
- Complex Instruction Set Computer
- 복잡한 명령어 집합을 활용하는 컴퓨터
- 가변 길이 명령어를 활용하며, 다양한 종류의 명령어가 있음
- ex) x86, x86-64
- RISC에 비해 코드 길이가 짧음
- 문제점
  - 클럭 소요 시간이 일정하지 않다
  - CISC 명령어 20%가 전체 명령어의 80% 가량을 차지한다는 연구 결과가 있음

### RISC
- Reduced Instruction Set Computer
- 원활한 파이프라이닝을 위해 명령어 길이와 수행 시간이 짧고 규격화되는 것을 지향
- 자주 쓰이는 기본적인 명령어를 작고 빠르게 만듦
- 하나의 명령어가 1클럭 내외로 실행되기 때문에 파이프라이닝에 최적회됨