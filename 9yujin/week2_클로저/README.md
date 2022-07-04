# 클로저

- **정의 :** 함수와 함수가 선언된 어휘적 환경의 조합
- 어떤 함수 A에서 선언한 변수 a를 참조하는 내부 함수 B를 외부로 전달할 경우,
  A의 **실행 컨텍스트**가 종료된 이후에도 변수 a가 사라지지 않는 현상



## 0. 실행 컨텍스트

- **정의 :** 실행할 코드에 제공할 환경 정보들을 모아놓은 객체
- 프언에서 나온 활성 레코드와 비슷한 개념..?



```javascript
// ------------------- (1)
var a = 1;
function outer() {
  function inner() {
    console.log(a);
    var a = 3;
    // --------------- (2)
  }
  inner(); // -------- (3)
  console.log(a);
  // ----------------- (4)
}
outer(); // ---------- (5)
console.log(a);
// ------------------- (6)
```

![다운로드](https://github.com/TTakDae/TechoTalk/blob/9yujin/9yujin/week2_%ED%81%B4%EB%A1%9C%EC%A0%80/%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C.png?raw=true)

콜 스택의 형태이다. 함수가 호출되면 자바스크립트 엔진은 해당 함수에 대한 환경 정보를 수집해서 실행 컨텍스트를 생성한 후 콜 스택에 담는다. 처음 자바스크립트 코드가 실행되는 순간에는 전역 컨텍스트가 콜 스텍에 담긴다.

실행 컨텍스트에 담기는 정보로는 아래 세가지가 있다.

#### VariableEnvironment

현재 컨텍스트 내의 식별자들에 대한 정보 + 외부 환경 정보.
선언 시점의 LexicalEnvironment의 스냅샷으로, 변경 사항은 반영되지 않음.

- environmentRecord (snapshot)
- outerEnvironmentReference (snapshot)

#### LexicalEnvironment

처음에는 VariableEnvironment와 같지만 변경 사항이 실시간으로 반영된다.

- **environmentRecord**
  - 현재 컨텍스트와 관련된 코드들의 식별자 정보들이 저장
  - 매개변수 이름, 함수 선언, 변수명 등을 순서대로 수집
  - **호이스팅 :** 변수와 함수 선언부 등을 위로 먼저 끌어올린다.
- **outerEnvironmentReference**
  - 바로 직전 컨텍스트(해당 함수가 선언된 위치)의 LexicalEvironment 정보를 참조
  - **스코프 :** 쭉쭉 타고 올라가다가 전역 컨텍스트의 Lexical까지 가도 없으면 undefined

#### ThisBinding

- this 식별자가 바라봐야 할 대상 객체
- this가 지정되지 않은 경우 전역 객체 저장, 그 외에는 함수를 호출하는 방법에 따라 다름



## 1. 다시 클로저

```javascript
fuction doSomething() {
	const x = 10;
	function sum(y) {
		return x + y;
	}
	return sum;
}

const something = doSomething();
console.log(something(3));
```

- `sum()`에선, `doSomething()`에서 선언한 x를 참조한다.
- `doSomething()`에선 내부함수 sum을 외부로 전달한다.
- 콘솔에선 13이 찍힌다. 즉, x 변수가 사라지지 않는다.



#### 예제

```javascript
function sum(x) {
	return function(y) {
		return x + y;
	};
}

const add = sum(2);
console.log(add(7));
```

`sum`에서 익명함수를 리턴하고, 그걸 `add`에 할당해 출력한다.



1. **전역컨텍스트의 Lexical**
   Lexical.Record : const add, function sum
   Lexical.Outer : null
   
2. **sum 함수 호출** : 콜스택에 **push**
   Lexical.Record : x, function anonymous()
   Lexical.Outer : `전역 컨텍스트`(1)의 Lexical을 참조
   
   - 함수가 호출되어 평가되는 시점이 아닌 함수가 선언되는 시점에 결정된다. 
   - 코드 평가 후에 실행.  x ←2, Record 안에 기록
   - 코드의 마지막까지 실행 후, sum의 Lexical은 콜스택에서 **pop**
   - 다시 전역 컨텍스트의 Lexical로 돌아와 add변수에 sum의 리턴값인 익명함수 할당
   
3. **add 함수 호출 :** 콜스택에 **push**

   ```javascript
   const add = function(y) {
   	return x + y;
   };
   ```
   
   Lexical.Record : 매개변수 y를 호이스팅해서 기록
   Lexical.Outer : `sum`(2)의 Lexical을 참조
   
   - 여기서, 이미 위에서 sum의 Lexical이 콜스택에서 pop됐는데 어떻게 된걸까
   - sum()에서 익명함수가 선언될때, sum의 Record를 익명함수의 Outer에 등록을 한다.
   - 즉, sum()이 리턴한 익명함수의 Outer가 가리키는 Lexical은 익명함수가 선언될 때 결정된다.
   - 코드 평가 후에 실행. y←7, Record 안에 기록
   - 리턴문을 실행할 때, 함수 안에 없는 x 변수를 맞이하게 됨...!!!

#### 자바스크립트의 변수 탐색 과정

1. 먼저 자기 자신 (add)에서 x를 찾음.
2. 다음으로 `add`의 Lexical.Outer가 가리키고 있는 `sum`의 Lexical에서 찾는다.
3. sum의 Lexical.Record에서 x = 2 를 찾을 수 있다.
4. 콜스택에는 존재하지 않지만 Outer가 참조하는 곳에서 가지고 있어 변수가 사라지지 않는 현상을 **클로저 현상**이라고 말할 수 있다. 그리고 이러한 변수를 **자유 변수**라고 부른다.



다시 예제로 돌아와, `add`에선 9가 리턴이 되고 콜스택에서 pop된다. 다시 전역 컨텍스트의 코드가 실행되고 전역 컨텍스트의 Lexical이 콜 스택에서 pop되며 프로그램이 종료된다.
