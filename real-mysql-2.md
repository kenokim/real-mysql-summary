## Chapter11. SQL queries & optimization

IN 연산자는 equality 연산자 여러 개를 묶어 사용하는 것으로, 같은 형태로 index 를 사용한다. 따라서 index 를 최적으로 사용한다.

IN (?,?,?) : scala 비교인 경우 = 연산자와 같다. tuple 비교인 경우 8.0 이전 버전에서는 성능 이슈가 있었다. sub-query 비교의 경우 8.0 버전부터는 성능이 최적화되었다.

explain 으로 type 을 확인하면 range 가 뜬다.

PK 는 unique 해야 하며 여러 column 을 PK 로 쓸 수 있다.


SELECT, FROM, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT

각각은 clause 라고 하며 어떤 게 먼저 처리되는지 알아야 결과값을 알 수 있다.

WHERE, GROUP BY, ORDER BY 는 index 를 사용할 수 있으며 값 그대로 비교해야 한다.
