# Promise에 대하여

## 1. Promise의 개념

Promise는 비동기 연산의 최종 결과를 나타내는 **객체**로서, 성공 또는 실패 시점에 실행할 콜백 함수들을 연결할 수 있게 한다.

## 2. Promise의 상태 = [[PromiseState]]

- pending: 초기 상태, 이행되거나 거부되지 않은 상태.
- fulfilled: 연산이 성공적으로 완료된 상태.
- rejected: 연산이 실패한 상태.

즉, pending -> fulfilled or rejected

## 3. Promise 생성

#### 기본적인 구조

- new Promise() 구문을 사용해 새로운 Promise 객체를 생성 (Promise는 클래스다)
- new Promise() 괄호 안에 들어가는 함수는 'executor 함수'라고 부르며, 이 함수는 즉시 실행됨
- resolve, reject라는 두 가지의 **콜백 함수**를 받음
- 비동기 작업의 결과에 따라 개발자는 resolve를 호출하여 Promise를 fulfilled 상태로 만들거나, reject를 호출해 Promise를 rejected 상태로 만들 수 있음. 이는 비동기 작업이 완료된 직후 이루어짐.

```javascript
const promise = new Promise((resolve, reject) => {
    //비동기 작업 수행

    if(/* 비동기 작업 성공 */){
        resolve('결과 값')
    } else {
        reject('오류 메시지')
    }
})
```

## 4. Promise의 상태 전이

#### 이행(fulfilled)

- resolve 함수가 호출되면, Promise의 상태는 fulfilled로 변경되고, 'then' 메서드 내부의 콜백함수가 실행됨
- resolve 함수에 전달된 값은 'then' 콜백함수의 인자로 사용됨

```javascript
promise.then((value) => {
  // value는 resolve에서 전달된 값
});
```

#### 거부(rejected)

- reject 함수가 호출되면, Promise의 상태는 rejected로 변경되고, 'catch' 메서드 내부의 콜백함수가 실행됨
- reject 함수에 전달된 오류 메시지 또는 객체는 'catch' 콜백함수의 인자로 사용

```javascript
promise.catch((error) => {
  // error는 reject에서 전달된 오류 메시지
});
```

## 5. Promise 객체의 메서드

1. then(): Promise의 [[PromiseState]]가 fulfilled 상태가 되면 실행할 콜백 함수를 작성. 에러가 발생하지 않으면 이 콜백은 [[PromiseResult]]에 담긴 값을 인자로 받음  
   ([PromiseResult] : Promise가 처리된 이후의 결과 값)

2. catch(): Promise가 rejected 상태가 되면 실행할 콜백 함수를 작성. 무언가 잘못되어 Promise가 거부되면, 이 콜백은 거부의 이유(에러)를 인자로 받음

3. finally(): Promise가 이행되거나 거부될 때 실행할 콜백 함수를 등록. 이 메서드는 최종 결과에 상관없이 어떤 작업을 "청소"하고자 할 때 유용

#### 예제 :

```javascript
/* 1. Promise 생성하기 */
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    // resolve("hello!");
    reject(new Error("no Network"));
  }, 1000);
});

/* 2. Promise 사용하기 */
promise.then((value) => {
  console.log(value);
});

// 위 식은 아래와 같음 - 인자와 파라미터 같으면 생략 가능
promise
  .then(console.log)
  .catch((error) => console.log(error))
  .finally(() => console.log("finished!"));
```

## 6. Promise chaining

#### then 메서드에는 값과 프로미스 둘 다 전달 가능하다

```javascript
const fetchNumber = new Promise((resolve) => {
  setTimeout(() => {
    resolve(1);
  }, 1000);
});

fetchNumber
  .then((num) => num * 2)
  .then((num) => num * 3)
  .then((num) => {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve(num - 1);
      }, 1000);
    });
  })
  .then((num) => console.log(num));
```

1. 값을 반환하는 경우:

- .then((num) => num _ 2) 또는 .then((num) => num _ 3) 같은 경우, 각 함수는 숫자를 인자로 받아들이고 계산된 값을 바로 반환하고 있다. 여기서 반환되는 것은 Promise 객체가 아니라 일반 값(여기서는 숫자)이다.
- 하지만, then 메서드는 항상 자동으로 반환된 값을 감싸는 Promise 객체를 생성하기 때문에, 체이닝이 계속될 수 있다. 따라서, 이러한 동기적 연산 결과도 다음 then으로 전달할 수 있다.

2. Promise를 반환하는 경우:

- .then((num) => { return new Promise(...) })는 명시적으로 새로운 Promise 객체를 반환한다. 이 Promise 객체는 일정 시간 후에 비동기적으로 처리된다 (setTimeout을 사용한 비동기 연산)
- 이 경우, 현재 Promise의 해결은 반환된 새로운 Promise가 해결될 때까지 지연된다. 새로운 Promise가 해결되면 그 결과가 다음 then 호출의 입력으로 사용된다.

이러한 방식으로 then은 동기적 연산과 비동기적 연산 모두를 처리할 수 있으며, 이 두 경우 모두 다음 then 메서드로 결과를 넘길 수 있어 체이닝이 가능하다.

## 7. Error Handling

닭 > 달걀 > 요리 순으로 비동기 작업이 이루어지는 코드

```javascript
const getHen = () =>
  new Promise((resolve, reject) => {
    setTimeout(() => resolve("🐓"), 1000);
  });

const getEgg = (hen) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(`${hen} => 🥚`), 1000);
  });
};

const cook = (egg) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(`${egg} => 🍳`), 1000);
  });
};

getHen()
  .then(getEgg) /* = (hen) => getEgg(hen) */
  .then(cook) /* = (egg) => cook(egg) */
  .then(console.log) /* = (meal) => console.log(meal) */;
```

만약, getEgg에서 egg를 잘 받아오지 못했다면?

```javascript
const getEgg = (hen) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => reject(new Error("no eggs!")), 1000);
  });
};

getHen() // Error: no eggs!
  .then(getEgg)
  .then(cook)
  .then(console.log)
  .catch(console.log);
```

중간에서 catch로 에러헨들링 할 수 있음

```javascript
const getHen = () =>
  new Promise((resolve, reject) => {
    setTimeout(() => resolve("🐓"), 1000);
  });

const getEgg = (hen) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => reject(new Error("no eggs!")), 1000);
  });
};

const cook = (egg) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(`${egg} => 🍳`), 1000);
  });
};

getHen()
  .then(getEgg)
  .catch((error) => {
    return "🌶️";
  })
  .then(cook)
  .then(console.log)
  .catch(console.log); // 🌶️ => 🍳
```

## 8. Promise.all()

`Promise.all`은 여러 개의 프로미스를 동시에 실행하며, 모든 프로미스가 완료될 때까지 기다린다. 이 메서드는 주로 여러 비동기 작업을 병렬로 처리할 때 사용된다.

간단한 예로, 여러 웹 사이트의 정보를 동시에 가져와야 하는 상황을 생각해볼 수 있다. 각 웹 사이트에서 정보를 가져오는 함수가 있을 때, `Promise.all`을 사용하여 모든 웹 사이트의 데이터를 한 번에 가져올 수 있다.

다음은 그 예시다:

```javascript
function fetchData(url) {
  // 이 함수는 각 URL로부터 데이터를 비동기적으로 가져온다.
  // 실제 상황에서는 fetch, axios, XMLHttpRequest 등을 사용할 수 있다.
  // 여기서는 setTimeout을 사용하여 비동기 작업을 시뮬레이션한다.
  return new Promise((resolve) => {
    console.log(`Fetching data from ${url}...`);
    setTimeout(() => {
      // 가상의 응답 데이터
      const responseData = `Response from ${url}`;
      console.log(`Data from ${url} has been received.`);
      resolve(responseData);
    }, 2000); // 각 요청을 처리하는 데 2초가 걸린다고 가정한다.
  });
}

// 여러 웹사이트의 URL들
const urls = [
  "https://api.site1.com",
  "https://api.site2.com",
  "https://api.site3.com",
];

// Promise.all을 사용하여 여러 웹 사이트의 데이터를 한 번에 가져온다.
Promise.all(urls.map((url) => fetchData(url)))
  .then((responses) => {
    // 여기서 responses 배열에는 각 URL로부터 받은 데이터의 배열이 들어 있다.
    console.log("All data has been fetched:", responses);
  })
  .catch((error) => {
    // 만약 하나라도 실패한다면, 여기서 에러를 캐치한다.
    console.error("An error occurred:", error);
  });
```

이 코드는 각 URL로부터 데이터를 비동기적으로 가져오며, 모든 요청이 완료되면 결과를 배열로 반환한다. 만약 하나 이상의 프로미스가 거부되면(`reject`), `Promise.all`은 즉시 에러와 함께 `catch` 블록으로 진입한다.

이러한 방식은 여러 비동기 작업을 병렬로 처리하고, 모든 작업이 성공적으로 완료됐을 때만 다음 단계를 진행하고자 할 때 유용하다.
