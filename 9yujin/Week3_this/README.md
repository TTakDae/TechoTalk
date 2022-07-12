# this

객체는 상태를 나타내는 **property**와 동작을 나타내는 **method**로 이루어져있다.

메소드가 자신이 속한 객체의 property를 참조하려면

```javascript
const someone = {
	name: '9yu',
	getName() {
	return someone.name;
	};
};
```

이렇게 할 수 도 있겠지만, 좋은 방법은 아니다.

```javascript
const someone = {
	name: '9yu',
	getName() {
	return this.name;
	};
};
```

대신 이렇게 this를 사용해서 자신이 속한 객체를 참조할 수 있다.



## 1. this 바인딩

```
function printThis() {
	console.log(this);
}
printThis();
```

지난주 클로저에서 실행컨텍스트에 대해서 한번 봤었다. 함수를 호출할때 실행컨텍스트가 생성되는데, this에 대한 정보가 이때 실행컨텍스트에 들어간다. **즉, this는 함수를 호출할때 결정된다.**

this와 this가 가리킬 객체를 연결하는 것을 this 바인딩이라고 부른다.

함수 호출방식에 따라 다르게 바인딩 됨.

- 일반 함수 호출 : 기본 바인딩
- 메서드 호출 : 암시적 바인딩
- 생성자 함수 호출 : new 바인딩
- Function.prototype.apply/call/bind 메서드에 의한 간접 호출 : 명시적 바인딩



#### 기본 바인딩
```
function printThis() {
	console.log(this);
}
printThis();
```

- 일반함수 호출시 this에는 전역 객체가 바인딩 된다

  브라우저의 전역객체 : window
  node의 전역객체 : global

다만 엄격모드에서는 undefined가 바인딩된다. `'use strict';` 



```javascript
const obj = {
	foo() {
		function boo() {
			console.log(this);
		}
		boo();
	};
}

obj.foo();

//------------

const obj = {
	foo() {
	setTimeout(function () {
		console.log(this);
	}, 0);
	};
}

obj.foo();
```

중첩함수와 콜백함수로 일반함수를 선언했기 때문에 전역객체가 바인딩 된다.

만약에 중첩이나 콜백함수를 이용해서 this를 사용해서 자신이 속한 객체의 메서드에 접근하려고 한 상황에서는 의도와 다르게 바인딩이 된 것이라고 할 수 있다.



- 권장하지 않는 방법 : this로 사용하고자 하는 객체를 변수에 할당하여 사용

```javascript
const me = {
	name : '9yu',
	printName() {
		const self = this;
		setTimout(function () {
			console.log(self.name);
		},0);
	};
};
```

self라는 이름으로 변수를 만들어서 사용했다. self에는 me가 들어간다.



#### 암시적 바인딩

```javascript
const outer = {
	inner: {
		printThis() {
			console.log(this);
		};
	};
};

outer.inner.printThis();
```

함수를 객체의 메서드로 호출할 경우, this는 메서드를 호출한 객체, 즉 점(.) 바로 앞에 적힌 객체로 바인딩 된다. 즉, this는 inner를 가리킨다.



#### new 바인딩

```javascript
function Someone(name) {
	this.name = name;
};

const me = new Someone('9yu');
```

생성자 함수로 호출할 경우, 함수를 사용하여 생성할 인스턴스(me)가 바인딩된다



#### 명시적 바인딩

apply, call, bind에 의한 간접 호출한 경우이다.

function.prototype의 메서드로서, 모든 함수가 상속받아 사용할 수 있다.

```javascript
Function.prototype.apply(thisArg, argsArray)
Function.prototype.call(thisArg, arg1, arg2, ...)
```

`apply`와 `call`은 호출할 함수의 인수를 전달하는 방식만 다를 뿐 this로 사용할 객체를 전달하면서 함수를 호출해준다.



`Function.prototype.bind(thisArg, arg1, arg2, ...)`

`bind`도 위와 똑같이 this로 사용할 객체를 전달한다. 하지만 함수를 호출해주지 않고, thisArg가 바인딩된 함수가 반환된다. 직접 호출해주는 코드가 필요하다.



#### this 바인딩 규칙 우선순위

여러 바인딩 규칙이 충돌할경우

- new > 명시적 > 암시적 > 기본



## 2. 화살표 함수에서의 this

```javascript
const arrowFn = () => {
	console.log('나는야 화살표 함수');
};
```

일반함수 내부에서 this가 전역객체를 바라보는 현상을 해결하기 위해 ES6에서 등장했다. 화살표함수는 실행컨텍스트가 만들어질 때 this를 바인딩하지 않는다. 즉, 화살표 함수 내부의 this는 존재하지 않는다.

- this 호출시, 상위 스코프의 this에 접근한다.
- 이를 Lexical this라고 부른다.
- 명시적 바인딩을 해주더라도 화살표 함수 내부에 사용하는 this는 교체될 수 없다.



```javascript
const someone = {
	name: '9yu'.
	printName: () => {
		console.log(this.name);
	},
};

someone.printName();
```

브라우저에선 빈 문자열이, 노드에선 undefined가 출력된다. 브라우저에서 윈도우 객체의 name값이 기본적으로 빈 문자열로 설정되어 있다. 즉, this가 someone이 아니라 전역객체에 바인딩 되어있다.

- 스코프를 타고 타고 올라가서, printName -> someone, someone -> 전역객체
- someone을 가리키고 싶다면 화살표함수를 사용하지 않는다.



## 3. 클래스에서의 this

```javascript
class SomeClass {
	//'strict mode;'
}
```

클래스 내의 모든 코드에는 암묵적으로 strict mode가 적용된다.

일반적으로 내부에서 this 호출 시, 클래스가 생성한 인스턴스 객체가 바인딩된다. 만약 메서드 내부에 중첩이나 콜백 함수를 일반 함수로 선언했을때, 엄격모드니까 undefined가 바인딩된다.



---



#### 정리

**(우선순위) this 바인딩 규칙**

- (4) 일반함수호출(기본바인딩) : 전역 객체 or undefined
- (3) 메서드 호출 (암시적 바인딩) : 호출 객체 
- (1) 생성자함수 호출 (new 바인딩) : 인스턴스 객체
- (2) Apply, call, bind에 의한 간접 호출 (명시적 바인딩) : 첫번째 인자 객체

**화살표 함수**

- this를 바인딩 하지 않음
- 상위 스코프의 this에 접근

**Class**

- 내부 : 엄격 모드
- 일반적으로 this => 인스턴스 객체
- 일반함수 선언, 호출시 -> undefined
