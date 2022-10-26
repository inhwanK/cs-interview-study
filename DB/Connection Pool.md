### 데이터베이스 커넥션 풀, DBCP(DataBase Connection Pool)
- 클라이언트의 요청에 따라 각 스레드에서 데이터베이스에 접근하려면 Connection이 필요, 이 때 미리 데이터베이스와 커넥션을 맺어놓는 것.
- 미리 커넥션의 갯수를 확보해 둔 뒤 이 커넥션을 애플리케이션에서 사용하고 반환하는 형태

<img src="images/connection pool.png" width="550" height="300">

### 풀(Pool)
- 필요할 때마다 개체를 할당하고 파괴하는 대신, 사용 준비된 상태로 초기화된 개체 집합
  - 즉, 데이터베이스 커넥션 풀은 커넥션(개체)을 미리 생성해 사용 준비된 상태로 초기화된 커넥션 집합 

### 사용하는 이유
- DB 커넥션 비용 절감
  - Pool 속에 미리 Connection이 생성되어 있기 때문에 Connection을 생성하고 닫는데 시간이 소비되지 않는다.
  - Connection을 계속해서 재사용하기 때문에 Connection의 수가 많이 필요하지 않다.
- 안정적인 DB 커넥션 유지
  - 커넥션 수를 제어하기 때문에 동시 접속자 수가 몰려도 애플리케이션이 쉽게 다운되지 않는다.

### 자바의 Connection 객체


Connection Pool과 비슷한 맥락으로 Thread Pool이라는 개념이 있다.

### Thread Pool과 Connection Pool
- WAS에서 Thread pool과 Connection pool내의 Thread와 Connection의 수는 직접적으로 메모리와 관련이 때문에, 많이 사용하면 할 수록 메모리를 많이 점유하게 된다.
  -  그렇다고 반대로 메모리를 위해 적게 지정한다면, 서버에서는 많은 요청을 처리하지 못하고 대기 할 수 밖에 없다. 
- 보통 WAS의 Thread의 수가 Conncetion의 수보다 많은 것이 좋다. 모든 요청이 DB에 접근하는 작업이 아니기 때문이다.

### 새롭게 궁금해진 점
- 스프링 부트의 커넥션 풀 설정
  - [Spring Boot의 Hikari CP 설정](https://bamdule.tistory.com/166)
  - [최범균님의 Connection Pool 설정 영상](https://www.youtube.com/watch?v=6Q7iRTb4tQE)
  - [스프링 부트 DBCP 및 MySQL 연동](https://engkimbs.tistory.com/783)
- Java에서 스레드 풀 사용
  - [Tecoble - Java 에서 스레드 풀(Thread Pool) 을 사용해 보자](https://tecoble.techcourse.co.kr/post/2021-09-18-java-thread-pool/)

### 참조 자료
- https://delf-lee.github.io/post/connection-pool/
- https://d2.naver.com/helloworld/5102792
- https://devkly.com/db/db-connection-pool/
- https://steady-coding.tistory.com/564

