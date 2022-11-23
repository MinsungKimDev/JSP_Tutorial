## JSP 게시판 만들기 튜토리얼
이 레포지토리는 [누구나 따라하면서 배우는 JSP 커뮤니티 게시판 만들기] 강좌를 학습하기 위하여 생성되었습니다.

### 1강  Hello World!
- 자바, 이클립스, 톰캣8.5를 설치하여 프로젝트 작성을 위한 준비
- 이클립스에서 Dynamic Web Project를 생성하고 index.jsp 생성
- 톰캣8.5 서버를 동작하여 index.jsp 페이지 띄우기

### 2강  로그인 페이지 디자인
- Bootstrap 3.3.7-dist를 프로젝트 폴더로 복사
- Bootstrap을 이용하여 반응형 웹페이지 login.jsp 작성

### 3강  회원 데이터베이스 구축하기
- mysql 설치 및 User 테이블 생성, 더미데이터 1개 생성, 클래스 작성
- 이런식으로 하나의 데이터를 관리하고 처리할 수 있는 기법을 JSP에서 구현한 것을 자바 빈즈라 한다.

### 4강  로그인 기능 구현하기
- User DAO(Data Access Object) 작성
- loginAction.jsp 작성
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