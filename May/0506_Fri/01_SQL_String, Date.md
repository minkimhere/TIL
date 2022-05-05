## [SQL] String, Date 연습

[REFERENCE : PROGRAMMERS > 숫자는 어떻게 쓰는 건지 알겠는데, 글자와 날짜는 어떻게 다루지?](https://programmers.co.kr/learn/courses/30/parts/17047)

### 루시와 엘라 찾기
```sql
SELECT ANIMAL_ID, NAME, SEX_UPON_INTAKE FROM ANIMAL_INS
WHERE NAME IN ('Lucy', 'Ella', 'Pickle', 'Rogan', 'Sabrina', 'Mitty')
```

### 이름에 el이 들어가는 동물 찾기
```sql
SELECT ANIMAL_ID, NAME FROM ANIMAL_INS
 WHERE ANIMAL_TYPE = 'Dog' 
   AND NAME LIKE '%EL%'
 ORDER BY NAME
```

### 중성화 여부 파악하기
- 방법1
```sql
SELECT ANIMAL_ID, 
       NAME,
       CASE WHEN SEX_UPON_INTAKE LIKE 'Neutered%' 
              OR SEX_UPON_INTAKE LIKE 'Spayed%'
            THEN 'O'
            ELSE 'X'
        END AS 중성화
  FROM ANIMAL_INS
```

- 방법2
- REGEXP_LIKE 사용. REGEXP_LIKE(컬럼, 정규식). * LIKE 성능이 빨라서 LIKE를 할 수 있으면 LIKE 쓰는 게 좋다고 한다.
```sql
SELECT ANIMAL_ID, 
       NAME,
       CASE WHEN REGEXP_LIKE(SEX_UPON_INTAKE, 'Neutered|Spayed')
            THEN 'O'
            ELSE 'X'
        END AS 중성화
  FROM ANIMAL_INS
```

- 방법3
- MySQL 8.0 이상부터 REGEXP_LIKE가 생겼다고 함. 그 이전에는 REGEXP를 사용했다고 함.
```sql
SELECT ANIMAL_ID, 
       NAME,
       CASE WHEN SEX_UPON_INTAKE REGEXP 'Neutered|Spayed'
            THEN 'O'
            ELSE 'X'
        END AS 중성화
  FROM ANIMAL_INS
    
```

### 오랜 기간 보호한 동물(2)
```sql
SELECT i.ANIMAL_ID, i.NAME FROM ANIMAL_INS i
  LEFT JOIN ANIMAL_OUTS o USING (ANIMAL_ID)
 WHERE o.ANIMAL_ID IS NOT NULL
 ORDER BY i.DATETIME - o.DATETIME
 LIMIT 2
```

### DATETIME에서 DATE로 형 변환
- 방법1
```sql
SELECT ANIMAL_ID, NAME, SUBSTRING(DATETIME, 1, 10) AS 날짜 FROM ANIMAL_INS
ORDER BY ANIMAL_ID
```

- 방법2
```sql
SELECT ANIMAL_ID, NAME, DATE_FORMAT(DATETIME, '%Y-%m-%d') FROM ANIMAL_INS
ORDER BY ANIMAL_ID
```