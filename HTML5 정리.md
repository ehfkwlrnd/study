# HTML5 정리

* target 속성

  _blank, _self, _parent, _top

  

* 오디오 속성

  autoplay, controls, loop, preload, src, volume(0부터 1까지 볼륨 설정)

  ``` html
  <audio src="old_pop.mp3" autoplay controls>
  	Your browser does not support the audio element.
  </audio>
  
  <!--아래와 같이 사용하는 것을 권장함-->
  <audio autoplay controls>
  	<source scr="old_pop.mp3" type="audio/mp3">
      <source scr="old_pop.ogg" type="audio/ogg">
      Your browser does not support the audio element.
  </audio>
  ```

  

* 비디오 속성

  autoplay, controls, loop, poster(비디오를 다운로드하는 중일 때 표시하는 이미지),

  preload, muted(비디오가 오디오의 출력을 중지), src, width, height

  ```html
  <video width="640" height="480" controls>
  	<source src="trailer.mp4" type="video/mp4">
      <source src="trailer.ogv" type="video/ogv">
      Your user agent does not support HTML5.
  </video>
  ```

  

* iframe

  ``` html
  <!--first-->
  <iframe src="inner.html" width="300" height="300"></iframe>
  
  <!--second-->
  <iframe src="" name="iframe1"></iframe>
  <a href="http://www.naver.com" target="iframe1">iframe안에서 열기</a>
  ```

  

* form 요소

  ```html
  <!--입력 양식은 항상 from으로 시작한다.-->
  <form action="input.jsp" method="post">
      <!--action은 입력을 처리하는 서버 스크립트이다.-->
      <!--method는 get과 post가 있다.-->
      <input type="text" name="input"/>
      <input type="submit">
  </form>
  ```

  

  * get 방식

    1. URL 주소에 사용자가 입력한 데이터를 붙이는 방법

       ```txt
       www.server.com/input.jsp?name1=value1&naem2=value2
       ```

    2. 글자 수 제한 (최대 2048자)

    3. 북마크가 가능하고 뒤로가기 가능

  * post 방식

    1. 사용자가 입력한 데이터를 HTTP Request 헤더에 포함시켜 전송
    2. 길이 제한 없음, 보안 유지
    3. 북마크 불가능, 뒤로가기 버튼을 누르면 데이터 다시 보내야 함.



* input 형식

  * type 속성

    text, password, radio, file, reset, image, hidden, submit, checkbox

    (추가)date, datetime, datetime-local, month, time, week, color, email, tel, search, range, number, url

    ```html
    <form>
        이름 : <input type="text" name="name"><br>
        패스워드 : <input type="password" name="pass"><br>
        
        성별 : 
        <label for="male">남성</label>
        <input type="radio" name="gender" value="male" checked>
        <label for="female">여성</label>
        <input type="radio" name="gender" value="female">
        <!--radio의 name속성은 동일해야 한다-->
        <!--checked 속성을 통해 기본체크를 한다-->
        
        <input type="checkbox" name="fruits" value="apple" checked>Apple
        <input type="checkbox" name="fruits" value="grape">Grape
        <!--checkbox의 name속성도 동일해야 한다-->
        
        <input type="button" value="calc" onclick="alert('1000원')">
        <input type="image" src="submit.png" alt="제출 버튼">
        <input type="textarea" name="feedback" rows="5" cols="50" readonly>
        <!--readonly 속성을 통해 쓰기 불가능-->
        
        <select name="cars">
            <option value="bmw" selected>BMW</option>
            <!--selected 속성을 통해 기본 선택을 한다-->
            <option value="benz">Benz</option>
            <option value="kia">KIA</option>
        </select>
        
        <!--입력요소 그룹핑-->
        <fieldset>
            <legend>인적사항입력</legend>
            <!--legend : 제목-->
            이름 : <input type="text"><br>
            주소 : <input type="password">
        </fieldset>
            
        <input type="file" accept="image/jpg, image/gif">
        <!--accept 속성을 통해 파일의 타입을 제한-->
        
        <input type="email" name="email" required>
        <input type="url" name="url" required>
        <!--required 속성을 통해 유효한지 검사할 수 있다-->
        
        <input type="tel" name="tel" required
               pattern="\d{3}-\d{4}-\d{4}"
               title="###-####-####">
        <!--pattern 속성의 정규식을 통해 유효성 검사할 수 있다.
    		title 속성을 통해 유효하지 않을 때 경고할 수 있다-->
        
        <input type="number" min="230" max="290" step="10" value="260">
        <!--min부터 max까지 step씩 디폴트는 value-->
    </form>
    ```