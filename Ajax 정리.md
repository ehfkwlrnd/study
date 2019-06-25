# Ajax 정리

* JQuery없이 사용예시

  ```javascript
  req = new HMLHttpRequest();
  req.onreadystatechange = function(){
      if(req.readyState == 4 && req.status == 200){
          /*function content(req.responseText 핸들링)*/
      }
  }
  req.open('GET', 'file.txt', true); /*(method, url, async[비동기여부])*/
  req.send();
  ```

  

* JQuery이용

  ```javascript
  $.get('test.jsp', function(data, status){/*$.get(url, callback)*/
      /*function content*/
  });
  
  $.post('test.jsp', {/*$.post(url, data, callback)*/
      name:'Hong',
      age:'21'
  },
  function(data, status){
      /*function content*/
  });
  
  $('#div').load('file.txt'); /*$(selector).load(url)*/
  
  $.ajax({           /*$.ajax({  environment  })*/
      url:'json.jsp',
      dataType:'json',
      success:function(data){
          /*content*/
      },
      error:function(error){
          /*content*/
      }
  });
  ```

  

* rss이용

  ```html
  <!--rss.jsp-->
  <!--c:import 사용하기 위해서 taglib추가 필요-->
  <%@ taglib prefix="c"  uri="http://java.sun.com/jsp/jstl/core" %>
  <c:import var="data" 
  url="http://www.kma.go.kr/weather/forecast/mid-term-rss3.jsp?stnId=109">
  </c:import>
  ${data}
  
  <!--get.js-->
  $.ajax({
      url:'rss.jsp'
      <!--나머지는 같음-->
  });
  ```

  