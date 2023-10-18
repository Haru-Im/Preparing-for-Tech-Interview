# JavaScript에서 '비동기 처리'가 필요한 이유

자바스크립트는 **싱글 스레드** 기반 언어로, 한 번에 하나의 작업만을 처리할 수 있다. 이 말은, 만약 코드에 시간이 오래 걸리는 작업(예: 서버에서 데이터를 가져오는 작업)이 있다면, 그 작업이 완료될 때까지 기다리는 동안 전체 시스템이 멈추거나 대기 상태에 놓이게 된다는 것이다.

예를 들어, 웹 애플리케이션에서 서버로부터 큰 데이터를 불러와야 하는 상황을 가정해보자. 이 데이터를 불러오는 데 10초가 걸린다면, 동기적 처리 방식을 사용하면 데이터 요청 동안 사용자는 아무런 반응이 없는 화면을 보게 될 것이다. 이는 사용자 경험 측면에서 매우 좋지 않다.

비동기 처리를 사용하면, 서버로부터 데이터를 요청한 후, 데이터가 도착할 때까지 기다리는 대신, 다른 작업(예: UI 업데이트, 다른 이벤트 처리 등)을 계속할 수 있다. 데이터가 도착하면 그때 해당 데이터를 활용한 처리를 계속하게 된다. 이 방식은 프로그램이 멈추지 않고 계속 실행될 수 있도록 해서, 사용자에게 더 나은 경험을 제공하고, 자원을 효율적으로 사용할 수 있게 한다.
<br/>
<br/>
<br/>

# Promise에 대하여

<br/>

## 1. Promise의 개념

Promise는 비동기 연산의 최종 결과를 나타내는 **객체**로서, 성공 또는 실패 시점에 실행할 콜백 함수들을 연결할 수 있게 한다.

<br/>

## 2. Promise의 상태 = [[PromiseState]]

- pending: 초기 상태, 이행되거나 거부되지 않은 상태.
- fulfilled: 연산이 성공적으로 완료된 상태.
- rejected: 연산이 실패한 상태.

즉, pending -> fulfilled or rejected

<br/>

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

<br/>

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

<br/>

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

<br/>

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

<br/>

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

<br/>

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

<br/>

# async, await에 대하여

`async`와 `await`은 자바스크립트의 비동기 처리 패턴 중 하나이며, 기존의 비동기 처리 방식인 콜백 함수와 프로미스의 단점을 보완하는 방식이다. 이 구문은 프로미스를 더 편리하게 사용할 수 있도록 도와주는 syntactic sugar이다.

<p style='font-size: small'>(syntactic sugar : 프로그래밍 언어에서 문법적 요소가 기능적으로 새로운 것을 추가하지 않으면서 코드 작성을 더 쉽고 가독성 있게 만들어 주는 코드 작성 스타일. 즉, 기존에 있던 기능을 보다 쉽게 사용할 수 있도록 제공되는 문법.)</p>

<br/>

## 1. async/await의 등장 배경:

프로미스가 콜백 지옥 문제를 어느 정도 해결했지만, 여전히 코드가 장황해지고, 복잡한 비동기 로직에서는 프로미스 체인이 길어지면 이해하거나 디버그하기 어려운 문제가 있었다. 특히 프로미스의 에러 처리가 명확하지 않아서 개발자들이 에러를 잡기 어려워하는 경우가 많았다.

이러한 문제를 해결하기 위해 ES2017(ES8)에서 `async/await`가 도입되었다. 이는 비동기 코드를 마치 동기 코드처럼 보이게 하면서도 비동기 처리의 이점을 유지한다. 코드의 가독성이 높아지고, 비동기 처리 과정을 좀 더 명확하게 표현할 수 있게 해준다.

<br/>

## 2. async/await 정의:

1. async 함수는 항상 프로미스를 반환한다. 일반 값을 반환하더라도 이 값은 프로미스로 감싸진다.
2. await 키워드는 오직 async 함수 안에서만 동작한다. await는 프로미스가 처리될 때까지 함수 실행을 일시 중단시키고, 처리가 완료되면 나머지 코드를 진행한다.

이 구문을 사용하면 프로미스의 결과 값을 더 직관적이고 동기적인 방식으로 처리할 수 있다.

<br/>

## 3. async 사용 예시

만약, 10초 정도 소요되는 데이터 페치 작업이 있다고 가정해보자.

```javascript
const fetchData = () => {
  // do network request in 10 secs...
  return data;
};

const data = fetchData();
console.log(data);

// 다른 코드들
```

자바스크립트는 싱글 스레드 기반 언어로, 한 줄에 하나의 코드만 처리하기 때문에 사용자는 10초 동안 데이터 페치가 완료될 때까지 그 아래의 코드를 기다려야 한다. 이는 UX를 저하시킨다.

이를 Promise로 바꾸면,

```javascript
const fetchData = () => {
  return new Promise((resolve, reject) => {
    //do network request in 10 secs...
    resolve(data);
  });
};

const data = fetchData();
data.then(console.log);
```

참고로 `resolve(data)`와 같이 resolve를 부르지 않으면 Promise는 계속 pending상태가 된다. 어쨌든, 위 식을 `async`를 활용해 아래와 같이 더 간편하게 바꿀 수 있다.

```javascript
const fetchData = async () => {
  // do network request in 10 secs...
  return data;
};

const data = fetchData();
data.then(console.log);
```

코드블럭을 async로 감싸면 Promise를 쓰지 않아도 **항상 Promise를 반환**한다.  
즉, 비동기적 처리를 가능하게 한다.

<br/>

## 4. await 사용 예시

- await은 async 안에서만 사용할 수 있다.
- await이 붙으면 Promise의 완료(해결 및 거부)를 기다리며, 그 동안 함수 실행이 일시 정지된다.
- Promise가 해결되면, 해당 Promise의 결과값이 await 표현식의 값이 된다.

```javascript
async function fetchData() {
  const response = await someAsyncFunction(); // 여기서 일시 중지, Promise가 해결될 때까지 기다림
  return response; // someAsyncFunction에서 반환된 해결된 값
}
```

중요한 점은, await을 사용할 떄 JavaScript 실행이 멈추는 것이 아닌 async 함수 내에서만 일시 정지된다는 것이다. 이 외의 JavaScript 코드는 계속 정상적으로 실행된다.  
따라서, async/await은 비동기 코드를 마치 동기 코드처럼 보이게 만들어 주지만, 실제로는 비동기적 특성을 그대로 유지하며 동작한다.

다른 예시

```javascript
const delay = (ms) => {
  return new Promise((resolve) => setTimeout(resolve, ms));
};

const getApple = async () => {
  await delay(2000);
  return "🍎";
};

const getBanana = async () => {
  await delay(1000);
  return "🍌";
};

const pickFruits = () => {
  return getApple().then((apple) => {
    return getBanana().then((banana) => {
      return `${apple} + ${banana}`;
    });
  });
};

pickFruits().then(console.log);
```

ms를 매개변수로 받아 ms뒤에 이행된 Promise를 반환하는 delay 함수  
각각 2초 뒤, 1초 뒤에 Apple과 Banana를 반환하는 getApple, getBanana 함수가 있다.
pickFruits 함수에서는 Promise인 getApple 함수의 결과값을 2초간 기다리고, Promise인 getBanana 함수의 결과값을 1초간 기다려 총 3초 뒤 🍎 + 🍌를 콘솔에 출력한다.

하지만 이러한 체이닝은 콜백 지옥과 유사하게 너무 중첩하게 되면 지저분하고 가독성도 떨어진다.
따라서 위 pickFruits를 async/await 키워드를 사용하 다음과 같이 바꿀 수 있다.

```javascript
const pickFruits = async () => {
  const apple = await getApple();
  const banana = await getBanana();

  return `${apple} + ${banana}`;
};

pickFruits.then(console.log);
```

pickFruits 함수에서는 async/await 키워드를 사용해 2초, 1초간 걸리는 Promise의 반환을 기다리고(await), 3초 뒤 🍎 + 🍌 를 콘솔에 출력한다.

만약 getApple에 에러가 발생한 경우를 다음과 같이 만들 수도 있다.

```javascript
const delay = (ms) => {
  return new Promise((resolve) => setTimeout(resolve, ms));
};

const getApple = async () => {
  await delay(2000);
  throw "Error in getApple function!";
  return "🍎";
};

const getBanana = async () => {
  await delay(1000);
  return "🍌";
};

const pickFruits = async () => {
  try {
    const apple = await getApple();
    const banana = await getBanana();

    return `${apple} + ${banana}`;
  } catch (error) {
    console.log(error);
  }
};

pickFruits().then(console.log); // Error in getApple function!
```

하지만 pickFruits 함수는 비효율적이다. 사과와 바나나는 서로 의존적이지 않으므로, 사과를 가져온 후 바나나를 기다릴 필요가 없다. 이 과정을 병렬로 처리해야 시간을 절약할 수 있다.

<br/>

#### 해결 방법 1. Promise를 만들어 병렬적으로 실행

```javascript
const pickFruits = async () => {
  const applePromise = getApple();
  const bananaPromise = getBanana();
  const apple = await applePromise;
  const banana = await bananaPromise;

  return `${apple} + ${banana}`;
};

pickFruits().then(console.log);
```

이렇게 하면 getApple과 getBanana 함수 호출이 거의 동시에 시작되며, 두 작업이 병렬로 수행된다.
(applePromise, bananaPromise를 통해 apple과 banana의 Promise를 만들었고, Promise의 executor 함수는 즉시 실행된다.)

따라서, apple과 banana를 **병렬적으로** 처리할 수 있게 되었다.
하지만, 이렇게 서로 연관이 없고 병렬적으로 처리되는 것들은 이렇게 코드를 작성하지는 않는다.

<br/>

#### 해결 방법 2. Promise.all

```javascript
const pickAllFruits = async () => {
  try {
    const fruits = await Promise.all([getApple(), getBanana()]);
    return fruits.join(" + ");
  } catch (error) {
    console.log("Error:", error);
  }
};

pickAllFruits().then(console.log); // 🍎 + 🍌
```

Promise.all은 여러 개의 프로미스를 병렬로 처리할 때 사용하는데, 주어진 모든 프로미스가 성공적으로 이행될 때만 작동한다. 하나라도 실패하면 전체가 실패한 것으로 간주된다.

<br/>

#### 추가. Promise.race

Promise.race 메서드는 여러 프로미스 중 하나가 가장 먼저 이행되거나 거부될 때까지 기다린 후, 그 결과를 반환한다. 다른 프로미스의 결과는 무시된다.

```javascript
const pickOnlyOne = () => {
  return Promise.race([getApple(), getBanana()]);
};

pickOnlyOne().then(console.log); // 🍌
```
