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

## Chapter 10. 실행 계획

Mysql 5.7 버전까지 테이블과 인덱스에 대한 개괄적인 정보로 실행 계획을 수립했다.

Mysql 8.0 버전부터는 인덱스되지 않은 칼럼들에 대해서도 데이터 분포도를 수집해서 저장하는 histogram 정보가 도입됬다.
