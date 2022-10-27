### JDBC란?(Java DataBase Connectivity)
>자바 언어로 데이터 베이스 프로그래밍을 하기 위한 라이브러리이다.
JDBC는 DBMS에 종속되지 않는 관련 API를 제공한다. JDBC API는 JDK에서 제공하고, JDBC 프로그래밍을 위해서 JDBC는 드라이버가 필요하다.
DM에 맞는 드라이버가 필요하다.

### JDBC API 클래스
![](https://velog.velcdn.com/images/ihanseul731/post/ee292c6b-d8ee-43a0-afcd-b4221def9779/image.png)
- DB를 연결하여 테이블 형태의 자료를 참조
- SQL문을 질의
- SQL문의 결과를 처리
### JDBC를 이용한 DB연동 과정
![](https://velog.velcdn.com/images/ihanseul731/post/b576eb04-96a2-4934-80d7-879bcf86b4db/image.png)
```
import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

public class JdbcMemberRepository implements MemberRepository {

    private final DataSource dataSource;
    
    public JdbcMemberRepository(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    @Override
    public Member save(Member member) {
        String sql = "insert into member(name) values(?)";
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
            pstmt.setString(1, member.getName());
            pstmt.executeUpdate();
            rs = pstmt.getGeneratedKeys();
            if (rs.next()) {
                member.setId(rs.getLong(1));
            } else {
                throw new SQLException("id 조회 실패");
            }
            return member;
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    @Override
    public Optional<Member> findById(Long id) {
        String sql = "select * from member where id = ?";
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setLong(1, id);
            rs = pstmt.executeQuery();
            if (rs.next()) {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                return Optional.of(member);
            } else {
                return Optional.empty();
            }
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    @Override
    public Optional<Member> findByName(String name) {
        // 생략
    }

    @Override
    public List<Member> findAll() {
        String sql = "select * from member";
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql);
            rs = pstmt.executeQuery();
            List<Member> members = new ArrayList<>();
            while (rs.next()) {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                members.add(member);
            }
            return members;
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    private Connection getConnection() {
        return DataSourceUtils.getConnection(dataSource);
    }

    private void close(Connection conn, PreparedStatement pstmt, ResultSet rs) {
        try {
            if (rs != null) {
                rs.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            if (pstmt != null) {
                pstmt.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            if (conn != null) {
                close(conn);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private void close(Connection conn) throws SQLException {
        DataSourceUtils.releaseConnection(conn, dataSource);
    }
}
```

### Plain JDBC API의 문제점
> 순수 JDBC 방법 사용시 반복해서 코드가 비효율 적이여서 jdbc template를 사용한다. 
```
conn=getConnection();
pstmt=conn.prepareStatement(sql);
rs=pstmt.executeQuery();
```

### JDBC Template

```
package com.test01;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class JDBCTest01 {

	public static void main(String[] args) throws ClassNotFoundException, SQLException {
		//1. Driver 등록
		Class.forName("com.mysql.jdbc.Driver"); //어느 디비랑 연결할건지
		
		//2. DBMS연결 connection객체 생성
		String url ="jdbc:mysql://localhost/multi";
		String user="root";
		String pw="1234";
		
		Connection con=DriverManager.getConnection(url, user,pw); //매개변수 3개 필요
		
		//3. 쿼리 준비
		String sql=" SELECT * FROM EMPLOYEE ";
		
		Statement stmt=con.createStatement();
		
		//4. 쿼리 실행 및 결과 리턴
		ResultSet rs=stmt.executeQuery(sql);
		
		System.out.println(rs);
		
		while(rs.next()) {
			
			System.out.println(rs.getInt(1)+" "+rs.getString(2)+" "+rs.getInt("SALARY"));
		}
		
		//5. db종료
		rs.close();
		stmt.close();
		con.close();
	}

}
```
