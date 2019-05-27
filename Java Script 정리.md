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

