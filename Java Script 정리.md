# Java Script 정리



* html 문서에 Java Script 포함 시키기

  ```html
  <script src="./js/myjs.js"></script>
  ```

  

* 입출력 함수

  ```javascript
  alert('Hello JavaScript');
  console.log('text');
  prompt('input your name', 'default text'); /*입력 스트링을 반환*/
  confirm('confirm test'); /*true or false 값을 반환*/
  ```




* 타이머 함수

  ```javascript
  setTimeout(func, time); /*time시간 후에 func실행*/
  setInterval(func, time); /*time시간 마다 func실행*/
  clearInterval(id); /*특정 시간마다 실행하는 함수 호출 정지*/
  /*사용 예시*/
  id = setInterval(func, time);
  clearInterval(id);
  ```

  

* 객체

  ``` javascript
  /*객체 상수를 사용하여 객체 생성 (싱글톤)*/
  var myCar = { 
      model: "benz",
      speed: 60,
      color: "red",
      brake: function() { this.speed -= 10;},
      accel: function() { this.speed += 10;}
  };
  
  /*생성자 함수를 사용하여 객체 생성*/
  function Car(model, speed, color){ 
      this.model = model;
      this.speed = speed;
      this.color = color;
      this.brake = function() {
          this.speed -= 10;
      }
      this.accel = function() {
          this.speed += 10;
      }
  }
  var myCar = new Car("benz", 90, "yellow"); /*생성시 new연산자 사용*/
  myCar.turbo = true; /*객체에 새로운 속성 추가 가능*/
  myCar.showModel = function() { /*객체에 새로운 메소드 추가 가능*/
      alert('model : ' + this.model);
  }
  
  /*속성 접근 방법*/
  console.log(myCar.model);
  console.log(myCar['model']);
  
  /*for문을 이용한 접근 방법*/
  /*for(let data of myCar) Error : for-of으로 접근할 수 없음*/
  for(let key in myCar){ /*for-in을 통해 key에 접근*/
      console.log(`${key} : ${myCar[key]}`);
  }
  ```

  

* 프로토타입

  ```javascript
  /*생성자 함수*/
  function Car(model, speed){ 
      this.model = model;
      this.speed = speed;
  }
  Car.prototype.color = "yellow";
  Car.prototype.brake = function() { 
      this.speed -= 10;
  }
  /*Car객체에 대해 숨겨진 객체 Car.prototype이 존재*/
  /*프로토타입 안에 정의하면 모든 Car객체가 공유할 수 있음*/
  ```



* 오버라이딩

  ```javascript
  var s = { /*객체 생성, print메소드 존재*/
      print: function(){console.log('hello');}}
  
  s.print = function() {console.log('overriding');}/*print메소드 오버라이딩*/
  ```



* JSON

  ``` javascript
  {"name":"Hail", "age":28, "student":false}
  /*속성명은 반드시 double quotation으로 감싸줘야 함*/
  /*속성값은 string, number, boolean만 가능*/
  
  s = JSON.stringify(object); /*객체를 문자열로 반환*/
  obj = JSON.parse(str); /*문자열을 객체로 파싱*/
  ```

  

* DOM 

  ```javascript
  var node = document.getElementById('id'); /*Node를 반환*/
  var node = document.querySelector(selector) /*Node를 반환(최신 버전)*/
  var childrens = node.childNodes; /*자식 Nodes를 배열로 반환*/
  var first = node.firstChild; /* == node.childNodes[0];*/
  var parent = node.parenNode;
  var sibling = node.nextSibling; /*바로 다음 형제 Node를 반환*/
  var sibling = node.previousSibling;
  
  node.attr = "attr value"; /*속성값 변경*/
  document.getElementById('image').src = "./img/image01.jpg"; /*예시*/
  document.getElementById('text').style.color = 'red';
  document.getElementById('header').style.visibility = 'hidden';
  
  /*createTextNode : 텍스트 데이터를 가진 노드를 생성*/
  if(document.createTextNode){ /*기능이 지원되는지 확인*/
      var node = document.createTextNode(text); /*text데이터를 갖는 노드 생성*/
      parentNode.appendChild(node); /*parentNode에 node를 자식으로 추가*/
  }
  parentNode.removeChild(childNode); /*parentNode로부터 childNode를 삭제*/
  ```

  

* BOM

  ```javascript
  /*window.xxx*/
  window.open(href, ?, feature); /*새 창 열기*/
  window.open('http://www.naver.com', '네이버새창', 'width=640, height=480'); /*예시*/
  /*location 속성과 함수들*/
  location.href /*전체 URL 반환*/
  location.pathname /*URL중 경로를 반환*/
  location.port /*URL중 port를 반환*/
  location.assign(href) /*새로운 문서를 로드*/
  location.reload() /*현재 문서를 재로드*/
  location.replace(href) /*현재 문서를 새로운 문서로 대체*/
  
  /*navigator 속성과 함수들*/
  navigator.appName /*브라우저 이름*/
  navigator.cookieEnabled /*쿠키 활성화 여부*/
  navigator.onLine /*인터넷에 연결 되어 있으면 true*/
  navigator.userAgent /*브라우저에서 서버로 가는 user-agent헤더*/
  
  /*event 종류*/
  onclick /*클릭할 때*/
  onmouseup /*마우스 버튼이 떼어졌을 때*/
  onmousedown /*마우스 버튼이 눌렸을 때*/
  onmouseover /*HTML요소 위에 마우스를 올릴 때*/
  onmouseout /*HTML요소 위에서 마우스가 떠날 떄*/
  onmouseenter /*HTMl요소 안으로 마우스가 들어올 때*/
  onfocus /*입력 필드를 마우스로 눌렀을 때*/
  onblur /*입력 필드의 포커스가 떠났을 때*/
  onchange /*입력 내용이 변경 되었을 때*/
  onkeydown /*키보드가 눌렸을 때*/
  onload /*웹 페이지 진입할 때*/
  
  window.onload = function(){ /*body태그 보다 위에 있어도 메모리에 load된 후 실행 되므로
  							 *접근에러가 생기지 않는다.*/
      //function contents
}
  ```
  
  

