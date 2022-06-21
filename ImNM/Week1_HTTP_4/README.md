# HTTP 메서드와 상태 코드

## 1. HTTP패킷 구조

![IMG_3096](https://user-images.githubusercontent.com/13329304/174727937-6e9c6646-e319-458b-8e82-bc21154e6a75.PNG)

- 시작 라인 - 요청 , 응답 메시지와 구성이 다름 응답시에 상태코드 반환
- 헤더 ( location , content-type 과 같은 헤더값)
- 공백라인 ( 헤더와 메시지 바디 구분을 위함 )
- 메시지 바디 ( 추가적인 정보 )

## 2. HTTP 메서드

![IMG_3097](https://user-images.githubusercontent.com/13329304/174727952-7f0001c3-a9a4-4788-85f7-b596ae0d1887.PNG)

- POST 응답 메시지에는 location 헤더 필드가 들어갈 수있음.
  즉 POST /users 를 해서 유저를 생성할시에
  location : /users/11 (11번째 유저면 ) 해당 정보가 들어감
- PUT 과 PATCH 의 차이점
  리소스를 완전히 대체와 부분 변경의 의미
  가령 유저객체에 전화번호와 이름 필드가 있다고 가정
  PUT -> 전화번호 필드만 post 에 담아서 보내면 유저객체에 변경된 전화번호 필드만 있게됨
  PATCH -> 전화번호 필드만 post 에 담아서 보내면 유저객체에 변경된 전화번호,이름 필드가 있게 됨.

## 3. HTTP 상태코드

### 2xx

- 200 OK
- 201 Created
  주로 Post 요청시에 생성시
- 202 Accepted
  이후 작업을 할예정인 것들 , 메시지큐에 알림 요청을 보낸다던지... 그런경우
- 204 No content
  성공을 했지만 응답 페이로드에 보낼 데이타가 없을경우!

### 3xx

웹 브라우저는 3xx 응답결과에 Location 헤더가 있으면 Location 위치로 자동 이동.

- 301 Moved Permanently
  영구적인 리다이렉션
- 302 Found
  일시적인 리다이렉션
- 303 See Other
  일시적인 리다이렉션
- 304 Not Modified
  특수 리다이렉션 ( 웹브라우저의 캐시 처리 ) etag 사용
- 307 Temporary Redirect
  일시적인 리다이렉션
- 308 Permanent Redirect
  영구적인 리다이렉션

  서버는 클라이언트에 요청에 따라서 etag를 보내주고 클라이언트는 가지고 있는 etag와 비교하여 브라우저 내부에서 캐시처리를 진행하게 된다.

  express 서버의 경우 자동으로 etag를 설정하게 되어있는데
  동일한 Get 요청 즉 url ( query 든 param이든 ) 같은 조건의 패스면
  브라우저의 캐시처리를 기대하게 되어 304로 리다이렉션을 날린다.
  이때 클라이언트가 앱인경우, 그리고 캐시를 기대하지 않는 경우 ( 동일한 요청에 대하여 바뀐 값이 필요할경우 ) 앱이 느려지는 경우가 있다.
  따라서 etag 설정을 상황에 따라 꺼줄 필요가 있다.

### 4xx

- 400 Bad Request
  잘못된 문법이나 메시지를 요청해서 서버가 이해할 수 없음.
- 401 Unauthorized
  클라이언트가 로그인이 필요함 (로그인 )
- 403 Forbidden
  서버가 요청을 이해했지만 승인 거부
  로그인 했지만 정지된 유저인경우( 인가)
- 404 Not Found
  요청 리소스를 찾을 수 없음

### 5xx

- 500 Internal Server Error
  서버 내부 문제로 오류 발생

- 502 Bad GateWay
  서버간의 유효하지 않은 응답을 받은 경우

- 503 Service Unavaliable
  서버가 일시적으로 요청을 처리할 준비가 되지 않음.
  유지보수를 위한 작동이 중단되거나 과부하가 걸린 서버
