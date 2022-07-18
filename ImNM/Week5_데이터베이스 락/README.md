# 데이터 베이스

## 락

### 락이란?

- 데이터 무결성 및 일관성이 깨지지 않도록
  여러 사용자들이 존재하는 환경에서 동시성 제어를 위해서 필요

벤더사 마다 락의 종류와 전략이 좀 다름
my sql InnoDB 에 초점 맞추서 설명

---

## 락의 종류

![락 컨플릭트 종류](https://user-images.githubusercontent.com/13329304/179574399-1e59d145-19f5-4eed-9497-bd41b1a4bd0a.jpg)

### 배타 잠금

한 트랜잭션에서 읽어간 데이터를 다른 트랜잭션에서 배타적으로 읽거나, 수정하기 위해 락을 획득 하려고 할때
기다리게 만들고 싶으면

가령 어떤 값을 읽어와서 + 1 하는 로직을 하고싶은데
밑에 공유 잠금처럼 읽어 갈수 있게 해버리면 중복 업데이트가 일어날 수 있음!

exclusive locks -- write lock -- X Lock 이라고도함.

내가 수정하고자하는 것에 대해 다른사람이 못건들도록 막아버리는것.

수정 쿼리를 날릴 때 각 row에 걸리는 락

x lock 이 걸려 있으면 다른 트랜잭션은 S lock , X lock 둘다 걸 수 없다.

---

### 공유 잠금

한 트랜잭션에서 읽어간 데이터를 다른 트랜잭션에서 배타적으로 수정하기 위해 락을 획득하려 할때 (읽어가는 것은 허용) 기다리게 만들고 싶다면

shared Locks -- read lock -- S lock 이라고도함

리드 작업을 수행할 때 사용
S lock이 걸려있는 row에 대해 다른 트랜잭션이 S lock을 걸 수 있으나 배타잠금(X) lock 은 못걸음

```sql
// in ~~ mode 를 적어주면 트랜잭션을 획득 할 수 있음
select * from booth where booth_no = 0 lock in share mode;

update booth set name = 'pick git' where booth_no = 0;

select * from information_schema.INNODN_LOCKS;
```

---

! 사실 테이블 자체에도 락이 있음 row-level 에걸릴 락을 관리하는 역할을 함

### 레코드 락 ( Record Lock )

이노 디비 상에서 레코드락은 row가 아닌 db index record 에 걸리는 락

테이블에 index가 없다면?
숨겨저 있는 clustered index을 사용하여 레코드를 잠군다.

> 잠깐 클러스터 인덱스란?

![클러스터 인덱스란?](https://user-images.githubusercontent.com/13329304/179574397-6d0b1b36-94fd-4788-aa49-4760c0fe23f1.jpg)
pk 값이라고 생각하면될듯?? 실제 데이터베이스 까지 갔다오는게아니라 아예 모든 값들이 메모리에 올라와있음

```sql
SELECT COUNT(*) FROM employees WHERE first_name='DK'; -- 250건 조회된다고 가정

SELECT COUNT(*) FROM employees WHERE first_name='DK' AND last_name='J'; -- 1건 조회된다고 가정

UPDATE employees SET hire_date=NOW() WHERE first_name='DK' AND last_name='J'; --어디에 락이 걸릴까?
출처: https://jeong-pro.tistory.com/241 [기본기를 쌓는 정아마추어 코딩블로그:티스토리]

```

250개의 레코드 전부에 락이 걸려 버린다.

---

### 갭 락 ( Gap Locks )

gap 이란 index record 가 없느 ㄴ부분
조건에 해당하는 새로운 row 가 추가되는 것을 방지하기 위한 것

```sql
select booth_no form booth where booth_no betwwen 1 and 6 for update;
// 명시적인 갭 락 선언
```

---

## 데드락

![데드락점유대기](https://user-images.githubusercontent.com/13329304/179574389-d532aeca-5bc9-4069-9574-1d20effc587d.jpg)
서로의 자원을 점유 대기 하는 과정속에서 풀어낼수없는 상황에 걸린것.

데이타베이스 레벨에서 데드락 탐지기능도 존재하고 트랜잭션타임아웃도 존재해서 괜찮음
다만 이럴때 트랜잭션이 롤백 취소 되어 버리기때문에 어플리케이션 레벨에서 처리해줘야함

출처 : https://www.youtube.com/watch?v=Ry_gB34cvwc

---

### 데드락 케이스

![데드락](https://user-images.githubusercontent.com/13329304/179574401-346caac4-a560-4ccb-a32f-00ff39c35f5e.jpg)
upsert 사용 있으면 업데이트 없으면 insert
where 절로 여러 row 동시에 업데이트 하는 경우

```sql
INSERT INTO my_table (pk, name) VALUES(1, 'a'), (2,'b') ON DUPLICATE KEY UPDATE name=VALUES(name);
INSERT INTO my_table (pk, name) VALUES(1, 'a'), (2,'b') ON DUPLICATE KEY UPDATE name=VALUES(name);
출처:  https://www.letmecompile.com/mysql-innodb-lock-deadlock/
```

- A에서 pk=1인 row upsert pk=1 lock 획득
- B에서 pk=2인 row upsert pk=2 lock 획득
- A에서 pk=2인 row upsert 시도 -> B에서 먼저 lock을 가져갔기때문에 대기
- B에서 pk=1인 row upsert 시도 -> A에서 먼저 lock을 가져갔기때문에 대기

---

## advanced

락 종류 두개정도 더있음 next lock 등...
auto increment lock 도 존재함 -> id 가 자동 증가할때에 설정값에 따라
동일 sql 쿼리가 다른 index 순서를 가질 때가 있음

https://www.letmecompile.com/mysql-innodb-lock-deadlock/

https://jeong-pro.tistory.com/241#:~:text=%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%20%3A%20%EB%85%BC%EB%A6%AC%EC%A0%81%EC%9D%B8%20%EC%9E%91%EC%97%85%20%EB%8B%A8%EC%9C%84,%EB%B3%B4%EC%9E%A5%ED%95%98%EA%B8%B0%20%EC%9C%84%ED%95%9C%20%EA%B8%B0%EB%8A%A5%EC%9D%B4%EB%8B%A4.
