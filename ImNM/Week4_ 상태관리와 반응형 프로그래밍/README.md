# 자바스크립트

## 상태관리와 반응형 프로그래밍

### 상태

- 변화하는데이터
  말그대로 변화하는 데이터...

### 상태 관리

- 변화하는 데이터를 관리 하는 방법

  사용자들은 데이터가 바뀌어도 페이지가 바뀌지 않은채 데이터만 바뀌는 걸 원함

- 반응형 프로그래밍이란?
  데이터가 변화하면 그에 따라 화면이 바뀌어야하는데 자바스크립트는 언어자체가 반응형 프로그래밍 언어가 아님
  값이 변화면 알아서 반응을 하는 프로그래밍을 반응형 프로그래밍이라고 함.

```javascript
  let price = 1000;
  let quantity = 2;
  let total = price * quantity

  price = 2000;
  console.log(`총 가격은 ${total} 원`)

// 반응형이 아닐경우
 총 가격은 2000원
// 반응형일경우
 총 가격은 4000원
```

- 반응형 프로그래밍의 정의
  데이터 스트림과 변화의 전파에 관련된 선언형 프로그래밍 패러다임 이다.
- 데이터 스트림
  시간 순으로 발생하는 이벤트의 나열.
  반응형 프로그래밍 에서는 모든 데이터를 스트림으로 본다.
- 선언형 프로그래밍 패러타임
  어떻게 할지 가아닌 무엇을 할지 .

즉 반응형 프로그래밍은 데이터가 변경이 될때마다 이벤트를 발생시켜
바뀐 데이터를 지속적으로 전달하는 목표를 가진 프로그래밍

어떻게 지속적으로 전달하는가?

### pull & push

제어권을 가진자 입장에서의 pull & push

데이터 생산자
데이터 소비자

- pull
  데이터 소비자가 데이터 생산자한테 땡겨옴

- push
  데이터 생산자가 소비자한테 바뀌었을때 알려줌

- react is not reactive ?
  https://www.swyx.io/reactrally/

리액트는 컴포넌트 렌더링(뷰)의 관점에서 보면 새로운데이터(상태변화)에 반응해서 렌더링이 일어난다.
Reactive Programming 은 비동기 이벤트 스트림에 대해 반응하여 연산을 수행하는 것이다.
하지만 리액트는 새로운데이터에 대해서 컴포넌트 렌더링 시점까지 연산을 지연, 즉 컴포넌트를 렌더링 하는 시점을 기준으로 동기화 되어 작동하기 때문에 "fully reactive" 하지 않다.

### 반응형 프로그래밍의 상태관리 패턴

![옵저버](https://user-images.githubusercontent.com/13329304/178517960-ec56d61a-9a61-47fc-bb9b-58f735e8cbb4.jpg)

- 옵저버 패턴
  객체 상태의 변화를 관찰하는 옵저버 들이 있고
  객체 자체에서 옵저버들을 관리함.

사용처 브라우저 상의 이벤트 핸들러 event enventlistner

- pub sub 패턴
  중간에 매개체가 있음 (제 3자)
  ![펍섭](https://user-images.githubusercontent.com/13329304/178517942-3fdbce94-ecc3-4fd7-a8df-c14b43721c00.jpg)
- 프록시 패턴

https://junilhwang.github.io/TIL/Javascript/Design/Vanilla-JS-Store/#_7-redux-%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Proxy

간단하게 set 프록시 객체에 변경된 데이터가 있으면 렌더링 하게 해버릴 수 있음

```javascript
// 기본적인 defineproperty 메서드 사용
export const observable = (obj) => {
  Object.keys(obj).forEach((key) => {
    let _value = obj[key];
    const observers = new Set();

    Object.defineProperty(obj, key, {
      get() {
        if (currentObserver) observers.add(currentObserver);
        return _value;
      },

      set(value) {
        if (_value === value) return;
        if (JSON.stringify(_value) === JSON.stringify(value)) return;
        _value = value;
        observers.forEach((fn) => fn());
      },
    });
  });
  return obj;
};
```

```javascript
// proxy 객체 사용 obj 인자로 넘겨준거 잘보삼

export const observable = (obj) => {
  const observerMap = {};

  return new Proxy(obj, {
    get(target, name) {
      observerMap[name] = observerMap[name] || new Set();
      if (currentObserver) observerMap[name].add(currentObserver);
      return target[name];
    },
    set(target, name, value) {
      if (target[name] === value) return true;
      if (JSON.stringify(target[name]) === JSON.stringify(value)) return true;
      target[name] = value;
      observerMap[name].forEach((fn) => fn());
      return true;
    },
  });
};
```

리액트의 html 이벤트도 프록시로 감싸서 사용중임!
syntheticEvent 등등..
