# CH10 회복과 병행제어 

## 1. 트랜잭션 

> Q. 트랜잭션이란 무엇인가?

- 데이터베이스는 다수의 사용자가 동시에 사용해도 정확한 데이터를 유지해야하고 장애가 발생해도 원래 상태로 복구할 수 있어야 한다. 
- 데이터베이스의 회복과 병행제어가 가능해져 데이터베이스가 일관된 상태를 유지할 수 있게 된다. 
- 트랜잭션은 작업 하나를 수행하는 데 필요한 데이터베이스의 연산들을 모아놓은 것으로 논리적인 작업의 단위가 된다. 
- SQL모음이라고 생각하면 좋음

<br>

> Q. 트랜잭션의 특성에 대해 말해보아라

- 트랜잭션의 특성은 원자성, 일관성, 격리성, 지속성 네가지의 특성을 가진다(ACID)
- Atomicity(원자성) : 트랜잭션을 구성하는 연산들이 모두 정상적으로 실행되거나 하나도 실행되지 않아야 한다는 all-or-nothing을 의미한다. 
- Consistency(일관성) : 트랜잭션이 성공적으로 수행된 후에도 데이터베이스가 일관된 상태를 유지해야 한다. 
  - 예를들어 성호의 계좌에서 은경의 계좌로 돈을 송금한다 했을때 delete 성호.잔액 update 은경.잔액 해야한다. 전 후의 계좌 잔액 결과 총계가 같아야하는게 일관성임.
- Isolation(격리성) : 트랜잭션이 완료될 때 까지 트랜잭션이 생성한 중간 결과물에 다른 트랜잭션이 접근할수 없음을 의미한다. 
- Durability(영속성) : 트랜잭션이 성공적으로 완료된 후 데이터 베이스에 반양한 수행결과는 손실되지 않고 영구적이어야 한다. 

<br>

> Q. 트랜잭션의 특성과 DBMS의 기능을 연결해보자 

- 회복 기능 : 원자성, 지속성 보장. 
- 병행 제어 기능 : 일관성, 격리성 보장. 


<br>

> Q. commit과 rollback에 대해 말해보세요

- commit은 트랜잭션의 수행이 성공적으로 완료되었음을 선언하는 연산이다. 
   - 예를들어 트랜잭션을 구성하는 연산이 정상적으로 처리되면 commit연산의 실행을 통해 트랜잭션의 수행이 성공적으로 완료가 된다. 
- rollback은 트랜잭션의 수행이 실패했음을 선언하는 연산이다. 
   - rollback이 실행되면 트랜잭션이 실행한 연산의 결과가 취소되고 트랜잭션 수행 전 상태로 돌아간다. 

<br>

> Q. 트랜잭션의 다섯가지 순서대로 상태를 말해보세요

- 활동상태 : 트랜잭션이 수행이 시작되어 수행중인 상태이다
- 부분완료상태 : 트랜잭션의 마지막 연산이 실행된 직푸를 이야기한다. 모든 연산이 끝났지만 수행된 최종 결과다 데이터베이스에 반영되지 않은 상태이다.
- 완료상태 : 트랜잭션이 성공적으로 완료되어 commit연산을 실행한 상태이다. 
- 실패상태 : 장애가 발생하여 트랜잭션의 수행이 중단된 상태이다. 
- 철회상태 : 실패하여 rollback연산을 실행한 상태를 철회라고 한다. 

<br>


## 2. 장애와 회복

> Q. 데이터베이스의 장애 유형에 대해서 말해보아라

- 시스템이 제대로 동작하지 않는 상태를 장애라고 한다. 장애가 발생하는 원인은 사용자의 실수, 정전등으로 인한 하드웨어 고장, 소프트웨어의 논리 오류등으로 다양하다. 
- 트랜잭션 장애, 시스템 장애, 미디어 장애로 나눌 수 있다. 


<br>

> Q. 저장장치의 종류에 대해 설명해보아라

- 휘발성 저장장치 : 장애가 발생하면 저장된 데이터가 손실된다 (메인메모리)
- 비휘발성 저장장치 : 장애가 발생해도 저장된 데이터가 손실되지 않는다. 단 디스크 헤더 손상과 같은 저장장치 자체의 이상이 발생하면 데이터가 손실될 수 있다 (디스크, 자기테이프, CD/DVD 등)
- 안정 저장장치 : 비휘발성 저장장치를 이용해 데이터 복사본 여러개를 만드는 방법으로, 어떤 장애가 발생해도 데이터가 손실되지 않고 데이터를 영구적으로 저장할 수 있다. 

<br>

- 일반적으로 데이터베이스는 비휘발성 저장장치인 디스크에 상주한다. 그러나 트랜잭션이 데이터베이스의 데이터를 처리하려면 데이터를 디스크에서 메인 메모리로 가져와 이를 처리한 후 그 결과를 다시 디스크로 보내는 작업이 필요하다. 

<br>

> Q. 블록에 대해 설명하라

- 디스크와 메인 메모리간의 이동을 블록 단위로 수행한다. 
- 디스크에 있는 블록을 디스크블록, 메인 메모리에 있는 블록을 버퍼 블록이라고 한다. 
- 디스크와 메인간의 데이터 이동은 input()과 output()으로 수행한다 
  - input(x) : 디스크 블록에 저장되어 있는 데이터 x를 **메인 메모리 버퍼블록으로 이동시키는** 연산
  - output(x) : 메인 메모리 버퍼 블록에 있는 데이터 x를 **디스크 블록으로 이동시키는** 연산. 

<br>

> Q. 메인 메모리의 버퍼블록과 프로그램 변수간의 데이터 이동 연산을 설명하라

- 응용 프로그램에서 트랜잭션 수행을 지시하면 메인 메모리 버퍼 블록에 있는 데이터를 프로그램의 변수로 가져온다. 
- 데이터 처리 결과를 저장한 변수 값을 메인 메모리 버퍼 블록으로 다시 올려야 한다. 
  - read(x) : 메인 메모리 버퍼블록에 있는 데이터 x를 프로그램의 변수로 읽어온다
  - write(x) : 프로그램의 변수 값을 메인 메모리 버퍼 블록에 있는 데이터 x에 기록한다. 


### 정리 
--- 

응용 프로그램 <- read(a) - 메인 메모리 <- input(a) - 디스크
<br>
응용 프로그램 - write(b)-> 메인 메모리 - output(b)-> 디스크 

<br>

> Q. 회복에 대해 설명하라 

- 회복이란 데이터베이스에 장애가 발생했을 때 장애가 발생하기 전의 모순이 없고 일관된 상태로 복구시키는 것이다. 
- 핵심 원리는 데이터 중복이다. 데이터를 별도의 장소에 미리 복사해두고 장애로 문제가 발생하였을 때 복사본을 이용해 원래 상태로 복원하는 것이다. 
  - dump : 데이터베이스 전체를 다른 저장장치에 주기적으로 복사하는 방법
    - 정해진 주기에 따라 수행한다. 디스크와 같은 비휘발성 저장장치에 데이터베이스 저장본을 저장한다
  - log : 데이터베이스에서 변경 연산이 실행될 때마다 데이터를 변경하기 이전 값과 변경한 이후의 값을 별도의 파일에 기록하는 방법. 

<br>

> Q. 장애 발생했을 때 데이터베이스를 복구하는 연산을 설명하라

- redo : 가장 최근에 저장한 데이터베이스 복사본을 가져온 후 로그를 이용해 복사본이 만들어진 이후에 실행된 모든 변경 연산을 재실행하여 장애가 발생하기 직전의 데이터베이스 상태로 복구한다 (전반적으로 손상된 경우에 사용)
- undo : 로그를 이용해 지금까지 실행된 모든 변경 연산을 취소하여 데이터베이스를 원래 상태로 복구하는 방법. 

<br>

> Q. 데이터베이스의 로그 분석에 대해서 설명하라. 

- 로그를 저장한 파일을 로그 파일이라고 한다. 레코드 단위로 기록이된다. 로그가 지워지지 않도록 데이터 손실이 발생하지 않는 저장장치에 저장해둔다. 
- <T, start> : 트랜잭션 T 가 수행을 시작했음 
- <T, X, old_value, new_value> : 트랜잭션 T 가 데이터 X를 이전값에서 새로운 값으로 변경하는 연산을 실행
- <T, commit> : 트랜잭션 T가 성공적으로 완료되었음을 기록
- <T, abort> : 트랜잭션 T가 철회되었음을 기록

<br>

> Q. 데이터베이스 회복기법에 대해 설명하라

- 데이터베이스 회복 기법으로 로그회복, 검사시점 회복, 미디어 회복 기법이 있다. 

<br>

> Q. 로그 회복 기법에 대해 설명하라

- 즉시 갱신 회복 기법 
  - 트랜잭션 수행 중에 데이터를 변경한 연산의 결과를 데이터베이스에 즉시 반영한다. 
  - 장애 발생에 대비 하기 위해 데이터 변경에 대한 내용을 로그 파일에도 기록한다. 
  - 트랜잭션이 완료되기 전에 장애가 발생한 경우 undo연산을 실행한다. 만약 트랜잭션이 완료된 후에 장애가 발생한 경우 redo연산을 실행한다.
  - redo와 undo연산이 모두 필요할 때는 undo연산을 먼저 실행한 후 redo연산을 실행한다.  
- 지연 갱신 회복 기법 
  - 트랜잭션이 수행되는 동안에 데이터 변경 연산 결과를 즉시 반영하지 않고 로그파일에만 기록해두는 것이다. 트랜잭션이 부분완료된 이후 로그에 기록된 내용을 이용해 한번에 반영한다. 
  - undo 연산은 필요없고 redo연산만 필요하므로 로그 레코드에 변경 이전 값을 기록할 필요가 없다. 

<br>

> Q. 검사시점 회복 기법에 대해 설명하라

- 로그 전체를 대상으로 회복 기법을 적용하면 너무 많은 시간이 걸리는 비효율성이 발생한다
- 검사 시점 회복 기법은 로그 기록을 이용하되 일정 시간 간격으로 검사 시점을 만들어둔다.
- 장애가 발생하면 가장 최근 검사 시점 이전의 트랜잭션에는 회복 작업을 수행하지 않고, 이후의 트랜잭션에만 회복 작업을 수행한다. 
- <Checkpoint L> 형식에서 L은 현재 실행되고 이쓴 트랜잭션의 리스트를 의미한다. 

<br>

> Q. 미디어 회복 기법에 대해 설명하라

- 디스크에 발생할 수 있는 장애에 대비한 회복 기법을 의미한다 
- DB의 내용을 일정 주기마다 다른 안전한 장치에 dump해두는 것이다. 그 다음 필요에 따라 로그의 내용을 토대로 redo 연산을 실행한다. 
- CPU가 낭비된다는 단점이 있다. 

<br>

## 3. 병행 제어 

> Q. 병행 수행과 병행 제어에 대해 설명하라. 

- 병행수행 : DBMS는 여러 사용자가 동시에 공유하고 여러개의 트랜잭션이 동시에 수행될 수 있다. 트랜잭션이 차례로 번갈아 진행되는 인터리빙 방식으로 진행된다. 
- 같은 데이터에 접근하여 연산을 실행하더라도 문제가 발생하지 않고 정확한 수행 결과를 얻을 수 있도록 트랜잭션의 수행을 제어하는 것을 병행제어라고 한다(동시성제어)

<br>

> Q. 병행 수행의 갱신분실, 모순성, 연쇄복귀에 대해 설명하라

- 갱신분실 
  - 하나의 트랜잭션이 수행한 데이터 변경 연산의 결과를 다른 트랜잭션이 덮어써 변경연산이 무효화 되는 것이다. 
  - 트랜잭션의 read(), write()가 하나의 트랜잭션에서 수행되고 순차적으로 실행되어야 갱신 분실 문제가 발생하지 않는다. 
- 모순성 
  - 하나의 트랜잭션이 여러 개의 데이터 변경 연산을 실행할 때 일관성 없는 상태의 데이터베이스에서 데이터를 가져와 연산을 실행함으로써 모순된 결과가 발생하는 것이다. 
- 연쇄 복귀 
  - 트랜잭션이 완료되기 전에 장애가 발생하여 rollback 연산을 수행하면 이 트랜잭션이 장애발생 전에 변경한 데이터를 가져가 변경 연산을 실행한 또다른 트랜잭션에도 rollback연산을 연쇄적으로 실행해야 한다는 것이다. 
  - 그런데 이미 완료된 트랜잭션에 대해 rollback이 실행될 수 없는 문제가 발생한다. 

<br>

> Q. 트랜잭션 스케줄애 대해 설명하라

- 트랜잭션에 포함되어있는 연산을 수행하는 순서이다. 트랜잭션은 많은 연산이 포함되어있어 각 연산을 실행하는 순서인 트랜잭션 스케줄도 여러가지가 있다. 
- 직렬스케줄, 비직렬 스케줄, 직렬가능 스케줄이 있다. 

<br>

> Q. 직렬스케줄, 비직렬 스케줄, 직렬 가능 스케줄에 대해 설명하라. 

- 직렬 스케줄 : 트랜잭션 별로 연산들을 순차적으로 실행하는 것이다. 
  - 모든 트랜잭션이 완료될 때 까지 다른 트랜잭션의 방해를 받지 않고 독립적으로 수행된다. 
  - 같은 트랜잭션들을 대상으로 하더라도 트랜잭션의 수행 순서에 따라 최종 결과가 달라질 수 있다. 
  - 그러나 인터리빙 방식을 사용하지 않고 각 트랜잭션을 독립적으로 수행하기 때문에 트랜잭션들이 동시에 수행되는 병행수행이라고 할 수 없다. 잘 사용하지 않는 방법. 
- 비직렬 스케줄 : 인터리빙 방식을 이용하여 트랜잭션을 병행해서 수행시키는 것이다. 
  - 트랜잭션이 돌아가면서 연산을 실행하기 때문에 하나의 트랜잭션이 완료되기 전에 다른 트랜잭션의 연산이 실행되어 갱신분실, 모순성, 연쇄복귀 등의 문제가 발생할 수 있고 최종 수행 결과의 정확성을 보장할 수 없다. 
- 직렬 가능 스케줄 : 직렬 스케줄에 따라 수행한 것 같이 정확한 결과를 생성하는 비직렬 스케줄을 의미한다. 
  - 직렬 가능 스케줄인지 여부를 파악하는 것이 어렵다. 
  - 그래서 직렬 가능성을 보장하는 병행제어 기법을 사용한다

<br>

> Q. 병행 제어 기법의 기본 원리를 말하라

- 모든 트랜잭션이 따르면 직렬 가능성이 보장되는 규약을 정의하고 트랜잭션들이 규약을 따르도록 하는 것이다. 

<br>

> Q. 로킹 기법에 대해 설명하라 

- 트랜잭션들이 동일한 데이터에 동시에 접근하지 못하도록 lock과 unlock연산으로 제어한다 
- 로킹 단위가 커질수록 병행성이 낮아지지만 제어가 쉽고, 로킹 단이가 작아질수록 제어가 어렵지만 병행성이 높아진다 
  - lock연산 : 트랜잭션이 데이터에 대한 독점권을 요청하는 연산
  - unlock연산 : 트랜잭션이 데이터에 대한 독점권을 반환하는 연산

<br>

> Q. 2단계 로킹 규약에 대해 설명하라

- 트랜잭션이 lock과 unlock연산을 확장 단계와 축소 단계로 나누어 수행해야 한다. 모든 트랜잭션이 2단계 로킹 규약을 준수하면 해당 스케줄은 직렬 가능성을 보장받는다 
  - 확장 단계 : 트랜잭션이 lock연산만 실행할 수 있고, unlock연산은 실행할 수 업는 단계
  - 축소 단계 : 트랜잭션이 unlock연산만 실행할 수 있고 lock연산은 실행할 수 없는 단계 

<br>

# CH11 보안과 권한관리 


## 

> Q. 

- 

<br>

##


> Q.  

- 
<br>


> Q.  

- 
<br>

##


> Q.  

- 
<br>
