# MySQL Architecture

## MySQL 구조

<img width="657" alt="스크린샷 2022-07-12 오후 1 48 53" src="https://user-images.githubusercontent.com/59060780/178410819-f408e45f-8f36-4ecf-be29-5c11bf01bfb4.png">

* MySQL 접속 클라이언트
  * 다양한 언어에서 접속 가능케하는 API / 쉘 스크립트를 통해 접속 할 수 있게해주는 역할

* MySQL 엔진
  * 클라의 접속과 SQL 요청을 처리해준다.
  * 쿼리 파서 / 전처리기 / 옵티마이저 / 실행 엔진
  * 옵티마이저: SQL 요청문을 최적화 하기 위해 실행 계획을 짜는 역할을 수행
  
* MySQL 스토리지 엔진
  * 디스크에 정보를 저장 / 디스크에 저장된 정보를 읽어오는 역할
  
* 운영체제와 하드웨어
  * 실제 테이블의 데이터와 로그 데이터를 파일로 저장하는 부분
  
## 쿼리 실행 과정

<img width="291" alt="스크린샷 2022-07-12 오후 1 55 21" src="https://user-images.githubusercontent.com/59060780/178411606-d3401aac-2338-4c30-aafe-453c40c3069c.png">

* 쿼리 캐시
  * SQL 요청문의 실행 결과를 메모리에 캐싱하는 역할
  * 하지만, 테이블의 데이터가 변경될 경우 캐시에 있는 데이터를 삭제해야하므로 성능이 저하 됨 == 캐시에 접근하는 쓰레드 자체에 Lock이 걸리게 되므로 (동시 처리 성능 저하) => 8.0버전 부터 제거
  
* 쿼리 파서
  * SQL 문장을 토큰으로 쪼개서 파스 트리로 만듦 (프로그래밍 언어론에서 배운 파스 트리 구현 방식과 비슷)
  * 파스 트리로 쪼개는 과정에서 SQL 문법 오류를 탐지하고 에러 처리
  
* 전처리기
  * 쿼리 파서에서 생성한 파스 트리를 기반으로 SQL 요청문의 문장 구조 체크
  * 토큰이 유효한지 체크 (Ex. 실제 존재하는 테이블 명인지, 해당 테이블에 컬럼 또는 레코드가 실제로 존재하는지, 접근할 수 있는 값인지)
  
* 옵티마이저
  * SQL 요청문을 최적화해서 실행 계획을 수립
  * 최적화 방법 2가지
  * 규칙 기반 최적화 (내장된 우선순위에 따라 실행 계획 수립)
  * 비용 기반 최적화 (작업의 비용과 테이블의 통계 정보를 활용)

* 쿼리 실행 엔진
  * 옵티마이저에서 수립된 실행 계획을 따라 스토리지 엔진 호출하고 레코드 쓰기 / 읽기 동작 수행

* 스토리지 엔진
  * 쿼리 실행 엔진이 요청하는대로 데이터를 디스크에 읽거나 씀
  * 핸들러 API에 의해 동작한다.
  * 플러그인 형태로 제공 (플러그인은 한계 (문제점)이 존재하기 때문에 8.0버전 부터는 플러그인 형식 아키텍쳐가 아닌 컴포넌트 형식의 아키텍쳐로 변경)

## InnoDB 스토리지 엔진

### 특징
#### PK에 의한 클러스터링
 * 클러스터링: 개체들이 주어졌을 때 (여기서는 한 레코드 내부의 컬럼 값들) 개체들을 부분 그룹으로 나누는 것 (Pk를 이용해 레코드로 매핑 시킴)
 * 생성된 레코드들을 PK순으로 정렬시킨다.
 * 따로 지정해주지 않아도 PK인덱스를 자동으로 생성해줌
 * 레코드에 접근할 때는 PK를 통해서만 접근 가능
 * 범위 검색이 매우 빠르다 (PK를 이용하기 때문)
 * PK를 바꾸는 경우 순서가 모두 바뀌기 때문에 성능 저하 문제
 * 대부분의 웹 서비스에서는 읽기 요청이 쓰기 요청보다 많기 때문에 쓰기 성능을 희생하고 읽기 성능을 높여주는 InnoDB 스토리지 엔진의 클러스터링 사용

#### 트랜젝션 지원
 * InnoDB 버퍼풀: 변경된 데이터를 디스크에 반영하기 전까지 잠시 보관하는 공간
 * 언두 로그(Undo Log): 롤백할 데이터 즉 변경 전의 데이터를 잠시 보관하는 공간

<img width="440" alt="스크린샷 2022-07-12 오후 2 29 14" src="https://user-images.githubusercontent.com/59060780/178415524-5c6f96e1-67be-4a03-88ac-5497cfa605a5.png">

 > 위 사진의 상황에서 `SELECT * FROM USER WHERE id = 1;` 의 쿼리로 조회 하는 경우는 어떤 값이 반환 될까? ==> DB에 설정된 트랜잭션의 격리 수준에 따라 다른값이 반환된다.

* 트랜잭션 격리 수준
  * READ_UNCOMMITTED: 커밋이 아직 되지 않은 데이터 즉, InnoDB 버퍼풀에 있는 데이터 (취미가 코딩)인 레코드가 조회 된다.
  * READ_COMMITTED / REATABLE_READ / SERIALAZABLE: 커밋 된 기록 (변경 쿼리 이전의 값) 즉, 언두 로그에 있는 레코드가 조회 된다.
  * MVCC: 위의 예시 처럼 트랜잭션 격리 수준에 따라 조회되는 값이 달라지는 기술 (다양한 버전이 동시에 관리된다.) => 레코드 잠금을 걸지 않아도 트랜잭션 격리 수준이 일정하다면 일관된 읽기 기능을 수행 할 수 있음

* 언두 로그 Vs 리두 로그
  * 언두 로그 (Undo Log): 변경되기 이전 데이터를 백업하고 트랜잭션의 롤백 기능 수행 시 언두 로그에 저장되어 있는 데이터를 복원한다. 
  * 리두 로그 (Redo Log): 변경된 데이터를 백업(Commit 완료된 데이터) ==> 영속성을 보장한다.

* 레코드 단위 잠금
  * 저장된 레코드의 데이터를 변경할 때는 동시성 문제때문에 레코드에 대한 접근을 막음 (잠금)
  * InnoDB 스토리지 엔진은 레코드 단위 정확하게는 인덱스에 대해 잠금을 실행하기 때문에 동시 처리 성능이 뛰어나다.
<img width="371" alt="스크린샷 2022-07-12 오후 2 42 18" src="https://user-images.githubusercontent.com/59060780/178417110-459b6d83-9ec0-43fd-bf40-38a35bd988b2.png">

> 인덱스를 pk로 걸었을 때는 박병욱을 찾기위해 모든 레코드가 잠긴다.

<img width="353" alt="스크린샷 2022-07-12 오후 2 42 38" src="https://user-images.githubusercontent.com/59060780/178417153-7ff753d7-1cab-4357-98f6-d694246cb5ea.png">

> 인덱스를 성 / 이름 두 개로 걸었을때는 박 병욱 레코드 하나만 잠기게 된다.

> 즉, 인덱스를 설정할 때는 신중하게 설정하자!

#### InnoDB 버퍼풀 / 어댑티스 해시 인덱스

* 쓰기 지연 버퍼
  * 변경된 데이터들을 버퍼풀에 모았다가 한꺼번에 디스크에 기록함.
  * JPA 영속성 컨텍스트의 쓰기 지연과 비슷하다.

* 데이터 캐싱
  * SQL 요청 결과의 인덱스 정보와 데이터 파일을 메모리에 캐싱한다.
  * 요청 결과의 테이블 데이터에 대해 페이징을 진행 (컴퓨터 구조에서 배운 가상메모리 페이징과 동일)
  * 페이지 교체 알고리즘: LRU (가장 오랫동안 사용되지 않은 페이지를 날림)

* 어댑티브 해시 인덱스
  * 페이지에 빠르게 접근하기 위한 해시 기반 인덱스
  * 자주 요청되는 페이지에 대해 InnoDB가 자동으로 생성해주는 인덱스 키와 페이지 주소 값을 매핑해준다.

