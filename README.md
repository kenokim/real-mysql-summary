## Chapter 4. 아키텍처

Mysql 은 mysql engine 과 storage engine 으로 구성되어 있다.

Storage engine 은 플러그인 모델로 여러 엔진을 플러그인해서 사용할 수 있다.

Storage engine 에는 InnoDB, MyISAM, Memory 엔진 등이 있다.

### 쿼리 실행 구조

1. 쿼리 파서 : SQL 쿼리를 tree 구조의 token 으로 만든다.

2. preprocessor : 파서 트리의 구조적인 문제점을 확인한다. 객체의 존재 여부와 권한을 체크한다.

3. 옵티마이저 : 쿼리를 효율적으로 수행할 방법을 찾는다.

4. Query Executor : storage engine 의 handler api 를 기반으로 쿼리를 수행한다. 

5. Storage engine : handler api 를 제공하며 이는 commit, delete, read, write, 등이 있다.





### 2. InnoDB Storage Engine

(1). PK clustering

InnoDB 는 PK 가 클러스터링 인덱스이다. 인덱스를 쓰면 select ~ where, order by, min/max 등에서 성능 향상을 얻을 수 있다.

Secondary index 의 경우 PK 를 논리적인 주소로 사용한다.

MyISAM 은 클러스터링 키가 지원되지 않는다.

(2). Foreign key

InnoDB 는 FK 기능을 지원하는데, 이는 장단점이 있는 기능이다.

해당 컬럼에 인덱스를 생성해야 하고, 변경 시 데이터를 체크하여 lock 이 여러 테이블로 전파된다.

foreign_key_checks 옵션으로 FK 체크를 OFF 할 수 있다.

(3). MVCC

Multi version concurrency control 은 record 의 버전을 여러개로 관리해서 동시성 이슈를 관리한다.

InnoDB 에서는 undo log 를 통해 이를 구현하며, update 쿼리를 실행할 경우 기존 데이터를 undo log 에 옮기고, buffer pool 의 값은 업데이트한다. 다른 스레드가 이러한 commit 되지 않은 레코드를 조회할 경우 READ_COMMITED 의 경우 undo log 값을, READ_UNCOMMITTED 의 경우 buffer pool 의 값을 읽는다.

(4). Non-locking consistent read

MVCC 를 통해 조회 기능의 경우 lock 을 사용하지 않아도 된다.

(5). Automatic deadlock detection

감시 스레드가 deadlock 을 감시하며 undo log 가 적은 트랜잭션을 강제 종료한다.

(6). Automatic disaster recovery

Mysql 서버 시작 시 incomplete transaction, partial write 에 대해 recovery 를 수행한다.

이는 undo log, redo log 등 로그 파일을 읽는 방식이다.

(7). Buffer pool

InnoDB 버퍼 풀은 디스크의 파일이나 데이터를 in-memory 에 캐시하는 곳이다. 쓰기 작업을 지연시켜 batch 처리할 수 있다.

innodb_buffer_pool_size -> 버퍼 풀의 크기는 dynamic 확장이 가능하다.

innodb_buffer_pool_instances -> 버퍼 풀은 세마포어로 관리하므로 버퍼 풀을 분산하면 lock racing 을 분산할 수 있다.










옵티마이저는 사용자가 쿼리를 최적으로 처리될 수 있게 하는 실행 계획을 수립한다.

하지만 항상 최적은 아니므로, explain 명령어로 실행 계획을 확인할 수 있게 해준다.



## Chapter 9. 옵티마이저와 힌트

Mysql 에서는 옵티마이저를 통해 데이터의 분포를 고려하여 최적의 query plan 을 수립한다.

EXPLAIN 커맨드를 통해 query plan 을 확인할 수 있다.

최근의 Mysql 에서는 cost-based optimizer 를 사용하고, 이는 테이블의 통계 정보를 통해 query plan 별 cost 를 계산한 뒤 선택하는 방식이다.

### 2. 기본 데이터 처리

Full table scan vs full index scan

mysql optmizer 는 다음의 조건에 대해 full-table scan 을 선택한다.

1. 테이블의 레코드가 너무 작은 경우
2. where 나 on clause 에 index 를 이용할 조건이 없는 경우
3. 일치하는 record 건수가 너무 많은 경우


### 3.
1.9. 세미 조인
다른 테이블과 조인을 수행하지는 않고, 데이터가 있는지만 체크하는 형태의 쿼리를 semi join 이라고 한다.

Mysql 5.7 이전에는 최적화가 안 된 취약한 쿼리였지만, 8.0 에서는 semijoin optimizer option 을 보면된다..


## Chapter 10. 실행 계획

Mysql 5.7 버전까지 테이블과 인덱스에 대한 개괄적인 정보로 실행 계획을 수립했다.

Mysql 8.0 버전부터는 인덱스되지 않은 칼럼들에 대해서도 데이터 분포도를 수집해서 저장하는 histogram 정보가 도입됬다.

Explain 은 테이블 포맷, JSON, Tree 포맷이 있다.

select_type 은 쿼리의 종류를 보여준다.
