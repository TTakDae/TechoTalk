# 자바스크립트

## 불변성

### 자바스크립트의 데이터 타입

- immutable 불변
  원시타입 undefined ,Null , Boolean , String, Number , Symbol

undefined 형은 변수가 선언된뒤에 값이 할당 되기 전까지 미리 채워놓는 값임.!

- mutable 가변 Object 형

둘이 결국 변수에는 참조 값이 담기게 됨.
`var A;` 라고 선언하게되면 undefiend 라는 값을 가리키는 주소를 A가 가지고 있음
이후 객체가 오건 원시타입이 오건 그 값을 가리키는 주소가 저장되는거임
![object 사진](https://user-images.githubusercontent.com/13329304/177193263-c4606f16-4f7f-47d2-802f-aa09e6866308.jpg)

```javascript
A = "string1";
// "string1"을 저장하는 공간 메모리에 있고 그공간을 가리키는 참조 주소를 A에 저장
A = {
  field : "string2";
};
// field 는 원시타입 string2를 가리키는 주소를 가지고 있으며
// 위 객체는 field 에대한 주소를 프로퍼티로 가진다.
// 위 객체를 가리키는 주소를 A 가 가진다.
```

객체가 오냐 원시값이 오냐 가르키는 주소를 저장하는 건 같지만
객체가 오면 객체 주소 저장하고 그 주소 정보에는 객체가 프로퍼티를 가르키는 정보들을 저장함. 이래서 깊은 복사 얕은 복사 가 생겨남.

### 변수와 값은 별개다.

![object 가변](https://user-images.githubusercontent.com/13329304/177193253-af922579-1168-41f8-9422-6971cb5a6b38.jpg)

- 할당된 primitive type 이 메모리에 저장되면,
  저장된 값을 바꿀 수 없는 것을 불변하다고 한다.

- Object 변수가 들고 있는 값을 바꾸지 않으면서,
  내부 프로퍼티에 재할당할 수 있는 것을 가변하다고 한다.

### const 선언과 값의 불변은 다르다.

- const는 변수 메모리 공간의 값을 읽기 전용으로 선언하는 것.
  즉 주소가 불변한다는거지 값이 불변하다는건 아님!!!

### 객체의 원본을 지키기 위해서는 깊은 복사가 필요하다.

- 프로퍼티 영역을 가리키는 변수의 값만 복사하면, 원본가 사본이 같은 영역을 공유하는 상황이 생긴다.
- 함수형 프로그래밍에서 순수 함수 -> 주변의 환경을 건들이면 안됨.

![얕은복사](https://user-images.githubusercontent.com/13329304/177193399-afb6e08d-56f5-43ee-adf5-865832df6dc9.jpg)

```javascript
var copyObject = function (target) {
  var result = {};
  if (typeof target === "object" && target !== null) {
    // 객체면 , 재귀 호출로 깊은 복사
    for (var prop in target) {
      result[prop] = copyObject(target[prop]);
    }
  } else {
    // 객체가 아니면 , 바로 복사
    result = target;
  }
  return result;
};
```

### 리액트의 불변성은?

리액트가 데이터가 바뀌는 걸 보고 재랜더링 하는 기준은 참조값이 변하면임
즉 배열의 원형을 변경하는건 감지를 못함 왜냐? 참조값이 변하지 않았기 때문
불변성을 지키라는건 참조값이 변하게 끔 map reduce 같은 걸 이용해서 새로운 참조값으로 return 해서 사용 하라는 뜻

### 참고자료

https://hsp0418.tistory.com/171
