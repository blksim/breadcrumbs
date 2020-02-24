참고 링크<br>
https://www.postgresql.org/docs/9.1/functions-conditional.html<br>
https://www.postgresql.org/docs/9.1/queries-with.html<br>

조건 별 조회 기능을 개발하다가, 서비스단에서 분기 별 쿼리를 따로 만들어야 할 지 db 쿼리 내에서 해결해야 할 지 여쭤봤는데
(당연히 쿼리로 해결하는 게 손쉬운 방법이었고) 힌트를 받았다. 복습<br>

```
with condition as (
   select COALESCE(null, '') as search_word
        , to_timestamp(COALESCE(null, '2020-01-01'), 'YYYY-MM-DD') as search_fr
        , to_timestamp(COALESCE(null, '2120-01-01'), 'YYYY-MM-DD') as search_to
)
select *
from   condition x
     , alarm_info y
where  y.alarm_msg like '%' || x.search_word || '%'
and    y.occur_date between x.search_fr and x.search_to
```
- condition이라는 가상의 테이블을 정의하여
- search_word, search_fr, search_to 를 조회하겠다.
- 여기서 search_word는 null일 경우 ''
- search_fr은 null일 경우 '2020-01-01'
- search_to는 null일 경우 '2120-01-01'로 치환하여 'YYYY-MM-DD' 포맷으로 변환한다.
- 앞에서 정의한 condition as x와 alarm_info as y 테이블을 전체 조회한다.
- 단 y.alarm_msg가 x.search_word를 포함해야 한다.(||는 문자열 결합 연산자)
- 또 y.occur_date가 x.search_fr에서 x.search_to 사이여야 한다.

### `with` expression
`WITH`은 긴 쿼리를 위한 보조 구문으로 쓰인다. 
이 구문은 주로 한 쿼리를 위한 임시 테이블을 정의하는 데 쓰인다.
`SELECT`, `INSERT`, `UPDATE`, 혹은 `DELETE`을 보조하거나,
그 자체로 주요 구문으로서 `SELECT`, `INSERT`, `UPDATE`, 혹은 `DELETE`와 함께 쓰일 수 있다.

```
WITH regional_sales AS (
  SELECT region, SUM(amount) AS total_sales
  FROM orders
  GROUP BY region
), top_regions AS (
  SELECT region
  FROM regional_sales
  WHERE total_sales > (SELECT SUM(total_sales)/10 FROM regional_sales)
)
SELECT region, product, SUM(quantity) AS product_units,
SUM(amount) AS product_sales
FROM orders
WHERE region IN (SELECT region FROM top_regions)
GROUP BY region, product;
```

**데이터 수정 구문 속 `WITH`**
```
WITH moved_rows AS (
  DELETE FROM products
  WHERE
    "date" >= '2010-10-01' AND
    "date" < '2010-11-01'
  RETURNING *
)
INSERT INTO products_log
SELECT * FROM moved_rows;
```
`INSERT`, `UPDATE`, `DELETE`와 `WITH`을 함께 사용하면 여러 다른 명령을 같은 쿼리에서 실행할 수 있다.
위 쿼리는 products의 rows를 products_log으로 옮겨준다.
`WITH` 내부의 `DELETE`는 특정한 조건을 만족하는 rows를 삭제하고, `RETURNING` 절과 함께 내용을 반환한다. 
그러고 나서 아래의 주요 쿼리가 결과를 읽은 다음 products_log에 삽입한다.

데이터 조작을 위한 구문은 가장 상위 수준의 구문인 `WITH`절 내에서만 허용된다.
`WITH` 내부의 데이터 조작 구문은 대개 `RETURNING` 절을 갖고 있다. 이는 타깃 테이블이 아닌 남은 쿼리에 의해 참조될 수 있는 임시 테이블을 구성한다.
만약 `RETURNING` 절이 빠져있다면, 나머지 쿼리가 임시 테이블을 참조할 수 없다.

### `COALESCE` function
```
COALESCE(value[, ...])
```
COALESCE 함수는 null이 아닌 첫 번째 인자를 반환한다. null은 모든 인자가 null일 때만 반환된다.
이는 종종 데이터가 갱신돼야 할 때 null 값을 위한 기본 값으로 대체되어 쓰인다.
예를 들어,
```
SELECT COALESCE(description, short_description_ '(none)') ...
```
CASE문 처럼, COALESCE는 오직 결과를 결정하는 인자만을 평가한다. 즉, 오른쪽에 있는 null이 아닌 인자는 평가되지 않는다는 것이다.
이는 SQL 표준 함수이고 다른 DB에서 쓰이는 NVL, IFNULL과 비슷한 효과가 있다.
