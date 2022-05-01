## [SQL] SUM, MAX, MIN 연습

[REFERENCE : PROGRAMMERS > 나이가 가장 많은 사람은 누구일까? 테이블을 뒤져 통계를 내어봅시다.](https://programmers.co.kr/learn/courses/30/parts/17043)

### 최댓값 구하기
- 방법1
```sql
SELECT DATETIME AS '시간' FROM ANIMAL_INS
ORDER BY DATETIME DESC
LIMIT 1
```

- 방법2
```sql
SELECT MAX(DATETIME) AS '시간' FROM ANIMAL_INS
```

### 최솟값 구하기
- 방법1
```sql
SELECT DATETIME AS '시간' FROM ANIMAL_INS
ORDER BY DATETIME
LIMIT 1
```

- 방법2
```sql
SELECT MIN(DATETIME) AS '시간' FROM ANIMAL_INS
```

### 동물 수 구하기
```sql
SELECT COUNT(*) as 'count' FROM ANIMAL_INS
```

### 중복 제거하기
- 방법1
```sql
SELECT COUNT(DISTINCT NAME) as count FROM ANIMAL_INS
```

- 방법2
```sql
SELECT COUNT(*) AS count
    FROM
    (
    SELECT COUNT(*) AS count FROM ANIMAL_INS
    WHERE NOT NAME = 'NULL'
    GROUP BY NAME
    ) AS c
```