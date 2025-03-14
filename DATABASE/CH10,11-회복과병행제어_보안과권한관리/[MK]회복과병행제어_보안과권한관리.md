# Chapter 10. 회복과 병행 제어
## 1. 트랜잭션
### 1. 트랜잭션의 개념
- 작업 하나를 수행하는 데 필요한 데이터베이스의 연산들을 모아놓은 것
	- DB에서 논리적인 작업의 단위
### 2. 트랜잭션의 특성
- ACID 특성
#### 원자성 atomicity
- 트랜잭션을 구성하는 연산들이 모두 정상적으로 실행되거나 하나도 실행되지 않아야 한다는 all-or-nothing 방식\
#### 일관성 consistency
- 트랜잭션이 성공적으로 수행된 후에도 데이터베이스가 일관된 상태를 유지해야 함
#### 격리성 isolation
- 현재 수행 중인 트랜잭션이 완료될 때까지 트랜잭션이 생성한 중간 연산 결과에 다른 트랜잭션들이 접근할 수 없음을 의미
#### 지속성 durability
- 트랜잭션이 성공적으로 완료된 후 데이터베이스에 반영한 수행 결과는 어떠한 경우에도 손실되지 않고 영구적이어야 함
#### 트랜잭션의 특성을 지원하는 DBMS의 기능
- 원자성 - 회복 기능
- 일관성 - 병행 제어 기능
- 격리성 - 병행 제어 기능
- 지속성 - 회복 기능
### 3. 트랜잭션의 연산
- commit 연산: 트랜잭션이 성공적으로 수행되었음을 선언(작업 완료)
- rollback 연산: 트랜잭션을 수행하는 데 실패했음을 선언(작업 취소)
### 4. 트랜잭션의 상태
#### 활동 상태 active
- 트랜잭션이 수행되기 시작하여 현재 수행 중인 상태
#### 부분 완료 상태 partially committed
- 트랜잭션의 마지막 연산이 실행된 직후의 상태
- 모든 연산의 처리가 끝났지만 트랜잭션이 수행된 최종 결과를 데이터베이스에 아직 반영하지 않은 상태
#### 완료 상태 committed
- 트랜잭션이 성공적으로 완료되어 commit 연산을 실행한 상태
#### 실패 상태 failed
 - 여러 이유로 인해 장애가 발생하여 트랜잭션의 수행이 중단된 상태
#### 철회 상태 aborted
- 트랜잭션 수행에 실패하여 rollback 연산을 실행한 상태
## 2. 장애와 회복
- 회복: 장애가 발생했을 때 데이터베이스를 장애가 발생하기 전의 일관된 상태로 복구시키는 것
### 1. 장애의 유형
- 장애 failure: 시스템이 제대로 동작하지 않는 상태

| 유형      | 설명                                                                                                                  |
| ------- | ------------------------------------------------------------------------------------------------------------------- |
| 트랜잭션 장애 | - 의미: 트랜잭션 수행 중 오류가 발생하여 정상적으로 수행을 계속할 수 없는 상태<br>- 원인: 트랜잭션의 논리적 오류, 잘못된 데이터 입력, 시스템 자원의 과다 사용 요구, 처리 대상 데이터의 부재 등 |
| 시스템 장애  | - 의미: 하드웨어의 결함으로 정상적으로 수행을 계속할 수 없는 상태<br>- 원인: 하드웨어 이상으로 메인 메모리에 저장된 정보가 손실되거나 교착 상태가 발생한 경우 등                     |
| 미디어 장애  | - 의미: 디스크 장치의 결함으로 디스크에 저장된 데이터베이스의 일부 혹은 전체가 손상된 상태<br>- 원인: 디스크 헤드의 손상이나 고장 등                                     |
### 2. 데이터베이스의 저장 연산
| 저장 장치             | 설명                                                                                                              |
| ----------------- | --------------------------------------------------------------------------------------------------------------- |
| 휘발성 저장 장치 (소멸성)   | - 의미: 장애가 발생하면 저장된 데이터 손실<br>- 예: 메인 메모리 등                                                                      |
| 비휘발성 저장 장치 (비소멸성) | - 의미: 장애가 발생해도 저장된 데이터가 손실되지 않음. 단, 디스크 헤더 손상 같은 저장 장치 자체에 이상이 발생하면 데이터가 손실될 수 있음<br>- 예: 디스크, 자기 테이프, CD/DVD 등 |
| 안정 저장 장치          | - 의미: 비휘발성 저장 장치를 이용해 데이터 복사본 여러 개를 만드는 방법으로, 어떤 장애가 발생해도 데이터가 손실되지 않고 데이터를 영구적으로 저장할 수 있음                      |
- 디스크와 메인메모리 간 데이터 이동을 블록(block) 단위로 수행
	- input(X) : 디스크 블록에 저장되어 있는 데이터 X를 메인 메모리 버퍼 블록으로 이동시키는 연산
	- output(X) : 메인 메모리 버퍼 블록에 있는 데이터 X를 디스크 블록으로 이동시키는 연산
- 메인메모리 버퍼 블록과 프로그램 변수 간의 데이터 이동
	- read(X): 메인 메모리 버퍼 블록에 저장되어 있는 데이터 X를 프로그램의 변수로 읽어오는 연산
	- write(X): 프로그램의 변수 값을 메인 메모리 버퍼 블록에 있는 데이터 X에 기록하는 연산
### 3. 회복 기법
- DB 관리 시스템에 있는 회복 관리자가 담당
#### 회복을 위한 연산
- 데이터를 별도의 장소에 미리 복사해두고 장애 발생 시 복사본 사용
	- 덤프(dump): 데이터베이스 전체를 다른 저장 장치에 주기적으로 복사하는 방법
	- 로그(log): 데이터베이스에서 변경 연산이 실행될 때마다 데이터를 변경하기 이전 값과 변경한 이후의 값을 별도의 파일에 기록하는 방법
- 데이터베이스 복구
	- redo(재실행): 가장 최근에 저장한 데이터베이스 복사본을 가져온 후 로그를 이용해 복사본이 만들어진 이후에 실행된 모든 변경 연산을 재실행하여 장애가 발생하기 직전의 데이터베이스 상태로 복구 (전반적으로 손상된 경우에 주로 사용)
	- undo(취소): 로그를 이용해 지금까지 실행된 모든 변경 연산을 취소하여 데이터베이스를 원래의 상태로 복구 (변경중이었거나 이미 변경된 내용만 신뢰성을 잃은 경우에 주로 사용)
- 로그 레코드: 로그를 저장한 로그 파일에 기록된 기본 단위

| 로그 레코드      | 설명  |
| ----------- | --- |
| <Ti, start> |     |
| <           |     |
#### 로그 회복 기법
1. 즉시 갱신 회복 기법: 트랜잭션 수행 중에 데이터를 변경한 연산의 결과를 데이터베이스에 즉시 반영
2. 지연 갱신 회복 기법: 트랜잭션이 수행되는 동안에는 데이터 변경 연산의 결과를 데이터베이스에 즉시 반영하지 않고 로그 파일에만 기록해 두었다가, 트랜잭션이 부분 완료된 후에 로그에 기록된 내용을 이용해 데이터베이스에 한 번에 반영
#### 검사 시점 회복 기법
- 로그 기록을 이용하되, 일정 시간 간격으로 검사 시점을 만들어둔다. 장애가 발생하면 가장 최근 검사 시점 이전의 트랜잭션에는 회복 작업을 수행하지 않고, 이후의 트랜잭션에만 회복 작업을 수행
#### 미디어 회복 기법
- 전체 데이터베이스의 내용을 일정 주기마다 다른 안전한 저장 장치에 복사해두는 덤프를 이용
## 3. 병행 제어
### 1. 병행 수행과 병행 제어
- 데이터베이스 관리 시스템은 여러 사용자가 데이터베이스를 동시에 공유할 수 있도록 여러개의 트랜잭션이 동시에 수행되는 병행 수행 지원
	- 여러 트랜잭션이 차례로 번갈아 수행되는 인터리빙 방식으로 진행
- 병행 제어: 여러 개의 트랜잭션이 병생 수행되면서 같은 데이터에 접근하여 연산을 실행하더라도, 문제가 발생하지 않고 정확한 수행 결과를 얻을 수 있도록 트랜잭션의 수행을 제어하는 것
### 2. 병행 수행의 문제
#### 갱신 분실 lost update
- 하나의 트랜잭션이 수행한 데이터 변경 연산의 결과를 다른 트랜잭션이 덮어써 변경 연산이 무효화되는 것
#### 모순성 inconsistency
- 하나의 트랜잭션이 여러 개의 데이터 변경 연산을 실행할 때 일관성 없는 상태의 데이터베이스에서 데이터를 가져와 연산을 실행함으로써 모순된 결과가 발생하는 것
#### 연쇄 복귀 cascading rollback
- 트랜잭션이 완료되기 전에 장애가 발생하여 rollback 연산을 수행하면, 이 트랜잭션이 장애 발생 전에 변경한 데이터를 가져가 변경 연산을 싱행한 또 다른 트랜잭션에도 rollback 연산을 연쇄적으로 실행해야 한다는 것
### 3. 트랜잭션 스케줄
- 연산 실행되는 순서에 따라 결과가 달라지기도 하여 순서가 중요함

| 트랜잭션 스케줄  | 의미                                         |
| --------- | ------------------------------------------ |
| 직렬 스케줄    | 인터리빙 방식을 이용하지 않고 트랜잭션별로 연산들을 순차적으로 실행시키는 것 |
| 비직렬 스케줄   | 인터리빙 방식을 이용하여 트랜잭션들을 병행해서 수행시키는 것          |
| 직렬 가능 스케줄 | 직렬 스케줄과 같이 정확한 결과를 생성하는 비직렬 스케줄            |
### 4. 병행 제어 기법
- 여러 트랜잭션을 병행 수행하면서도 정확한 결과를 얻을 수 있는 직렬 가능성을 보장받기 위해 사용
#### 로킹 기법 locking
- 병행 수행되는 트랜잭션들이 동일한 데이터에 동시에 접근하지 못하도록 lock, unlock 2개의 연산을 이용해 제어
- 한 트랜잭션이 먼저 접근한 데이터에 대한 연산을 모두 마칠 때까지, 해당 데이터에 다른 트랜잭션이 접근하지 못하도록 상호 배제하여 직렬 가능성 보장
#### 2단계 로킹 규약
- 기본 로킹 규약의 문제를 해결하고 트랜잭션의 직렬 가능성을 보장하기 위해 lock과 unlock 연산으 ㅣ수행 시점에 대한 새로운 규약을 추가한  것
1. 확장 단계: 트랜잭션이 lock 연산만 실행할 수 있고, unlock 연산은 실행할 수 없는 단계
2. 축소 단계: 트랜잭션이 unlock 연산만 실행할 수 있고, lock 연산은 실행할 수 없는 단계
# Chapter 11. 보안과 권한 관리
## 1. 보안
1. 물리적 환경에 대한 보안: 자연 재해처럼 물리적 손상을 발생시키는 위험으로부터 보호
2. 권한 관리를 통한 보안: 접근이 허락된 사용자만 부여된 권한 내에서 데이터베이스를 사용할 수 있도록
3. 운영 관리를 통한 보안: 접근이 허락된 사용자가 부여된 권한 내에서 데이터베이스를 사용하더라도 실수 등의 이유로 데이터 무결성을 위반할 수 있음 -> 올바른 제약조건 정의
## 2. 권한 관리
### 1. 권한 관리의 개념
- 계정이 발급된 사용자가 로그인에 성공했을 경우만 DB에 접근이 가능하도록 하는 접근 제어 기능을 기본으로 제공
### 2. 권한의 부여
- 객체의 소유자가 다른 사용자에게 객체에 대한 사용 권한을 부여하기 위해 필요한 SQL 명령어 - GRANT\
```sql
GRANT 권한 ON 객체 TO 사용자 [WITH GRANT OPTION];
```
### 3. 권한의 취소
- 다른 사용자에게 부여된 객체의 사용권한 취소
```SQL
REVOKE 권한 ON 객체 FROM 사용자 CASCADE | RESTRICT;
```
### 4. 역할의 부여와 취소
- 미리 부여하고 싶은 권한을 역할에 넣어두고 필요할 때 역할을 부여하면 여러 권한을 한번에 쉽게 부여 가능
```SQL
CREATE ROLE 롤이름;

GRANT 권한 ON 객체 TO 롤이름;

GRANT 롤이름 TO 사용자;
```
- 역할 부여 취소
```SQL
REVOKE FROM 
```
# 예상 질문
1. 데이터베이스 보안 유형 세 가지 중 한가지 설명
2. 어떤 변수에 값을 저장할 때 저장 장치에 어떤 변경사항이 있는지 데이터 이동 연산을 이용해 설명해주세요.