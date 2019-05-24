# CSS3 정리

* css는 html 문서에 직접 적을 경우 head태그 안의 style태그 안에 적는다. 

```html
<head>
    <style>
        p {
            color: red;
        }
    </style>
</head>
```

* css선택자의 종류

  1. 전체 선택자

     ``` css
     {color: blue;}
     ```

  2. 타입 선택자

     ```css
     p {color: blue;}
     ```

  3. 아이디 선택자

     ``` css
     #special {color: blue;}
     ```

  4. 클래스 선택자

     ``` css
     .target {color: blue;}
     ```

* 의사 클래스

  ``` css
  :link /*방문되지 않은 링크*/
  :visited /*방문되 링크*/
  :hover /*마우스가 링크 위에 있을 때*/
  :active /*클릭하고 있는 상태*/
  :checked /*체크된 상태의 input 요소를 모두 선택*/
  
  
  table:nth-child(1) /*첫 번재 요소*/
  table:nth-child(2n) /*짝수번째 요소*/
  table:nth-child(2n+1) /*홀수번째 요소*/
  ```

  

* 속성 선택자

  ``` css
  h1[title] /*title 속성을 가지고 있는 h1요소를 선택*/
  p[class="example"] /*class속성이 example인 p요소를 선택*/
  ```



* 외부 스타일 시트를 연결하는 법

  ``` html
  <link type="text/css" rel="stylesheet" href="mystyle.css">
  ```



* 자주 사용하는 속성

  ``` css
  font-weight: bold; /*볼드체 설정*/
  font-size: 10em; /*폰트의 크기*/
  font-style: italic; /*폰트 스타일 변경*/
  
  color: blue; /*텍스트 색상*/
  text-align: center /*텍스트 정렬*/
  text-decoration: underline; /*텍스트 장식*/
  text-transform: uppercase; /*대소문자 변환*/
  text-shadow: 3px 3px 3px gray; /*텍스트 그림자 (x방향, y방향, 흐림정도, 색상)*/
  
  border: 1px solid black; /*경계선 (width, style, color)*/
  border-style: dotted solid double dashed; /*경계선 스타일 (상 하 좌 우)*/
  border-radius: 10px; /*둥근 경계선*/
  border-collapse: collapse; /*이웃한 셀의 경계선 합침*/
  box-shadow: 20px 10px 5px black; /*경계선 그림자 (x방향, y방향, 흐림정도, 색상)*/
  
  background-color: blue; /*배경색 변경*/
  list-style: none; /*리스트 스타일 변경*/
  margin: auto; /*자손들 가운데 정렬*/
  vertical-align: center; /*수직 정렬*/
  
  display: inline; /*인라인으로 변경*/
  display: hidden; /*화면에서 감춰짐*/
  display: none; /*없는 것으로 간주됨*/
  
  ```
* 요소 위치 정하기

  ```css
  position: static; /*디폴트 상태*/
  position: absolute; /*컨테이너의 원점이 기준*/
  position: relative; /*정상정인 위치가 기준*/
  position: fixed; /*윈도우의 원점이 기준*/
    
  #one {
      position: relative;
      left: 30px;
      top: 10px;
  }
    
  float: left; /*위치 정할 때*/
  clear: both; /*float의 흐름을 제거*/
  ```

* overflow 속성

  ```css
  overflow: hidden; /*부모 영역을 벗어나면 보이지 않게 함*/
  ```

  