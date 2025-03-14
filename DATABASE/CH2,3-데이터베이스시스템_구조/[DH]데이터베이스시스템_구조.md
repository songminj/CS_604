# Chapter 02. 데이터베이스 관리 시스템
## 01. 데이터베이스 관리 시스템의 등장 배경
DBMS 이전엔 파일 시스템(file system)이라는 청보 처리 시스템을 이용하였으나, 단점이 많았다.
- 장점: 별도 구매 비용이 들지 않는다. 
- 단점: 
  - 응용 프로그램마다 파일을 따로 유지해야 하므로 같은 데이터가 여러 파일에 저장될 수 있다.(데이터 중복성 문제)
  - 응용 프로그램이 파일에 직접 접근해 데이터를 처리해야 하므로 데이터 파일에 종속적이다.
  - 데이터 파일에 대한 동시 공유, 보안, 회복 기능이 부족하다. 일반 파일 시스템은동시 접근을 제공하지 않는다.
  - 응용 프로그램을 개발하기 쉽지 않다.  
=> 이들을 해결하고자 DBMS 등장

## 02. 데이터베이스 관리 시스템의 정의 
- 파일 시스템의 데이터 중복과 데이터 종속 문제를 해겨하기 위해 제시된 SW
- DB를 생성, 접근, 관리
- 데이터 동시 접근 문제를 관리
- 제공하는 인터페이스를 활용해 쉽게 데이터 처리 가능

**DBMS 제공 주요 기능**
- 정의 기능: DB 구조 정의, 수정 
- 조작 기능: 데이터를 삽입, 수정, 수정, 삭제 
- 제어 기능: 데이터를 항상 정확하고 안전하게 유지

## 03. 데이터베이스 관리 시스템의 장, 단점
### 장점
- 데이터 중복을 통제할 수 있다.
- 데이터 독립성이 확보된다.(응용 프로그램과 DB 간 독립)
- 데이터를 동시 공유할 수 있다.
- 데이터 보안이 향상된다.
- 데이터 무결성을 유지할 수 있다.
- 표준화할 수 있다.
- 장애 발생 시 회복이 가능하다.
- 응용 프로그램 개발 비용이 줄어든다.

### 단점
- 비용이 많이 든다.
- 백업과 회복 방법이 부족하다.
- 중앙 집중 관리로 인한 취약점이 존재한다.

## 04. 데이터베이스 관리 시스템의 발전 과정
1. 1세대 데이터베이스 관리 시스템: 네트워크, 계층 DBMS
2. 2세대 데이터베이스 관리 시스템: 관계 DBMS
3. 3세대 데이터베이스 관리 시스템: 객체지향, 객체관계 DBMS
4. 4세대 이후 데이터베이스 관리 시스템: NoSQL, NewSQL DBMS

# Chapter 03. 데이터베이스 시스템
## 01. 데이터베이스 시스템의 정의
- 데이터베이스: 데이터를 저장하는 곳    
- 데이터베이스 관리 시스템: DB에 저장된 데이터가 일관되고 무결한 상태로 유지되도록 관리하는 역할
- 데이터베이스 시스템: DB와 DBMS를 이용해 조직에 필요한 정보를 제공하는 전체 시스템. 
  DB에 데이터를 저장하고 관리해 조직에 필요한 정보를 생성해주는 시스템

## 02. 데이터베이스의 구조
### 1. 스키마: 데이터베이스에 저장되는 데이터 구조와 제약조건을 정의한 것
   - 인스턴스: 실제로 저장된 값
   - 스키마는 한번 정의되면 자주 변경되진 않지만, 인스턴스는 계속 변한다.
### 2. 3단계 데이터베이스 구조
일반 사용자마저 DB의 내부 구조를 알고 있는 것은 불필요하므로 3단계 DB 구조를 제안
   - 내부 -> 외부로 갈수록 추상화의 레벨이 높아진다. 
      - 외부 단계: 개별 사용자 관점에서 바라보는 단계(관심있는 일부분만 알면 된다.)
      - 개념 단계: 조직 전체의 관점에서 바라보는 단계(전체 DB의 논리적 구조를 정의한다.)
      - 내부 단계: 물리적인 저장 장치의 관점에서 바라보는 단계(디스크 등 저장 장치 관점에서 표현한다.)
### 3. 데이터 독립성
- 세 가지 유형의 스키마가 있지만, 모두 하나의 DB를 표현한 것이다. 따라서 각 스키마 사이 유기적인 대응 관계가 필요하다.
- 이때의 대응 관계를 매핑(mapping)이라고 한다.
- 외부 스키마 - (외부/개념 사상(논리적 데이터 독립성)) - 개념 스키마 - (개념/내부 사상(물리적 데이터 독립성)) - 내부 스키마
- 단게별로 스키마를 유지하며 대응고나계를 정의하는 궁극적인 목적은 **데이터 독립성**을 실형하기 위함이다.
  - 하위 스키마를 변경하더라도 상위 스키마가 영향을 받지 않는다.
  - ex) 내부 스키마가 변경되어도 개념 스키마는 영향을 받지 않으며, 개념 스키마가 변경되어도 의부 스키마는 영향을 받지 않는다.
### 4. 데이터 사전
- 저장된 데이터를 올바르게 관리하고 이용하기 위해선 부가 정보가 필요하다.(ex- 스키마, 사상 정보 등)
- 데이터에 관한 정보를 저장하는 곳을 데이터 사전(시스템 카탈로그)이라고 한다.

## 03. 데이터베이스 사용자
1. 데이터베이스 관리자(DBA): DBS를 운영 및 관리
   - 데이터베이스 구성 요서 선정
   - 데이터베이스 스키마 정의
   - 보안 및 접근 권한 정책 결정 등
2. 최종 사용자
   - 데이터를 CRUD하기 위해 DB에 접근하는 사용자. B에 대한 전문 지식이 필수는 아니다.
3. 응용 프로그래머
    - 응용 프로그램을 작성할 때 DB에 접근하는 데이터 조작어를 삽입하는 사용자

## 04. 데이터 언어
1. 데이터 정의어(DDL): create, alter, drop 등
2. 데이터 조작어(DML): insert, select, update, delete 등
3. 데이터 제어어(DCL): revoke, grant 등 - DB의 어떤 특성을 보완하기 위한 것인가요?

## 05. 데이터베이스 관리 시스템의 구성
1. 질의 처리기
2. 저장 데이터 관리자

# 예상 질문
> Q1. 개발할 때 DBMS를 사용하는 이유가 여러 가지 있습니다. 생각나는 것을 몇 가지 이야기해 보시고 그중 가장 큰 장점으로 생각되는 것과 그 이유를 알려주세요.

DBMS를 사용하는 이유는 데이터 동시 접근 문제를 관리해준다는 점, DB에 직접 접근하지 않으므로 보안성이 높아진다는 점, DB와 응용 프로그램 간 독립성이 확보된다는 점 등이 있습니다.  
저는 이중 독립성이 확보된다는 점이 가장 큰 장점이라고 생각합니다. 그 이유는 로직을 구현할 때 DB 내부 동작을 일일이 고려하지 않아도 되며, 이후 수정 작업이 필요할 때에도 DB를 고려하지 않고 구현한 코드만 바꾸면 되기 때문입니다.

> Q2. RDBMS와 NoSQL의 차이점을 설명해 주시고, RDBMS 혹은 NoSQL을 사용한 경험이 있다면 이야기해 주세요.   

RDBMS는 데이터 구조가 명확하고 스키마가 정해져있기에 유연하지 못합니다. 대신 데이터의 일관성을 보장합니다. 데이터를 Table 형태로 저장하고 이들끼리 Relation을 맺을 수 있어 데이터 간 연결을 이룹니다.  
NoSQL은 비관계형 DB로, 스키마가 고정되지 않아 유연하며 다양한 데이터 모델을 지원합니다. 다만 RDBMS만큼 강력한 데이터 일관성을 보장하지는 않습니다.