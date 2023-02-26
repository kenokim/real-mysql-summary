
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
