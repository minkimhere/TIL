## [SQL] IS NULL 연습

[REFERENCE : PROGRAMMERS > 값이 없음을 의미하는 NULL. NULL을 처리하는 방법을 배워봅니다.](https://programmers.co.kr/learn/courses/30/parts/17045)

### 이름이 없는 동물의 아이디
```sql
SELECT ANIMAL_ID FROM ANIMAL_INS
WHERE NAME IS NULL
ORDER BY NAME
```

### 이름이 있는 동물의 아이디
```sql
SELECT ANIMAL_ID FROM ANIMAL_INS
WHERE NAME IS NOT NULL
```

### NULL 처리하기
```sql
SELECT 
    ANIMAL_TYPE,
    CASE 
        WHEN NAME IS NULL THEN 'No name' 
        ELSE NAME 
    END AS NAME,
    SEX_UPON_INTAKE
FROM ANIMAL_INS
```