## Index란?

### Index란?
- 우리말로 색인, 쉽게 찾아볼 수 있도록 일정한 순서에 따라 놓은 목록

## DB의 Index
- 데이터베이스에서 조회 및 검색 성능을 향상시키는 자료구조.
  - where 절 등을 통해 활용된다.
  
- DBMS의 인덱스는 항상 정렬된 상태를 유지.
- 하나의 데이터베이스 객체.
- 데이터베이스 크기의 약 10% 정도의 저장공간 필요.

## DB의 스캔 방식
DB의 스캔 방식에는 Full Table Scan과 Index Scan이 있다.

### Full Table Scan

<img src="images/Full%20Table%20Scan.png" width="650">
출처 - https://hoon93.tistory.com/53

- 순차적 접근
  - 테이블에 존재하는 모든 데이터를 읽어 가면서 조건에 맞으면 추출, 맞지 않으면 버림
- 접근 비용 감소
 
#### 사용하는 경우
- 적용가능한 인덱스가 없는 경우
- 인덱스 처리 범위가 넓은 경우 (인덱스가 있더라도 처리 범위가 넓어 성능상의 이점이 없을 때)
- 작은 테이블에 접근하는 경우

### Index Scan
<img src="images/index%20scan.jfif" width="400">

출처 - https://hoon93.tistory.com/53

- 인덱스를 구성하는 컬럼 값을 기반으로 데이터를 추출하는 접근 기법

### Index의 알고리즘

#### B-Tree
데이터 베이스 인덱스의 기본 구조로 B-Tree(Balanced-Tree)이다. 일반적으로 B-Tree의 확장된 개념인 B+Tree가 사용된다.

<img src="images/b-tree.jfif" width="650">

출처 - http://www.btechsmartclass.com/data_structures/b-trees.html

- 이진트리와 유사하지만, 자식 노드를 2개 이상 가질 수 있다.

자세한 사항은 [인덱스 자료 구조 (B-Tree)](https://velog.io/@sem/DB-%EC%9D%B8%EB%8D%B1%EC%8A%A4-%EC%9E%90%EB%A3%8C-%EA%B5%AC%EC%A1%B0-B-Tree) 를 참고하자.

### B+tree 알고리즘
<img src="images/b+tree.png" width="650">

- 실제 데이터가 저장된 리프노드(Leaf nodes)
- 리프노드까지의 경로 역할을 하는 논리프노드(Non-leaf nodes)
- 경로의 출발점이 되는 루트 노드(Root node)
B+tree는 리프노드에 이르기까지에 대한 자식 노드에 포인터가 저장되어 있다. 즉, B+트리의 검색은 루트노드에서 어떤 리프 노드에 이르는 한 개의 경로만 검색하면 되므로 매우 효율적이다.

#### B+tree 사용 이유
- 왜 index 생성 시 b-tree를 사용하는지? hash table이 더 효율적이지 않은지? 
  - SELECT 질의 조건에는 부등호 연산(<>)도 포함 
  - hash table은 동등 연산에 특화된 자료구조이기 때문에 부등호 연산 사용 시 문제 발생

#### 주의할 점
- 인덱스는 따로 테이블의 형태로 관리가 된다. 자원을 소모한다는 의미. 때문에 무분별한 인덱스의 사용은 성능에 부정적인 영향을 미칠 수 있다. 
- 또한 인덱스는 이진트리를 사용하기 때문에 기본적으로 정렬되어 있다. 이로인해 검색과 조회의 속도를 향상시킬 수 있지만 잦은 데이터의 변경(삽입, 수정 삭제)가 된다면 인덱스 데이블을 변경과 정렬에 드는 오버헤드 때문에 오히려 성능 저하가 일어날 수 있다.
  - INSERT : 테이블에는 입력 순서대로 저장되지만, 인덱스 테이블에는 정렬하여 저장하기 때문에 성능 저하 발생
  - DELETE : 테이블에서만 삭제되고 인덱스 테이블에는 남아있어 쿼리 수행 속도 저하
  - UPDATE : 인덱스에는 UPDATE가 없기 때문에 DELETE, INSERT 두 작업 수행하여 부하 발생


- 데이터의 중복이 높은 컬럼(카디널리티가 낮은 컬럼)은 인덱스로 만들어도 무용지물 (예: 성별)
- 다중 컬럼 인덱싱할 때 카디널리티가 높은 컬럼->낮은 컬럼 순으로 인덱싱해야 효율적

### 새롭게 궁금해진 점
- B-Tree, B+Tree, B*Tree
  - [인덱스 자료 구조 (B-Tree)](https://velog.io/@sem/DB-%EC%9D%B8%EB%8D%B1%EC%8A%A4-%EC%9E%90%EB%A3%8C-%EA%B5%AC%EC%A1%B0-B-Tree)


- 클러스터링 인덱스와 논-클러스터링 인덱스
  -[우테코 - [10분 테코톡] 라라, 제로의 데이터베이스 인덱스](https://youtu.be/edpYzFgHbqs)
### 참조 자료
- [우테코 - [10분 테코톡] 라라, 제로의 데이터베이스 인덱스](https://youtu.be/edpYzFgHbqs)
- https://github.com/WeareSoft/tech-interview/blob/master/contents/db.md#index%EB%9E%80
- https://hoon93.tistory.com/53