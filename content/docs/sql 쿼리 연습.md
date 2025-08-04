---
title: "sql문 연습"
date: "2025-08-04"
draft: false
---


https://solvesql.com/problems/publisher-with-many-games/

```sql
SELECT
  companies.name,
  publisher_id,
  COUNT(*) AS publishing_count
FROM
  games
  JOIN companies ON games.publisher_id = companies.company_id
GROUP BY
  publisher_id
HAVING
  COUNT(*) >= 10;
```

```sql
SELECT * FROM orders
JOIN order_items product ON product.order_id = orders.order_id
LIMIT 3; -- 3줄 까지만 출력
```

https://solvesql.com/problems/max-row/
```sql
SELECT
  id -- id만 출력..
FROM points
  WHERE x = (SELECT MAX(x) FROM points) -- 서브쿼리
  OR y = (SELECT MAX(y) FROM points) -- 서브쿼리
  ORDER BY id ASC; -- 오름차순 정렬
```

https://solvesql.com/problems/weekday-stats-airpollution/
```sql
SELECT 
-- `CASE` 문으로 숫자를 한글 요일로 변환
  CASE strftime('%u', measured_at)
    WHEN '1' THEN '월요일'
    WHEN '2' THEN '화요일'
    WHEN '3' THEN '수요일'
    WHEN '4' THEN '목요일'
    WHEN '5' THEN '금요일'
    WHEN '6' THEN '토요일'
    WHEN '7' THEN '일요일'
  END AS weekday,
  -- ROUND(..., 4) : 소수점 넷째 자리까지 반올림
  ROUND(AVG(no2), 4) AS no2,
  ROUND(AVG(o3), 4) AS o3,
  ROUND(AVG(co), 4) AS co,
  ROUND(AVG(so2), 4) AS so2,
  ROUND(AVG(pm10), 4) AS pm10,
  ROUND(AVG(pm2_5), 4) AS pm2_5
FROM measurements
GROUP BY strftime('%u', measured_at)
ORDER BY strftime('%u', measured_at);
```

- `%w`는 일요일을 0으로 시작하는 요일 숫자 (0~6)
- `%u`는 월요일을 1로 시작하는 요일 숫자 (1~7) → 우리가 원하는 요일 순서에 딱 맞아요!

%w 나.. %u 이런 게... 오라클 작성할 때도 동일한가?

`%w`, `%u` 같은 포맷은 **SQLite나 PostgreSQL** 등 일부 DB에서 사용하는 `strftime()` 함수의 포맷이에요. **오라클(Oracle)**에서는 방식이 조금 달라요.
### 🏛️ 오라클에서 요일 숫자 구하는 방법

오라클에서는 `TO_CHAR()` 함수를 사용해서 날짜의 요일을 숫자로 변환해요:
```sql
SELECT TO_CHAR(SYSDATE, 'D') AS 요일숫자 FROM dual;
```

- `'D'`는 **요일을 숫자로 반환**하는 포맷이에요.
- 반환되는 숫자는 **NLS_TERRITORY 설정**에 따라 달라질 수 있어요.
    - 예: `1 = 일요일`, `2 = 월요일` … 이런 식으로 시작 요일이 달라질 수 있어요.
- ### 📌 요일을 한글로 바꾸고 싶다면?

```sql
SELECT 
  TO_CHAR(SYSDATE, 'D') AS 요일숫자,
  DECODE(TO_CHAR(SYSDATE, 'D'),
         '1', '일요일',
         '2', '월요일',
         '3', '화요일',
         '4', '수요일',
         '5', '목요일',
         '6', '금요일',
         '7', '토요일') AS 한글요일
FROM dual;
```

이렇게 `DECODE()`를 활용하면 숫자를 한글 요일로 매핑할 수 있어요.