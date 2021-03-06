# jQuery 정리

* jQuery를 웹 페이지에서 사용하기

  1. <https://developers.google.com/speed/libraries/#jquery> 

  2. 3.x snippet: 을 html파일에 붙여넣기

     ```html
     <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
     ```

     

* $ = jQuery

  ```javascript
  $('h1'); /*h1태그 모두 가져옴, jQuery객체 반환*/
  $('h1').eq(index); /*index번째 h1태그-객체 반환*/
  $('h1:eq(index)'); /*index번째 h1태그-객체 반환*/
  $('h1')[index]; /*index번째 h1태그-노드 반환*/
  $('h1').children(); /*h1의 자식 태그-객체를 모두 반환*/
  $('h1').parent(); /*h1의 바로 윗 부모 태그-객체만 반환*/
  $('h1').parents(); /*h1의 모든 부모 태그-객체 반환*/
  $(selector).each((index, item)=>{ /*function content*/ });
  /*태그-객체에 대해, item은 태그-노드*/
  array.forEach((item, index)=>{/*function content*/});/*(비교)배열에 대하여*/
  $(text); /*태그-객체 생성*/
  parentObj.append(html); /*html을 parentObj객체의 노드에 자식으로 추가*/
  parentObj.append(childObj);
  parentObj.prepend(childObj);
  neighborObj.after(obj);
  neighborObj.before(obj);
  childObj.appendTo(parentObj); /*자식 노드로 뒤에 추가*/
  childObj.prependTo(parentObj); /*자식 노드로 앞에 추가*/
  obj.remove(); /*선택된 요소와 그 자식 요소를 삭제*/
  obj.empty(); /*선택된 요소의 자식 요소만 삭제*/
  
  var d =$(selector);
  /*text*/
  d.text(); /*text를 반환*/
  d.text(textContent); /*textContent를 저장*/
  
  /*html*/
  d.html();
  d.html(htmlContent);
  
  /*val*/
  d.val();
  d.val(valText);
  
  /*css*/
  d.css();
  d.css(cssContent);
  
  /*attr*/
  d.attr();
  d.attr(attrContent);
  
  /*position*/
  d.position(); /*{top:xxx, left:yyy}값 반환*/
  
  /*이벤트 처리*/
  d.eventMethod(function(){ /*예시*/
     /*function content*/ 
  });
  /*이벤트 메소드 종류*/
  click, mouseenter, mouseleave, submit, change, focus, blur, keypress, keydown, keyup, 
  load, unload, scroll
  
  /*애니메이션*/
  d.show(duration, complete);/*duration: "slow", "fast", millisecond,     */ 			   
  d.hide(duration, complete);/*complete: callback function                */
  d.toggle(duration, complete);
  d.fadeIn(duration, complete);/*show, hide와 비슷*/
  d.fadeOut(duration, complete);
  d.slideUp(duration, complete);
  d.slideDown(duration, complete);
  d.stop(); /*애니메이션 도중에 중단*/
  
  /*크기*/
  d.height();
  d.height(size);
  d.width();
  d.width(size);
  ```



* 드래그와 드롭

  ```html
  <!--드래그, 드롭 이벤트-->
  dragstart, drag, dragenter, dragleave, dragover, drop
  <!--예시-->
  <img src="xxx" draggble="true" ondragstart="func1(event)">
  <div id="cart" ondragover="func2(event)" ondrop="func3(event, this)" >
  </div>
  ```
  
  ```javascript
  /*****사용예시******/
  function func1(e){
      e.dataTransfer.effectAllowed = 'move';
  	e.dataTransfer.setData("Text", e.target.id);
  }
  function func2(e){
      e.preventDefault();
  }
  function func3(e, d){
      e.preventDefault();
  	var src = e.dataTransfer.getData("Text");
  	d.appendChild(document.getElementById(src));
  }
  ```
  
  



* HTML5 위치 정보

  ```javascript
  var geo = navigator.geolocation; /*객체*/
  
  geo.getCurrentPosition(callback, errorHandler);/*callback메소드와 errorHandler메소드*/
  var id = geo.watchPosition(callback, errorHandler); /*주기적으로 실행*/
  geo.clearWatch(id);/*watchPosition을 중지*/
  function callback(location){ /*callback메소드는 location매개변수를 받는다*/
      var co = location.coords; /*장치의 위치정보를 가지고 있는 객체*/
      var latitude = co.latitude;   //위도
      var longitude = co.longitude; //경도
      var altitude = co.altitude;   //고도
      var heading = co.heading;     //방위각
      var speed = co.speed;         //속도
  }
  ```

  

* 구글맵 사용

  ```html
  <script src="https://maps.googleapis.com/maps/api/js?
               key=YOUR_API_KEY&callback=initMap"
      		 async defer></script>
  <div id="map"></div>
  <script>
  var map, marker, cen;
  function initMap() {
      cen = {lat: -34.397, lng: 150.644}
  	map = new google.maps.Map(document.getElementById('map'), {
  		center: cen,
  		zoom: 8
  	});
      marker = new google.maps.Marker({
  		position: cen,
  		map: map,
  		label: "me"
  	});
  }
  </script>
  ```

  

* Worker

  1. `worker.js` 와 `receive.js` 가 있다.

  2. ```javascript
     /*receive.js*/
     w = new Worker('./js/worker.js'); /*worker.js의 메세지를 수신할 준비*/
     ```

  3. ```javascript
     /*worker.js*/
     postMessage(result);/*작업 이후 result를 송신*/
     ```

  4. ```javascript
     /*receive.js*/
     w.onmessage = function(event){
         /*event.data를 통해 result핸들링*/
     }
     ```

  ```javascript
  /******사용예시******/
  /*receive.js*/
  var w;
  function wstart(){
  	w = new Worker("./js/worker.js");
  	w.onmessage = function(event){
  		$('#result').text(event.data);
  	};
  }
  function wstop(){
  	w.terminate();
  }
  /*worker.js*/
  var i = 0;
  function count(){
  	i++;
  	postMessage(i);
  	setTimeout("count()", 1000);
  }
  count();
  ```

  

* 웹 스토리지

  ```javascript
  /*스토리지 전역변수*/
  localStorage 
  sessionStorage 
  
  /******사용예시******/
  if(!localStorage.count)
  	localStorage.count = 0;
  localStorage.count += 1;
  ```

