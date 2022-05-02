## [SQL] GROUP BY 연습

[REFERENCE : PROGRAMMERS > 나랑 이름이 같은 사람은 몇 명일까요? 데이터를 묶고 평균값을 계산해보세요.](https://programmers.co.kr/learn/courses/30/parts/17044)

### 고양이와 개는 몇 마리 있을까
- ORDER BY 1 은 첫 번째 컬럼을 ORDER BY 했다는 말이다. 여기서 첫 번째 컬럼은 ANIMAL_TYPE, 두 번째 컬럼은 count 이다. 그러므로 ANIMAL_TYPE 을 기준으로 ORDER BY 한 것이다.
```sql
SELECT ANIMAL_TYPE, COUNT(*) as count FROM ANIMAL_INS
GROUP BY ANIMAL_TYPE
ORDER BY 1
```

### 동명 동물 수 찾기
- COUNT(*) 일 때는 NULL 포함해서 COUNT, COUNT(COLUMN NAME) 일 떄는 NULL 제외하고 COUNT.
```sql
SELECT NAME, COUNT(NAME) AS count FROM ANIMAL_INS
GROUP BY NAME
HAVING count > 1
ORDER BY NAME
```

### 입양 시각 구하기(1)
```sql
SELECT HOUR(DATETIME) AS hour, COUNT(HOUR(DATETIME)) AS COUNT FROM ANIMAL_OUTS
GROUP BY hour
HAVING hour BETWEEN '9' AND '19'
ORDER BY hour
```

### 입양 시각 구하기(2)
- MySQL에서 = 연산자는 SET 이외의 문에서 비교연산자로 취급된다. 따라서 보통 변수를 선언하고 값을 대입할 때는 := 를 사용한다.

```sql
SET @변수이름 = 대입값; 혹은 SET @변수이름 := 대입값;
SELECT @변수이름 := 대입값;
```

- ANIMAL_OUTS 테이블에서는 0 부터 6 까지, 20 부터 23 까지가  없으므로 이를 추가해주기 위해서 가상테이블을 만든 후 합친다.  
0을 기준 삼아 가상테이블을 만들기 위해 SET 으로 변수를 정의한다. 이 때 -1로 변수를 할당해준다. 이유는 가상 테이블을 만들 때, 0부터 ANIMAL_OUTS의 ROW수(100개) 만큼 계속 1씩 증가될 것이므로 기준점은 -1로 잡는다.  
설정한 변수로 가상 테이블을 만든다. SET으로 만든 @hour 변수를 1씩 증가시키고, HAVING으로 23까지만 조건을 건다. (23시까지만 필요하므로)  
가상테이블을 만들었다면 만들어진 0 부터 23 부터의 해당 가상테이블을 기준으로 ANIMAL_OUTS를 LEFT JOIN 한다. 합쳐졌다면 필요한 HOUR와 COUNT를 사용해준다.  
 *왜 WHERE을 쓰지 않았는가? -> alias를 사용하려면 GROUP BY가 되고 나서부터 사용이 가능하기 때문에 GROUP BY, HAVING으로 조건을 설정하였다.

```sql
SET @hour := -1;
  
WITH h1 AS (
SELECT @hour := @hour + 1 AS HOUR FROM ANIMAL_OUTS
GROUP BY hour
HAVING hour BETWEEN '0' AND '23'
)

SELECT HOUR, COUNT(HOUR(ANIMAL_OUTS.DATETIME)) AS COUNT FROM h1
LEFT JOIN ANIMAL_OUTS
ON h1.HOUR = HOUR(ANIMAL_OUTS.DATETIME)
GROUP BY HOUR
```