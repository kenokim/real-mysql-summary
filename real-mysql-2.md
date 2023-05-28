## Chapter11. SQL queries & optimization

IN 연산자는 equality 연산자 여러 개를 묶어 사용하는 것으로, 같은 형태로 index 를 사용한다. 따라서 index 를 최적으로 사용한다.

IN (?,?,?) : scala 비교인 경우 = 연산자와 같다. tuple 비교인 경우 8.0 이전 버전에서는 성능 이슈가 있었다. sub-query 비교의 경우 8.0 버전부터는 성능이 최적화되었다.

explain 으로 type 을 확인하면 range 가 뜬다.

PK 는 unique 해야 하며 여러 column 을 PK 로 쓸 수 있다.


SELECT, FROM, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT

각각은 clause 라고 하며 어떤 게 먼저 처리되는지 알아야 결과값을 알 수 있다.

WHERE, GROUP BY, ORDER BY 는 index 를 사용할 수 있으며 값 그대로 비교해야 한다.


- COUNT : 레코드의 건수를 반환한다. (ex) COUNT(PK), COUNT(*), COUNT(1)
  - order by, left join 과 같이 레코드 건수와 무관한 작업을 포함하지 않아야 한다.
  - index 를 적절히 사용하지 않는 count 는 부하가 많이 걸린다.

- outer join 은 inner join 보다 부하가 많이 걸리므로, full-scan 쿼리가 포함되므로, 필요하지 않은 경우에는 inner join 으로 하자.

- group by 는 특정 column 의 값으로 레코딩을 그루핑하고, 그룹별로 집계된 결과를 하나의 레코드로 조회한다.
  - group by 대상 column 에 index 를 사용하지 않을 경우 filesort 가 필요하다. 
  - group by 의 대상 column 이 여러 개일 경우 인덱스가 없으면 사용하지 않는다. A, B 로 group by 를 걸 경우, index A B 이거나 index A, index B 가 있어야 한다.

쿼리의 성능을 테스트 할 때, 먼저 실행 계획을 보고 문제가 있는지 판단한 후 버퍼, 캐시 등을 고려하여 실행하여 체크한다.


