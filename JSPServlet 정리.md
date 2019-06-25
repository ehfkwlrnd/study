# JSP/Servlet 정리

* .jsp는 WAS에 의해 .java파일로 변환된다. 

  변환된 java파일 확인 경로 :

  C:\workspace.metadata\.plugins\org.eclipse.wst.server.core\tmp0\work\Catalina\localhost\day02\org\apache\jsp




* HelloServlet.java

  ```java
  @WebServlet({ "/Hello", "/hello.do" })/*애너테이션을 이용한 url설정*/
  /*Servlet의 생명주기는 (Constructor - init - service - destroy) */
  /*웹 서버 실행과 동시에 로드(constructor, init) 시키련면 아래와 같이 작성 */
  @WebServlet(urlPatterns = { "/Hello", "/hello.do" }, loadOnStartup = 1)
  public class HelloServlet extends HttpServlet {
      @Override
  	public void service(HttpServletRequest request, 
                          HttpServletResponse response) 
          				throws ServletException, IOException 
      {
  		/*request, response 인코딩*/
     		request.setCharacterEncoding("utf-8");
  		response.setContentType("text/html;charset=utf-8");
      
      	/*response에 텍스트 기록*/
      	PrintWriter out = response.getWriter();
  		out.print('<h1>title</h1>');
      
      	/*request에 전달된 값을 전달*/
      	String name = request.getParameter("name");
      	/*select, checkbox와 같은 요소를 받아올 때*/
      	String[] names = request.getParameterValues("name");
      }
  }
  ```

  

* UploadFileServlet.java

  ```java
  @WebServlet({ "/UploadServlet", "/upload.do" })
  /*아래는 파일 업로드를 처리하기 위해 붙여주는 애너테이션*/
  @MultipartConfig(location = "c:/upload", maxFileSize = 1024*1024*5)
  /*location : 파일이 저장될 위치, maxFileSize : 최대파일크기(byte)*/
  public class UploadServlet extends HttpServlet {
  	@Override
  	public void service(HttpServletRequest request, 
                          HttpServletResponse response) 
          				throws ServletException, IOException 
      {
          /*폴더가 없다면 생성*/
          String path = "c:/upload";
  		File dir = new File(path); 
  		if(!dir.isDirectory()) {
  			dir.mkdir();
  		}
          
          /*파일들을 Collection으로 얻어옴, 파일 하나가 part하나*/
          Collection<Part> parts = request.getParts();
          for(Part part:parts){
              String filename = p.getSubmittedFileName();
            p.write(filename); /*파일저장*/
              /*filename='test.png'라 하면 loacation=c:/upload이므로*/
              /*c:/upload/test.png로 저장됨*/
          }
      }
  }
  ```
  
  

* JSP코드

  ```jsp
  <% /*Java code*/ %> //스크립틀릿(자바 코드)
  <%= /*Java variable* /%> //표현식(문자열로 변환)
  <%-- /*Comment*/ --> //JSP주석
  <%! /*declare function or variable*/ %> //변수나 함수 선언
  <%@ /*페이지 전체에 지시*/ %>
  <%@ page import="java.util.xxxx"%> //사용예시, 임포트
  ```

  ```java
  String param = request.getParameter(paraname);
  /*html요소의 value를 리턴한다. 이 때 요소의 name이 paraname이 된다.*/
  String[] params = request.getParameterValues();
  ```

* 커스템 에러 페이지

  ```java
  /*hello.jsp*/
  <%@ page errorPage="error.jsp"%>
  
  /*error.jsp*/
  <%@ page isErrorPage="true"%>
  <p><%= exception %></P> /*에러페이지로 지정되면 exception을 전역변수로 사용할 수 있다.*/
  	
  ```



* Model2 아키텍쳐 (MVC [Model-View-Control 패턴])

  Model : Javabeans, View : html, jsp,  Controller : servlet



* 리다이렉트, 요청 디스패치

  ```java
  /*리다이렉트*/
  response.sendRedirect("test.jsp"); /*request공유 불가*/
  
  /*요청 디스패치*/
  RequestDispatcher rdp = getServletContext().getRequestDispatcher("test.jsp");
  rdp.forward(request, response); /*request공유*/
  ```

  

* 세션 트래킹

  서버는 각 세션에 대한 데이터를 저장하기 위해 **세션 객체**를 이용한다.

  ```java
  HttpSession session = request.getSession();
  session.setAttribute("name", "John"); /*(key, value)*/
  String name = (String)session.getAttribute("name"); /*return Object*/
  session.removeAttribute("name"); /*key*/
  session.invalidate(); /*세션 종료*/
  ```




* taglib

  ```java
  /* https://mvnrepository.com/ 검색
   * jstl검색
   * jstl.jar ver1.2다운 */
  
  /*페이지 상단에 선언*/
  <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
      
  /*사용예시*/
  <c:forEach var="book" items=${list}>
  		<tr><td>${book.bookno}</td></tr>
  </c:forEach>
  ```

  