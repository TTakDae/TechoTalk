# Promise

### 1. 동기 처리와 비동기 처리

* 동기 처리 (Synchronous)
  * 각 작업들 (task)를 순차적으로 처리
  * Ex. 클라가 서버에 요청을 보냈을 때 *** 해당 요청에 대한 응답을 받아야만 다음 작업 수행 가능 ***
    * A작업이 실행되면 B작업은 대기
* 비동기 처리 (Asynchronous)
  * 한 작업이 끝나기까지 기다리지 않고 바로 다음 작업들을 실행  
  * Ex. 클라가 서버에 요청을 보냈을 때 *** 해당 요청에 대한 응답 여부에 상관없이 다음 작업 수행 가능 ***
    * A작업 실행 동시에 B작업도 동시에 실행

```javascript
console.log("A");
setTimeout(() => {
  console.log("B");
}, 0);
console.log("C");
```

> javascript에서 사용하는 setTimeout을 이용한 코드이다. 결과 값이 A => B => C 일 것 같지만 setTimeout 메서드는 비동기적으로 실행되기 때문에 setTimeout이 실행되는 동시에 C가 콘솔에 찍히게 된다.

```javascript
function sleep(callback) {
  setTimeout(function() {
    callback();
  }, 1000);
}
sleep(function() {
  console.log("A");
  sleep(function() {
    console.log("B");
    sleep(function() {
      console.log("C");
    });
  });
});
```

> 위의 코드와 달리 결과값을 A => B => C로 만들기 위해 콜백 함수를 여러 번 중첩하여 실행 순서를 제어한 코드이다. 



위의 코드처럼 콜백 함수를 여러 번 중첩할 시 *** 콜백 지옥 *** 에 빠지게 된다 => 이를 해결하기 위해 *** Promise ***를 사용한다.



----



## 2. Promise

> 비동기 처리를 실행하고 *** 그 처리가 끝난 후 *** 다음 처리를 실행하기 위해 사용

#### Promise의 실행

```javascript
var promise = new Promise((resolve, reject) => {
  ...
})
```

* Promise에는 실행하고자 하는 함수를 인수로 넘김 => 이 함수는 *** reslove ***, *** reject ***의 인수를 갖는다.
* resolve: 함수 내부에서 실행하는 로직의 처리가 끝났을 때, 호출해야 하는 콜백 함수. 어떠한 값도 인수로 넘길 수 있음
* reject: 함수 내부에서 실행하는 로직의 처리가 실패했을 때 호출해야 하는 콜백 함수. 어떠한 값도 인수로 넘길 수 있음



#### Promise의 종료

```javascript
promise.then(onFullfilled);
promise.catch(onRejected);
```

* Promise가 reslove 함수를 통해 종료되었을 때, reslove 함수에 인수로 넘긴 값을 then 메서드에 인수로 넘긴 함수에 전달되어 처리를 위해 사용
* then의 인수 함수는 *** 성공 콜백 함수 ***라고 한다.
* Promise가 reject의 함수를 통해 종료되었을 때, then을 건너 뛰고 reject 함수에 인수로 넘긴 값을 catch문에 넘긴 함수에 전달되어 처리를 위해 사용
* catch의 인수 함수는 *** 실패 콜백 함수 *** 라고 한다.

```javascript
promise.then(onFullfilled, onRejected);
```

* then 메서드에 성공 콜백 함수, 실패 콜백 함수 모두 인수로 넘겨 줄 수 있다.



### 3. 비동기 처리 여러 개를 병렬로 실행하기

-----

#### Promise.all

```javascript
const [totalCount, ticketList] = await Promise.all([
          Ticket.countDocuments({
            accountName: search
          }),
          Ticket.find({
            accountName: search
          })
            .limit(limit)
            .skip(offset)
            .sort({ ticketNumber: -1 })
            .populate({
              path: 'manager',
              model: 'admin',
              select: { _id: 1, name: 1 }
            })
        ]);
```

* Promise.all는 배열 내부에 있는 promise 객체들을 병렬로 실행
  * 배열 내부의 모든 객체들이 reslove 함수를 호출 하면 .then 메서드를 실행 (response라는 배열을 인수로 받음)
  * 배열 내부의 객체 중 한개라도 reject 함수를 호출 하면 가장 먼저 호출된 reject 함수의 인수를 통해 .catch 메서드를 실행

#### Promise.race

* Promise.race는 배열 내부에 있는 promise 객체들 중 가장 일찍 종료한 작업의 결과를 바탕으로 .then/.catch를 호출한다. (나머지 객체들도 실행 되기는 함)
