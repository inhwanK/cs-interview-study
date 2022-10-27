
### ORM란?(Object Relational Mapping)
>- 객체와 관계형 데이터 베이스의 데이터를 **자동으로 매핑**해주는 것 을 말한다.
>- 객체를 데이터베이스에 저장할때 Insert SQL을 직접 작성하는게 아니라 객체를 마치 자바 컬렉션에 저장하듯이 ORM프레임워크에 저장하면 된다.


### 영속성이란?
> 프로그램이 종료되어도 데이터가 사라지지 않는 특성을 말한다.
영속성을 갖지 못한 데이터는 메모리에만 존재하기 때문에 프로그램이 종료되면 사라지게 된다.
영속성은 파일 시스템, 관계형 데이터베이스 또는 객체 데이터 베이스를 활용하여 구현한다.
데이터가 영속성을 가지기 위해 Spring에서 사용하는 방법은 3가지가 존재한다.
>1. JDBC (Java)
>2. Spring JDBC
>3. Persistence Framework

### Persistence Framework종류
#### ORM : JPA, Hibernate
**JPA/Hibernate**
- JPA는 자바의 ORM 기술 표준으로 인터페이스의 모음이다. 이러한 JPA표준 명세를 구현한 구현체가 바로 Hibernate이다.
JDBC vsJPA

**Sequelize**
- Sequelize는 Postgres, MySQL, MariaDB, SQLite등을 지원하는 Promise에 기반한 비동기로 동작하는 Node.js ORM이다.

**Django ORM**
- Python기반 프레임워크인 Django에서 자체적으로 지원하는 ORM이다.

### ORM 장점
> - 객체 지향적인 코드로 인해 더 직관적이고 비즈니스 로직에 집중할 수 있음.
    - ORM을 이용하면 SQL Query가 아닌 직관적인 코드(메서드)로 데이터를 조작할 수 있어 개발자가 객체 모델로 프로그래밍하는데 징중할 수 있도록 한다.
> - 재사용 및 유지보수의 편리성이 증가한다.
	- ORM은 독립적으로 작성되어있고, 해당 객체들을 재활용할 수 있다.
    - 매핑정보가 명확하여, ERD를 보는 것에 대한 의존도 낮춘다.
> - DBMS에 대한 종속성이 줄어든다.
	- Object에 집중하는 코드이기 때문에 DBMS를 교체할 때도 적은 시간과 적은 비용이 듬
    
### ORM 단점
> - ORM으로만 서비스를 구현하기가 어려움
>- **프로시저**가 많은 시스템에선 ORM의 객체 지향적인 장점을 활용하기 어렵다

#### 프로시저란?
> **SQL Server에서 제공하는 프로그래밍 기능 **
주기적으로 실행되는 긴 쿼리문을 프로시저에 저장하고 저장된 프로시저를 호출하여 프로그래밍한다.

#### SQLMapper: MyBatis
>- 객체와 관계형 데이터 베이스의 데이터를 개발자가 작성한 SQL로 매핑시켜주는 프레임 워크
>- 개발자가 SQL을 직접 작성해야하고, SQL문을 실행하고 얻은 데이터를 객체로 매핑시켜줌
### SQL Mapper장점
>- SQL을 작성할 줄안다면 수월하게 사용할 수있다.
>- 세부적인 SQL변경시 편리하다.

### SQL Mapper단점
>- DBMS에 따라 SQL문법이 다르기에 DBMS에 종속적
>- 개발시 SQL문작성
>- DBMS 변경시 재사용 어려움
>- 2개 이상의 DBMS의 지원시 유지보수가 어려움


### JDBC vs JPA, SPringJDBC
#### JDBC란?
>DB에 접근하고 SQL을 날릴 수 있게 해주는 자바의 표준 API다.
Driver Manager를 사용하여 드라이버들을 로딩, 해제한다.
![](https://velog.velcdn.com/images/ihanseul731/post/e0ff8ca8-5162-470e-9621-1b55271c3cd2/image.png)
```
// JDBC를 사용한 Java Application(DAO)
public class CarDao {
    public Connection getConnection() {
        Connection con = null;
        String server = "localhost:3306"; // MySQL 서버 주소
        String database = "DB_NAME"; // MySQL DATABASE 이름
        String option = "?useSSL=false&serverTimezone=UTC";
        String userName = "USER_ID"; //  MySQL 서버 아이디
        String password = "USER_PASSWORD"; // MySQL 서버 비밀번호

        // 드라이버 로딩
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.err.println(" !! JDBC Driver load 오류: " + e.getMessage());
        }

        // 드라이버 연결
        try {
            con = DriverManager.getConnection("jdbc:mysql://" + server + "/" + database + option, userName, password);
            System.out.println("정상적으로 연결되었습니다.");
        } catch (SQLException e) {
            System.err.println("연결 오류:" + e.getMessage());
        }
        return con;
    }

    // 드라이버 연결해제
    public void closeConnection(Connection con) {
        try {
            if (con != null)
                con.close();
        } catch (SQLException e) {
            System.err.println("con 오류:" + e.getMessage());
        }
    }

    // CRUD
    public void addCar(Car car) throws SQLException {
        String query = "INSERT INTO car (car_id, car_brand, car_created) VALUES (?, ?, ?)";
        PreparedStatement pstmt = getConnection().prepareStatement(query);
        pstmt.setInt(1, car.getId());
        pstmt.setString(2, car.getBrand());
        pstmt.setString(3, car.getCreated());
        pstmt.executeUpdate();
    }
}
```

#### JPA란? 
>JPA는 자바 진영 ORM의 API표준 명세이다. 
>**JPA도 내부적으로 JDBC를 사용한다.**
![](https://velog.velcdn.com/images/ihanseul731/post/d4f2bab9-7fc0-4a00-a864-79c5cd1162c2/image.png)
```
// JDBC를 사용한 Java Application(DAO)
public class CarDao {
    public Connection getConnection() {
        Connection con = null;
        String server = "localhost:3306"; // MySQL 서버 주소
        String database = "DB_NAME"; // MySQL DATABASE 이름
        String option = "?useSSL=false&serverTimezone=UTC";
        String userName = "USER_ID"; //  MySQL 서버 아이디
        String password = "USER_PASSWORD"; // MySQL 서버 비밀번호

        // 드라이버 로딩
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.err.println(" !! JDBC Driver load 오류: " + e.getMessage());
        }

        // 드라이버 연결
        try {
            con = DriverManager.getConnection("jdbc:mysql://" + server + "/" + database + option, userName, password);
            System.out.println("정상적으로 연결되었습니다.");
        } catch (SQLException e) {
            System.err.println("연결 오류:" + e.getMessage());
        }
        return con;
    }

    // 드라이버 연결해제
    public void closeConnection(Connection con) {
        try {
            if (con != null)
                con.close();
        } catch (SQLException e) {
            System.err.println("con 오류:" + e.getMessage());
        }
    }

    // CRUD
    public void addCar(Car car) throws SQLException {
        String query = "INSERT INTO car (car_id, car_brand, car_created) VALUES (?, ?, ?)";
        PreparedStatement pstmt = getConnection().prepareStatement(query);
        pstmt.setInt(1, car.getId());
        pstmt.setString(2, car.getBrand());
        pstmt.setString(3, car.getCreated());
        pstmt.executeUpdate();
    }
}
```
#### Spring DataJDBC란?
>Spring JDBC는 JDBC에서 DriveManager가 하는 일들을 JDBC Template에게 맡긴다. 개발자는 쿼리로 질의 할수 있음
>**JDBC Template은 SQL의 Mapper중 하나임(Mybatis도 SQL Mapper중 하나임)

```
public class CarDao {
    private String driver = "com.mysql.cj.jdbc.Driver";
    private String url = "localhost:3306";
    private String userid = "USER_ID";
    private String userpw = "USER_PASSWORD";

    private DriverManagerDataSource dataSource;
    private JdbcTemplate template;

    public CarDao() {
        dataSource = new DriverManagerDataSource();
        dataSource.setDriverClass(driver);
        dataSource.setJdbcUrl(url);
        dataSource.setUser(userid);
        dataSource.setPassword(userpw);

        template = new JdbcTemplate();
        template.setDataSource(dataSource);
    }

    // CRUD
    public int carInsert(Car car) {
        String query = "INSERT INTO car (car_id, car_brand, car_created) VALUES (?, ?, ?)";
        int result = template.update(query, car.getId(), car.getBrand(), car.getCreated());

        return result;
    }
 }
```
