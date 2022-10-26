### 데이터베이스 커넥션


### 데이터베이스 커넥션 풀, DBCP(DataBase Connection Pool)
클라이언트의 요청에 따라 각 어플리케이션의 스레드에서 데이터베이스에 접근하기 위해서는 Connection이 필요하다. 
Connection pool은 이런 Connection을 여러 개 생성해 두어 저장해 놓은 공간(캐시), 또는 이 공간의 Connection을 필요할 때 꺼내 쓰고 반환하는 기법을 말한다.

데이터베이스와 커넥션을 미리 맺어놓는 것, 미리 커넥션의 갯수를 확보해 둔 뒤 이 커넥션을 애플리케이션에서 사용하고 반환하는 형태


#### 사용하는 이유
- DB 커넥션 비용 절감
  - Pool 속에 미리 Connection이 생성되어 있기 때문에 Connection을 생성하고 닫는데 시간이 소비되지 않는다.
  - Connection을 계속해서 재사용하기 때문에 Connection의 수가 많이 필요하지 않다.
  
- 안정적인 DB 커넥션 유지
  - 커넥션 수를 제어하기 때문에 동시 접속자 수가 몰려도 애플리케이션이 쉽게 다운되지 않는다.
  
- [Spring Boot의 Hikari CP 설정](https://bamdule.tistory.com/166)
 
- Connection Pool
  - 

DB에 접근하는 단계
웹 컨테이너가 실행되면서 DB와 연결된 Connection 객체들을 미리 생성하여 pool에 저장한다.
DB에 요청 시, pool에서 Connection 객체를 가져와 DB에 접근한다.
처리가 끝나면 다시 pool에 반환한다.

Connction이 부족하면?
모든 요청이 DB에 접근하고 있고 남은 Conncetion이 없다면, 해당 클라이언트는 대기 상태로 전환시키고 Pool에 Connection이 반환되면 대기 상태에 있는 클라이언트에게 순차적으로 제공된다.
왜 사용할까?
매 연결마다 Connection 객체를 생성하고 소멸시키는 비용을 줄일 수 있다.
미리 생성된 Connection 객체를 사용하기 때문에, DB 접근 시간이 단축된다.
DB에 접근하는 Connection의 수를 제한하여, 메모리와 DB에 걸리는 부하를 조정할 수 있다.
Thread Pool
비슷한 맥락으로 Thread pool이라는 개념도 있다.
이 역시 매 요청마다 요청을 처리할 Thread를 만드는것이 아닌, 미리 생성한 pool 내의 Thread를 소멸시키지 않고 재사용하여 효율적으로 자원을 활용하는 기법.
Thread Pool과 Connection pool
WAS에서 Thread pool과 Connection pool내의 Thread와 Connection의 수는 직접적으로 메모리와 관련이 있기 때문에, 많이 사용하면 할 수록 메모리를 많이 점유하게 된다. 그렇다고 반대로 메모리를 위해 적게 지정한다면, 서버에서는 많은 요청을 처리하지 못하고 대기 할 수 밖에 없다.
보통 WAS의 Thread의 수가 Conncetion의 수보다 많은 것이 좋은데, 그 이유는 모든 요청이 DB에 접근하는 작업이 아니기 때문이다.


https://delf-lee.github.io/post/connection-pool/
https://bamdule.tistory.com/166
https://devkly.com/db/db-connection-pool/
https://engkimbs.tistory.com/783
https://steady-coding.tistory.com/564
https://principled-ptarmigan-d63.notion.site/Jdbc-Connection-Pool-a2dd51dc061b4cfe817f5eddcc2cded8

