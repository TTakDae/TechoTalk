# 자바스크립트

## 싱글 스레드 , 논 블로킹

![사진3](https://user-images.githubusercontent.com/13329304/176104396-6e3b583f-3e48-4607-bb0a-c9abdac1b092.jpg)

- `싱글 스레드가 뭐야?`
  콜스텍이 하나인것 즉 한번에 하나의 함수만 실행 시킬 수 있다는 것. 오래 걸리는 작업이면 블로킹이 될 수 있다.
- `setTimeOut 함수 실행되면 어찌되는가..?`
  필자가 기존에 생각하던건 비동기적 함수가 나중에 task Queue에 담기는 상황은 알고있었는데
  예를 들어 3초의 시간이 지난뒤에 task queue에 담아주세요 하면 누가 그걸 3초뒤에 알려주나?? 라는 생각을 가지고 있었음

` 놓치고 있었던게 브라우저에는 V8 엔진 뿐만아니라 web Api 라는 것이 등장함. Node js 환경에서는 libuv 를 통해서 비동기 i/o를 지원`
즉 싱글스레드인 자바스크립트가 비동기적 처리를 할 수 있다는것은 web Api 나 libuv 같은 놈들 때문임!

1. 일단 setTimeOut 함수가 콜스택에서 실행되면서 web Api 를 이용해 타임 아웃 이벤트를 등록
2. 타임아웃 이벤트가 발생되면 테스크 큐에 실행될 콜백 함수를 담음.
3. 콜백함수를 적절한 타이밍에 실행시킴

- `적절한 타이밍?`
  Task 큐는 하나가 아님 이강좌에서는 세종류가 있다고 말해주지만 사실 두종류만 알고있으면 될듯.
  ![사진1](https://user-images.githubusercontent.com/13329304/176104412-8259b796-36d9-4a6c-bc1a-3dc0d1a0fdbc.jpg)

- MacroTaskQueue
  큐에있는 하나만 빼서 처리하고 다음 루프로 넘어감
- MicroTaskQueue
  중요포인트는 call Stack이 비자마자 바로 실행 된다는 것임, 그리고 프라미즈 then 같은걸 처리중에 같은 microTaskQueue가 쌓이면 그것도 다음 루프로 안넘기고 바로 처리함

이러한 루프를 돌면서 call stack 이 비는 그 시점에 브라우저 랜더링도 자바스크립트에 의해 처리되는 것임!

### 여기서 잠깐 ! 비동기 동기 뭔데~

```javascript
// 동기적인 forEach
[1, 2, 3, 4].forEach(function (i) {
  console.log(i);
});
```

```javascript
// 비동기적 forEach
function asyncForEach(array, cb) {
  array.forEach(function () {
    setTimeOut(cb, 0);
  });
}
asyncForEach([1, 2, 3, 4], function (i) {
  console.log(i);
});
```

저 둘의 차이점은 동기와 비동기인데
비동기를 통해서 다른 작업 ( 애니메이션이라던지 리랜더링이라던지 ) 을 할 수 있는 여유 시간을 준다고 보면됨

동기적인 `forEach`는 만약 저 시간이 오래걸리는 시간이라면
모든게 끝날 때까지 다른게 침범할 수 없는
즉 ( task 큐 )에 계속 정보가 쌓이게됨

비동기적인 `forEach`는 `setTimeOut` 을 통해 web Api 한테 0초 뒤에 알려달라고 넘김
그럼 `taskQueue`에 쌓이고 `taskQueue`에 쌓인걸 `call Stack` 으로 꺼내오면서 실행시키는거임
즉 `taskQueue` 에 다른 이벤트들 (클릭 이벤트들)또는 랜더링 로직이 있다면 `call Stack`으로 꺼내오면서
실행시킬수 있는 여유를 준다는 것

### 매크로 , 마이크로 차이점

```html
<!DOCTYPE html>
<script>
  "use strict";

  setTimeout(() => alert("timeout"));

  Promise.resolve().then(() => alert("promise"));
  Promise.resolve().then(() => alert("promise2"));

  alert("code");
</script>
```

```
// 결과
code
promise
promise2
timeout
```

`task Queue` 가 하나가 아니라는것에 초점을 맞춰 줬으면 함

그리고 콜스택이 비어야지 task 큐에 있는 콜백 함수를 콜스택으로 옮겨와서 실행시킬 수 있는 거임

1.  script main 콜스택 담김
2.  첫번째 setTimeOut 콜스택 담김 & 실행
3.  macro TaskQueue 에 0초후에 알려달라고 이벤트 검
4.  Promise구문 콜스택에서 실행
    (이때도 스크립트 main 콜스택이 있어서 스크립트 코드를 다 읽을때까지 다른 큐에 담인 콜백을 실행 못함!!!!) ,
    then 에 담긴 콜백 함수를 micro TaskQueue에 담음
5.  4와 같이 두번째 프라미즈도 담음
6.  alert을 만나 동기적으로 실행
7.  `script`가 끝남 즉 main 이 끝나서 콜스택이 빔
8.  micro 큐에 담긴 then 콜백이 먼저실행 (위에 사진참조)
9.  다음 루프로 안넘기고 마이크로 큐가 빌때까지 실행함 ( 중요!! ) 그래서 promise2가 실행됨
10. timeout 출력됨 ( 매크로 큐 ) 에서 꺼내와서 실행

### 참고자료

https://ko.javascript.info/event-loop
https://www.youtube.com/watch?v=8aGhZQkoFbQ
https://ko.javascript.info/event-loop
