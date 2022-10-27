# [DB] JOIN 총정리

# 예제 테이블

이번 주제를 보면서 예제에 쓰일 테이블의 정보 입니다.

T1 테이블                                                                      T2 테이블

![t1 테이블.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5b857f97-9d4b-4ab5-8b6e-2f61203ae2c8/t1_%ED%85%8C%EC%9D%B4%EB%B8%94.png)

![t2 테이블.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cf47d19a-4fa8-4192-a651-000aa9f6ff05/t2_%ED%85%8C%EC%9D%B4%EB%B8%94.png)

# JOIN이란?

조인이란 **둘 이상의 테이블을 연결해서 데이터를 검색하는 방법**입니다

관계형 데이터베이스는 외래 키 열이라고 하는 공통 열을 사용하여 함께 연결된 여러 관련 테이블로 구성됩니다. 이 때문에 각 테이블의 데이터는 비즈니스 관점에서 불완전합니다.

![orders_order_details_tables.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/20b3cd43-f16c-4bb0-9cac-8a9e878b6e14/orders_order_details_tables.png)

예를 들어 orderNumber라는 열을 공유하는 orders, orderdetails 테이블이 있다고 가정해 보겠습니다.

orders을 사용하여 완전한 주문 정보를 얻으려면 orderdetails 테이블의 모든 데이터를 가져올 필요가 있습니다.

이러한 상황에서 사용되는 것이 조인입니다.

# 논리적 조인 (조인의 종류)

논리적 조인은 우리가 흔하게 알고 있는 INNER JOIN, OUTER JOIN같이 어떠한 형태로 데이터를 가지고 올지를 정하는 것입니다.

JOIN의 종류는 가지고 올 그래프의 모양에 따라 크게 네 가지로 분류됩니다.

1. INNER JOIN
  1. EQUI JOIN
  2. NON - EQUI JOIN
2. OUTER(LEFT, RIGHT) JOIN
3. CROSS JOIN

## 내부 조인 (INNER JOIN)

내부 조인(INNER JOIN)은 **두 테이블의 교집합**을 가지고 옵니다.

즉, 기준이 되는 테이블의 열과 같은 데이터가 포함된 다른 테이블의 행을 모두 가져옵니다.

![내부 조인.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fa533896-cb00-4ad2-b4c2-82631c567362/%EB%82%B4%EB%B6%80_%EC%A1%B0%EC%9D%B8.png)

```sql
SELECT *
FROM T1 INNER JOIN T2 
ON T1.ID = T2.ID;
```

- 먼저 FROM 절에 표시된 메인 테이블을 지정합니다. (T1)
- INNER JOIN 절에 나오는 메인 테이블과 조인할 테이블을 지정합니다. (T1, T2)
- ON 키워드 뒤에 조인 조건을 지정합니다. 조인 조건은 주 테이블과 조인 테이블이 동시에 가지고 있는 열을 지정합니다.
- 조인 조건을 기준으로 두 테이블의 열이 일치하면 새로운 행으로 조합하여 JOIN 테이블에 포함됩니다.

INNER JOIN은 조인을 위한 연산자가 = 이냐 아니냐에 따라 EQUI JOIN, NON - EQUI JOIN 으로 다시 나뉩니다.

## 동등 내부 조인 (EQUI JOIN)

두 개의 테이블 간의 칼럼 값들이 서로 정확하게 일치하는 경우에 사용되는 INNER JOIN 방법입니다.

대체로 PK - FK의 관계가 맺어져 있을 경우 사용합니다. (일반적인 경우이지 꼭 그런것은 아닙니다.)

조인 조건에 = 연산자를 사용해서 표현합니다.

EQUI JOIN 쿼리를 실행시키면, t1과 t2테이블의 교집합이 결과로 출력됩니다.

```sql
SELECT * 
FROM t1
INNER JOIN t2
ON t1.id = t2.t1_id;
```

![동등 내부조인 결과.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/04e14459-7d8b-41d8-91a0-193ca08bd081/%EB%8F%99%EB%93%B1_%EB%82%B4%EB%B6%80%EC%A1%B0%EC%9D%B8_%EA%B2%B0%EA%B3%BC.png)

## 비동등 내부 조인 (NON-EQUI JOIN)

두 개의 테이블 간에 칼럼 값들이 서로 정확하게 일치하지 않는 경우에 사용됩니다.

= 연산자가 아닌 BETWEEN, >, ≥, <, ≤ 와 같은 비교 연산자들을 사용하여 JOIN을 수행합니다.

두 테이블 간의 칼럼 값들이 서로 명확한 관계를 가지지 않거나 일치하지 않는 경우에 사용하게 됩니다.

NON-EQUI JOIN 쿼리를 실행 시키면 지정된 범위안에 포함된 데이터들의 교집합이 출력됩니다.

```sql
SELECT * 
FROM t1
INNER JOIN t2
ON t1.pay BETWEEN t2.low_price AND t2.high_price;
```

![비 동등 내부조인 결과.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/896482b6-64f4-4b45-8c70-9c512c8228dc/%EB%B9%84_%EB%8F%99%EB%93%B1_%EB%82%B4%EB%B6%80%EC%A1%B0%EC%9D%B8_%EA%B2%B0%EA%B3%BC.png)

## 외부 조인 (OUTER JOIN)

외부 조인(OUTER JOIN)은 ****둘 이상의 테이블에서 왼쪽 혹은 오른쪽 테이블, 즉 **기준이 되는 테이블의 전체 데이터 + 참조 테이블의 조인 데이터를 가져올 수 있습니다.**

INNER JOIN과 비슷하게 FROM 절 뒤에 LEFT JOIN 혹은 RIGHT JOIN 과 ON 키워드를 이용해 데이터를 가져올 수 있습니다.

![LEFT JOIN.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4d1bb807-c2cd-4505-9879-e234089b3064/LEFT_JOIN.png)

```sql
// LEFT OUTER JOIN
SELECT *
FROM T1 LEFT JOIN T2
ON T1.ID = T2.ID;

// RIGHT OUTER JOIN
SELECT *
FROM T1 RIGHT JOIN T2
ON T1.ID = T2.ID;
```

- FROM 절 뒤의 **T1은 기준 테이블**, **LEFT/RIGHT JOIN 절 뒤의 T2는 참조 테이블로 취급**합니다.
- ON 키워드 뒤에 조인 조건을 지정합니다. **조인 조건을 기준으로 두 테이블의 열을 비교하여 일치하면 새로운 행으로 조합하여 JOIN 테이블에 포함**됩니다.
- OUTER JOIN의 경우 **기준 테이블의 행과 참조 테이블의 행이 일치하지 않는 경우에도 참조 테이블의 데이터를 NULL로 설정,** 새로운 행을 조합하여 JOIN 테이블에 포함 시킵니다.

t1 테이블을 기준으로 t2 테이블을 LEFT JOIN하면 아래와 같은 결과를 리턴합니다.

```sql
SELECT * 
FROM t1
LEFT JOIN t2
ON t1.id = t2.t1_id;
```

![LEFT JOIN 결과.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1e42a8c0-9f39-41ca-9845-ba72724567a5/LEFT_JOIN_%EA%B2%B0%EA%B3%BC.png)

## CROSS JOIN (CARTESIAN PRODUCT)

교차 조인(CROSS JOIN)은 **기준 테이블의 각 행에 조인 테이블의 모든 행을 하나씩 각각 조합**합니다.

즉, t1 테이블의 1번 행을 t2 테이블의 1번 행부터 m번 행까지 조합하여 JOIN 테이블에 포함시키고, 이 과정을 t1 테이블의 1번 행부터 n번 행까지 반복합니다.

때문에 일반적으로 **교차 조인의 결과 집합에는 [t1 테이블의 행의 개수 * t2 테이블의 행의 개수]개의 행이 있습니다.**

CROSS JOIN은 카티션 곱이라고도 부릅니다.

```sql
SELECT *
FROM t1
CROSS JOIN t2;
```

![교차 조인 결과.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/71a5d092-472b-4081-acb7-967c5655ccce/%EA%B5%90%EC%B0%A8_%EC%A1%B0%EC%9D%B8_%EA%B2%B0%EA%B3%BC.png)

조인 결과 **t1 테이블과 t2 테이블이 가질 수 있는 모든 경우의 수**가 나옵니다.

교차 조인의 경우 t1 테이블과 t2 테이블의 모든 행의 곱을 구해오기 때문에 INNER, OUTER JOIN과 달리 **ON 키워드를 사용하여 조인 조건을 지정할 필요가 없습니다.**

# 물리적 조인 (조인의 방식)

물리적 조인이란 말 그대로 **물리적으로 디스크에서 데이터를 조인하여 가지고 오는 방법**에 따라 분류한 조인입니다. 조인 수행 원리, 조인 알고리즘이라고도 합니다.

물리적 조인은 세 가지로 분류할 수 있습니다.

1. Nested Loops Join (중첩 루프 조인)
2. Sort Merge Join (정렬 병합 조인)
3. Hash Join (해쉬 조인)

## Nested Loops Join (중첩 루프 조인)

두 개 이상의 테이블에서, 하나의 테이블을 기준으로 각 ROW를 추출할 때마다 순차적으로 상대 테이블의 연관된 모든 ROW를 조인해 추출하는 방식입니다.

이러한 방식은 프로그래밍의 중첩 for문과 유사합니다.

### Nested Loop Join의 수행 절차

![NL JOIN.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ffd6cf23-2687-4d91-88c6-b4d4e691c2c6/NL_JOIN.png)

(위 그림에서 기준이 되는 Table A를 Driving Table 혹은 Outer Table이라 하고, 참조 되는 Table B를 Driven Table 혹은 Inner Table이라고 합니다)

1. Table A에서 row를 하나씩 스캔합니다.
2. Driving Table의 row 하나마다 내부 테이블의 레코드를 하나씩 스캔해서 Join 조건에 맞으면 리턴합니다.
3. 모든 루프를 종료할 때 까지 반복합니다.

이렇게 **모든 테이블의 칼럼을 하나씩 확인하며 조인을 수행**하게됩니다.

만약 적절한 조치를 취하지 않는다면 NL Join은 최악의 경우 N * M의 수행시간이 걸리게 될 것 입니다. 그렇기 때문에 **Driven Table에 적절한 인덱스를 사용할 수 없는 상황**에서는 사용할 수 없습니다.

### Nested Loops Join 특징

1. 인덱스에 의한 랜덤 액세스에 기반을 두기 때문에 대량의 데이터 처리 시 적합하지 않음
2. Driving Table로는 테이블의 데이터가 적은 마스터 테이블이거나, WHERE 절의 조건으로 적절하게 ROW를 제어할 수 있는 것이어야 함
3. Driven Table에는 조인 시 사용할 연결고리 칼럼에 대한 적절한 인덱스가 있어야 함.

### 튜닝 포인트

- 테이블 간 조인 횟수 최소화 ⇒ 조인 순서의 최적화 (Driving Table의 크기가 작을 수록 좋음)
- 연결고리 칼럼에 대한 인덱스 구성 및 활용 (Driven Table의 Join 키에 해당하는 칼럼에 적절한 인덱스 부여)

정리하자면 Nested Loops Join은 인덱스의 특징을 그대로 따라 가는 것 같습니다. 인덱스를 사용하기 때문에 대용량 데이터 조회에 사용하면 오히려 성능이 떨어질 수 있다는 점에 주의해야 합니다.

## Sort Merge Join (정렬 병합 조인)

대용량의 데이터로 인해 **조회의 범위가 클 때 주로 사용하는 조인 방식**입니다.

**양쪽 테이블을 각각 Access하여 그 결과를 정렬**하고 그 **정렬한 결과를 차례로 Scan 해 나가면서 연결고리의 조건으로 조인을 수행**합니다.

Sort Merge Join은 정렬을 수행한다는 특성 때문에 주로 아래와 같은 상황에 많이 사용 됩니다.

- 대용량의 데이터로 인해 인덱스 **랜덤 액세스에 의한 오버헤드가 부담이 될 경우**
- 조인 조건으로 >, ≥, <, ≤, BETWEEN 과 같은 **범위 비교 연산자가 사용될 경우**

### Sort Merge Join 수행 절차

![Sort Merge Join.jpg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/21a90a7b-2dd3-4fd4-979a-db4d28a8798b/Sort_Merge_Join.jpg)

1. 각 테이블에 대해 동시에 독립적으로 데이터를 먼저 읽어 들인다.
2. 읽어 들인 각 테이블의 데이터를 조인을 위한 연결고리 칼럼을 기준으로 정렬한다.
3. 정렬이 모두 끝난 후 조인 작업을 수행한다.

### Sort Merge Join 특징

1. 연결고리에 인덱스가 생성되어 있지 않은 경우에 빠른 조인을 위하여 사용됨
2. 조인하고자 하는 각 테이블에 대해서 독립적으로 데이터를 읽어 들일 때, 이를 얼마나 빠르게 할 것 인가가 중요함
3. 각 테이블로부터 읽은 데이터를 연결고리 칼럼을 기준으로 정렬을 수행할 때 얼마나 빠르게 할 것인가가 중요함

### 튜닝포인트

- 각 테이블로부터 데이터를 빨리 읽도록 함
- 읽어드린 데이터를 빠르게 정렬할 수 있도록 함 ⇒ Sort 메모리 사이즈 최적화

정리하자면 Sort Merge Join은 정렬작업에 걸리는 부담을 얼마나 줄이느냐에 따라 성능이 갈리게 됩니다.

## Hash Join (해쉬 조인)

해쉬 조인은 조인될 **두 테이블 중 하나를 해시 테이블로 선정하여 조인 될 테이블의 조인 키 값을 해시 알고리즘으로 비교하여 매치되는 결과값을 얻는 방식**입니다. 해쉬 조인은 비용 기반 옵티마이저를 사용할 때만 사용될 수 있는 조인 방식이며 ‘=’ 비교를 통한 조인에서만 사용될 수 있습니다. 주로 많은 양의 데이터를 조인해야 하는 경우에 주로 사용됩니다.

### Hash Join 수행 절차

![해시 조인.jpg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eb720e53-5564-467e-9929-d3a5dfd7a158/%ED%95%B4%EC%8B%9C_%EC%A1%B0%EC%9D%B8.jpg)

1. 둘 중 작은 집합을 읽어 Hash Area에 Hash Table을 생성
2. 이때, 해시 함수를 통해 리턴받은 버킷 주소(A, B, C, D)로 찾아가 해시체인에 연결
3. 반대쪽 큰 집합을 읽어 해시 함수를 버킷 주소를 알아냄
4. 리턴 받은 버킷 주소로 찾아가서 해시 체인을 스캔하면서 데이터 탐색

### Hash Join 특징

1. NL조인처럼 랜덤 엑세스에 의한 부하가 없고, Sort Merge 조인처럼 양쪽 집합을 정렬해야 하는 부담이 없다
2. 해시테이블을 생성하는데 비용이 든다.
3. 동등 조인에서만 사용가능 하다.

### 튜닝 포인트

- Hash Table을 만드는 과정을 효율화 한다. ⇒ Hash Table을 만드는 테이블의 작고, 중복 데이터가 적으면 적을수록 좋다.
- Hash Table이 만들어지는 Hash Area에 충분한 메모리 확보

해시 조인의 성능은 Hash Table을 얼마나 빠르게 잘 만드느냐에 달린 것 같습니다.