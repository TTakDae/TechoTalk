# RESTful

#### REST (REpresentational State Transfer)

- 분산 하이퍼미디어 시스템을 위한 소프트웨어 아키텍쳐 (MDN)
  네트워크 리소스를 정의하고 처리하는 방법을 설명하는 일련의 원칙을 기반으로 하는 아키텍쳐 스타일



- REST에 대한 원칙을 잘 지켰을때, RESTful하다고 함.
- 자원(리소스)의 표현에 의한 상태(정보) 전달
- 자원의 표현은 rest의 가장 큰 특징 중 하나
- 리소스를 uri에 표현을 하기 때문에 주고받는 정보에 대해 어느정도 예측을 할 수 있음



#### uri에는 동사를 사용하지 않는다

자원에 대한 행위를 표현하기 위해서 httpMethod를 사용함

- Create -> Post
- Read -> Get
- Update -> Put, Patch
- Delete -> Delete



#### 표현의 어려움

ex. 로그인 로그아웃.. 예외적으로 허용( `/login` `/logout` ) => 이렇게 하면 틀린걸까??

REST는 아키텍쳐 스타일일 뿐, 모든 원칙들을 지키지 않는다고 해서 API가 틀린것은 아님.



#### 정리

- HTTP를 잘 활용하기 위해서 만들어진 아키텍쳐이다

- URI와 HTTP Method를 사용해서 자원과 행위를 표현한다

- API의 의미를 표현하기 쉽고, 의미를 파악하기도 쉽다.



