## JSP 게시판 만들기 튜토리얼
이 레포지토리는 [누구나 따라하면서 배우는 JSP 커뮤니티 게시판 만들기] 강좌를 학습하기 위하여 생성되었습니다.

### 1강  Hello World!
- 자바, 이클립스, 톰캣8.5를 설치하여 프로젝트 작성을 위한 준비
- 이클립스에서 Dynamic Web Project를 생성하고 index.jsp 작성
- 톰캣8.5 서버를 동작하여 index.jsp 페이지 띄우기

---

### 2강  로그인 페이지 디자인
- Bootstrap 3.3.7-dist를 프로젝트 폴더로 복사
- Bootstrap을 이용하여 반응형 웹페이지 login.jsp 작성

---

### 3강  회원 데이터베이스 구축하기
- mysql 설치 및 User 테이블 생성, 더미데이터 1개 생성, 클래스 작성
- 이런식으로 하나의 데이터를 관리하고 처리할 수 있는 기법을 JSP에서 구현한 것을 자바 빈즈라 한다.

---

### 4강  로그인 기능 구현하기
#### UserDAO.java 작성
- java - mysql 연결 설정
    > 1. mysql-connector-java-8.0.28.jar 파일 프로젝트에 추가
    > 2. src/main/webapp/WEB-INF/lib 폴더에 jar 파일 복사
    > 3. 프로젝트에 우클릭 - 맨밑 Properties 선택
    > 4. java build path - Libraries 탭 - Modulepath 클릭
    > 5. Add JARs... - 복사한 jar 파일 경로 찾아서 선택 - Apply and Close
- 데이터베이스 접속 코드
    ```java
    String dbURL = "jdbc:mysql//localhost:3306/bbs"; // 접속 실패시 URL 틀리지 않았는지 확인 할 것
    String dbID = "root";
    String dbPassword = "1234"; 

    // Class.forname("com.mysql.jdbc.Driver"); // 더이상 사용안함
    Class.forname("com.mysql.cj.jdbc.Driver"); // 이렇게 사용해야함

    conn = DriverManager.getConnection(dbURL, dbID, dbPassword);
    // conn은 생성자 밖에 private으로 생성한 Connection 객체
    ```
- SQL injection 공격을 막기 위하여 prepareStatement를 이용
    ```java
    public class UserDAO {
        private PreparedStatement pstmt;
    //                    ⁝
        public int login(String userID, String userPassword) {
            String SQL = "SELECT userPassword FROM USER WHERE userID = ?";
            pstmt = conn.preparedStatement(SQL);
            pstmt.setString(1, userID); // SQL 문자열의 ? 부분에 userID를 포함시켜줌
    //                    ⁝
        }
    }
    ```

#### loginAction.jsp 작성
- jsp 파일에서 자바 문법을 사용하려면 `<% %>` 태그 안에 작성한다.
- jsp 안에서 자바문법을 사용하다 html 태그로 내보내고 싶다면 `java.io.PrintWriter`를 import 하고 다음과 같이 사용한다. 
    ```java
    PrintWriter script = response.getWriter();
    script.println("<div>이런식으로 작성</div>");
    ```
- 스크립트를 이용하여 이전페이지로 되돌아가게 하는 방법은 다음과 같다.
    ```java
    script.println("<script>");
    script.println("alert('비밀번호가 틀립니다.')");
    script.println("history.back()"); // 요부분
    script.println("</script>");
    ```

---

### 5강 회원가입 페이지 디자인
#### join.jsp 작성
- 성별 선택 라디오 버튼을 사용하려면 html로 다음과 같이 작성한다.
- ※ 스타일은 <b>bootstrap 3.3.7 버전</b>이다 ※
    ```html
    <div class="form-group" style="text-align: center;">
        <div class="btn-group" data-toggle="buttons">
            <label class="btn btn-primary active">
                <input type="radio" name="userGender" autocomplete="off" value="남자" checked>남자
            </label>
            <label class="btn btn-primary">
                <input type="radio" name="userGender" autocomplete="off" value="여자" >여자
            </label>
        </div>
    </div>
    ```

---

### 6강 회원가입 기능 구현하기
#### joinAction.jsp 작성
-  jsp파일에 User 객체의 나머지 멤버 모두 명시 (userName, userGender, userEmail)
    ```jsp
                            ⁝
    <jsp:setProperty name="user" property="userEmail"/>
                            ⁝
    ```
- 다음과 같은 로직 수행
    > 1. join.jsp에서 회원가입 정보 입력 후 회원가입 버튼 누르면 joinAction.jsp 수행
    > 2. joinAction.jsp에서는 user 객체에 정보를 담아 UserDAO.join(user); 수행
    > 3. UserDAO.join 에서는 user의 정보를 SQL에 담아 SQL 실행 - DB에 등록
    > 4. 정상적으로 등록되었으면 login.jsp로 이동, 이미 존재하는 ID일경우 다시 join.jsp로 이동

---

### 7강 접속한 회원 세션 관리하기
#### 사용자 세션 생성하기
- login.jsp에서 로그인에 성공하였다면 회원의 세션을 만들어준다.
- 그 다음 메인 페이지로 이동하도록 스크립트를 이용한다.
    ```java
    session.getAttribute("userID", user.getUserID()); 
    // 세션에 userID라는 항목에 user.getUserID()를 하여 값을 참조시킨다.
    PrintWriter script = response.getWriter();
    script.println("<script>");
    script.println("location.href = 'main.jsp'");
    script.println("</script>");
    ```

#### logout.jsp 작성
- logout.jsp에 접속할 경우 사용자의 세션을 해제한다.
    ```java
    session.invalidate();
    ```
- 그 다음 메인페이지로 이동하도록 스크립트를 작성한다.

### main.jsp 수정
- 이제 index.jsp에 접속하면 main.jsp에 접속하도록 코드 수정
- main.jsp에서 사용자가 로그인 한 상태라면 우측 접속하기가 '로그인, 회원가입'이 아닌 '로그아웃'이 보이도록 코드 작성
- jsp if문 사이에 html 태그를 끼워넣으면 조건을 만족할때만 html 태그가 표시된다.
    ```jsp
    <%
        if(userID == null) { // 이 조건을 만족해야 아래 태그가 표시된다.	
	%>
        <ul class="nav navbar-nav navbar-right">
            <li class="dropdown">
                <a href="#" class="dropdown-toggle"
                    data-toggle="dropdown" role="button" aria-haspopup="true"
                    aria-expanded="false">접속하기<span class="caret"></span></a>
                <ul class="dropdown-menu">
                    <li><a href="login.jsp">로그인</a></li>
                    <li><a href="join.jsp">회원가입</a></li>
                </ul>
            </li>
        </ul>
    <%
        }
    %>
    ```

---

### 8강 게시판 메인 페이지 디자인
#### bbs.jsp 파일 작성
- main.jsp 파일을 복사하여 아래 내용을 추가하였다.
    ```html
    <div class="container">
		<div class="row">
			<table class="table table-striped" style="text-align: center; border: 1px solid #dddddd">
				<thead>
					<tr>
						<th style="background-color: eeeeee; text-align: center;">번호</th>
						<th style="background-color: eeeeee; text-align: center;">제목</th>
						<th style="background-color: eeeeee; text-align: center;">작성자</th>
						<th style="background-color: eeeeee; text-align: center;">작성일</th>
					</tr>
				</thead>
				<tbody>
					<tr>
						<td>1</td>
						<td>안녕하세요</td>
						<td>홍길동</td>
						<td>2022-11-25</td>
					</tr>
				</tbody>
			</table>
			<a href="write.jsp" class="btn btn-primary pull-right">글쓰기</a>
		</div>
	</div>
    ```

---

### 9강 게시판 데이터베이스 구축하기
- Java resources에 bbs 패키지 생성
- Bbs.java 작성, getter setter 작성
- MySQL에 bbs 테이블 생성

---

### 10강 글쓰기 기능 구현하기
- write.jsp 작성
- BbsDAO.java 작성
- writeAction.jsp 작성

---

### 11강 게시판 글 목록 기능 구현
- bbs.jsp에 글 목록 기능구현

---

### 12강 게시판 글 보기 기능 구현하기
- view.jsp 작성
- request.Parameter("bbsID") 와 같이 queryString에서 값을 가져오는 방법 학습

---

### 13강 게시글 수정 및 삭제 기능
- update.jsp 작성
- updateAction.jsp 작성
- 사용자 입력 내용 필터링 방법 학습
```java
    bbs.getBbsContent().replaceAll(" ", "&#38;nbsp;").replaceAll("<" , "&#38;lt;").replaceAll(">", "&#38;gt;").replaceAll("\n", "&lt;br>");
```

---

### 14강 웹사이트 메인 페이지 디자인
- 부트스트랩 carousol 사용

---

### 15강 프로젝트 완성 및 배포
- 카페24 웹호스팅 서비스 이용방법 학습