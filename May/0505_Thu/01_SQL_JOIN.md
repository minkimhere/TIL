## [SQL] JOIN 연습

[REFERENCE : PROGRAMMERS > 테이블 사이의 복잡한 관계를 파악해보아요. 이제부터 어렵습니다.](https://programmers.co.kr/learn/courses/30/parts/17046)

### 없어진 기록 찾기
- in : 보호소에 들어온 동물들.  
out : 보호소에서 입양 보낸 동물들.  
보호소에서 들어온 동물들 기록이 사라짐. 보호소에서 입양 보낸 동물들 기록에는 ID가 있지만, 보호소에서 들어온 동물들 기록에는 ID가 없는 경우를 찾아낸다. 왜냐하면 입양을 가려면 무조건 보호소에 들어와야하기 때문이다.  

- 방법1
```sql
SELECT o.ANIMAL_ID, o.NAME FROM ANIMAL_OUTS o
LEFT JOIN ANIMAL_INS i
ON o.ANIMAL_ID = i.ANIMAL_ID
WHERE i.ANIMAL_ID IS NULL
ORDER BY i.ANIMAL_ID
```

- 방법2
- 만약 동물ID WHERE IN (...)이라면, ... 안에 있는  동물ID에 있는 것만 보고 싶다라는 뜻. WHERE IN ('ID1', 'ID2') 이런식으로 있따면, 동물ID 목록에서 ID1, ID2 만 보고 싶다는 뜻. 그래서 만약 보호소 동물ID를 구한 다음에 그 동물ID를 넣어주면, 그 동물ID들만 보고 싶다는 뜻. 그렇다면 반대로 WHERE NOT IN (...)을 한다면, 그 동물ID가 아닌 것만 보여달라는 뜻. 그렇다면 원래 구하기로 한 목적이 보호소에서 없어진 걸 구하라고 했으므로, WHERE NOT IN (...)을 하면 보호소에는 없지만 입양에만 있는 ID를 구하게 된다. 
```sql
SELECT ANIMAL_ID, NAME FROM ANIMAL_OUTS 
WHERE ANIMAL_ID NOT IN (SELECT ANIMAL_ID FROM ANIMAL_INS)
```

### 있었는데요 없었습니다
```sql
SELECT o.ANIMAL_ID, o.NAME FROM ANIMAL_INS i
  LEFT JOIN ANIMAL_OUTS o 
    ON i.ANIMAL_ID = o.ANIMAL_ID
 WHERE o.DATETIME < i.DATETIME
 ORDER BY i.DATETIME
```

### 오랜 기간 보호한 동물(1)
```sql
SELECT NAME, DATETIME FROM ANIMAL_INS i
WHERE ANIMAL_ID NOT IN (SELECT ANIMAL_ID FROM ANIMAL_OUTS)
ORDER BY DATETIME
LIMIT 3
```

### 보호소에서 중성화한 동물
- 방법1
```sql
SELECT i.ANIMAL_ID, 
       i.ANIMAL_TYPE, 
       i.NAME 
  FROM ANIMAL_INS i
  LEFT JOIN ANIMAL_OUTS o
    ON i.ANIMAL_ID = o.ANIMAL_ID
 WHERE i.SEX_UPON_INTAKE LIKE 'Intact%'
   AND (o.SEX_UPON_OUTCOME LIKE 'Spayed%' OR o.SEX_UPON_OUTCOME LIKE 'Neutered%')
```
- 방법2
- JOIN 할 때 JOIN 할 컬럼 이름이 같다면 USING (컬럼이름) 으로 하면 ON 으로 안 해도 된다.  
중성화 되지 않았지만, 입양할 때 중성화된 동물 구하기이므로, 전과 후가 달라진 것만 구하면 된다. 중성화 됐던 동물은 그대로 중성화된 상태이므로 상태가 그대로이고, 중성화 안 됐었던 동물만 중성화가 되면 상태가 달라지기 때문이다.
```sql
SELECT i.ANIMAL_ID, 
       i.ANIMAL_TYPE, 
       i.NAME 
  FROM ANIMAL_INS i
  LEFT JOIN ANIMAL_OUTS o USING (ANIMAL_ID)
 WHERE i.SEX_UPON_INTAKE != o.SEX_UPON_OUTCOME
```