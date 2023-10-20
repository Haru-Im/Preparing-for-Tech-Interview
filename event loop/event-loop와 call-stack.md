# JavaScript의 비동기 처리 (Event Loop와 Call Stack)

## 1. 자바스크립트와 싱글 스레드

자바스크립트는 싱글 스레드 기반 언어이다. 즉, 한 번에 하나의 작업만 처리할 수 있는 환경에서 동작한다. 이러한 구조는 동시에 발생하는 여러 작업을 처리할 때 제한점이 될 수 있는데, 특정 코드가 긴 작업을 수행하면 다음 코드 실행까지 전체 시스템이 멈추게 되어 비효율적이다.

### 1.1. 블로킹 vs 논블로킹

![블로킹,논블로킹](https://velog.velcdn.com/images/yangddu/post/d67d1f1d-743b-4f12-aaa4-4d860cffb395/image.png)
블로킹(Blocking)과 논블로킹(Non-Blocking)은 애플리케이션에서 작업 처리 방식을 설명할 때 사용하는 용어다. 블로킹 방식은 특정 작업이 완료될 때까지 기다렸다가 다음 작업을 수행하는 반면, 논블로킹 방식은 작업 완료 여부와 상관없이 다음 작업을 진행한다. 자바스크립트는 싱글 스레드이지만, 논블로킹 방식을 채택하여 여러 작업을 효율적으로 처리한다.

<br/>

## 2. 자바스크립트 엔진의 구조

자바스크립트 엔진은 주로 메모리 힙(Memory Heap)과 콜 스택(Call Stack)으로 구성된다.

![싱글스레드](https://miro.medium.com/v2/resize:fit:1024/1*RLbK8nM3pfLWPu4qIUaWww.png)

### 2.1. 메모리 힙

메모리 힙은 프로그램에서 사용되는 변수나 객체 등의 메모리 할당이 일어나는 공간이다. 이 공간은 동적으로 크기가 조절되며, 객체나 변수가 생성될 때 메모리 힙에 할당되고, 필요 없어질 때 가비지 컬렉터에 의해 회수된다.

### 2.2. 콜 스택

콜 스택은 코드에서 발생하는 함수 호출의 실행 순서와 관련된 정보를 저장하는 자료 구조다. 함수가 호출되면 해당 함수의 실행 컨텍스트가 콜 스택의 최상단에 푸시(push)되며, 함수의 실행이 완료되면 스택의 최상단에서 팝(pop)된다. 자바스크립트는 싱글 스레드 언어이기 때문에 한 번에 하나의 함수만 실행할 수 있다. 따라서, 하나의 함수가 실행 중일 때는 다른 함수들은 콜 스택에 대기하게 된다.

<br/>

## 3. 비동기 처리와 이벤트 루프

자바스크립트는 싱글 스레드 언어이지만, 비동기 처리를 통해 여러 작업을 효율적으로 관리한다. 비동기 처리는 작업의 완료를 기다리지 않고 다음 작업을 수행하는 것을 의미한다. 이를 관리하기 위해 자바스크립트는 `이벤트 루프`를 사용한다.

![diagram](https://miro.medium.com/v2/resize:fit:720/format:webp/1*FA9NGxNB6-v1oI2qGEtlRQ.png)

### 3.1. Web APIs

Web API는 브라우저에서 제공하는 인터페이스로, 비동기 작업을 수행할 수 있도록 돕는다. 대표적인 예로는 DOM 조작, AJAX 통신, setTimeout 함수 등이 있다. Web API는 논블로킹 방식으로 동작한다. 이러한 API를 통해 함수가 비동기로 실행되면, 해당 함수의 콜백은 콜백 큐에 삽입된다.

### 3.2. 콜백 큐

콜백 큐는 콜백 함수들이 대기하는 공간이다. 큐(Queue)는 데이터 구조 중 하나로, 먼저 들어온 것이 먼저 나가는 (First In First Out, FIFO) 원칙을 따른다. 비동기 작업이 완료되면 해당 콜백 함수는 이 큐에 추가된다.

### 3.3. 이벤트 루프

이벤트 루프는 콜 스택과 콜백 큐를 지속적으로 감시한다. 만약 콜 스택이 비어있다면, 콜백 큐의 함수를 콜 스택으로 옮겨서 실행한다. 이를 통해, 자바스크립트는 이벤트 기반의 실행을 보장하며, 싱글 스레드의 한계를 극복한다.

<br/>

## 4. 비동기 처리 예시와 실행 과정

```javascript
console.log("Hi");
setTimeout(function cb1() {
  console.log("cb1");
}, 5000);
console.log("Bye");
```

<br/>
위 코드의 실행 과정은 다음과 같다:
<br/>

![1](https://miro.medium.com/v2/resize:fit:720/format:webp/1*9fbOuFXJHwhqa6ToCc_v2A.png)
처음 상태는 텅 빈 상태이다.
<br/>
<br/>

![2](https://miro.medium.com/v2/resize:fit:720/format:webp/1*dvrghQCVQIZOfNC27Jrtlw.png)
코드의 첫 번째 줄인 `console.log("Hi")`는 함수이므로, 콜스택에 쌓이게 된다.  
(아직 함수가 실행되기 전의 상태이다. 콜스택은 쌓인 함수 하나하나를 '실행'하게 되기 때문이다.)
<br/>
<br/>

![3](https://miro.medium.com/v2/resize:fit:720/format:webp/1*yn9Y4PXNP8XTz6mtCAzDZQ.png)
`console.log("Hi")`가 실행되면 브라우저 콘솔에 찍히게 된다.
<br/>
<br/>

![4](https://miro.medium.com/v2/resize:fit:720/format:webp/1*iBedryNbqtixYTKviPC1tA.png)
`console.log("Hi")`가 실행되었기 때문에 콜스택에서 빠져나간다.(pop)  
이제, 콜스택에 어떤 함수도 남아있지 않다.
<br/>
<br/>

![5](https://miro.medium.com/v2/resize:fit:720/format:webp/1*HIn-BxIP38X6mF_65snMKg.png)
`setTimeout(function cb1() { ... })`도 함수이므로, call되면 무조건 콜스택에 쌓인다.
<br/>
<br/>

![6](https://miro.medium.com/v2/resize:fit:720/format:webp/1*vd3X2O_qRfqaEpW4AfZM4w.png)
`setTimeout(function cb1() { ... })`이 실행되면 브라우저는 Web API에 타이머를 등록한다. 등록된 ms만큼 타이머를 재고, ms가 지나야만 콜백을 호출하게 된다.
<br/>
<br/>

![7](https://miro.medium.com/v2/resize:fit:720/format:webp/1*_nYLhoZPKD_HPhpJtQeErA.png)
`setTimeout(function cb1() { ... })`이 실행이 되었기 때문에 콜스택에서 빠져나간다. 따라서 콜스택은 다시 빈 상태가 된다.
<br/>
<br/>

![8](https://miro.medium.com/v2/resize:fit:720/format:webp/1*1NAeDnEv6DWFewX_C-L8mg.png)
콜스택이 빈 상태였기 때문에 다음 줄 `console.log("Bye")`가 콜스택에 추가된다.
<br/>
<br/>

![9](https://miro.medium.com/v2/resize:fit:720/format:webp/1*UwtM7DmK1BmlBOUUYEopGQ.png)
`console.log("Bye")`가 실행된다.
<br/>
<br/>

![10](https://miro.medium.com/v2/resize:fit:720/format:webp/1*-vHNuJsJVXvqq5dLHPt7cQ.png)
`console.log("Bye")`가 콜스택에서 제거된다.
<br/>
<br/>

![11](https://miro.medium.com/v2/resize:fit:720/format:webp/1*eOj6NVwGI2N78onh6CuCbA.png)
5000ms 후에 타이머가 완료되면 `cb1` 콜백을 콜백 큐에 푸시한다.  
(콜백 큐에 콜백이 이런 식으로 쌓이게 됨)
<br/>
<br/>

![12](https://miro.medium.com/v2/resize:fit:720/format:webp/1*jQMQ9BEKPycs2wFC233aNg.png)
이벤트 루프는 콜백 큐와 콜 스택을 항상 보고 있다가, 콜 스택이 비어있으면 콜백 큐에 있던 `cb1`을 콜 스택에 전달해준다.
즉, 이벤트 루프는 `cb1`을 콜백 큐에서 꺼내 콜 스택에 푸쉬한다.
<br/>
<br/>

![13](https://miro.medium.com/v2/resize:fit:720/format:webp/1*hpyVeL1zsaeHaqS7mU4Qfw.png)
콜 스택에 콜백 함수인 `cb1`이 들어왔으므로 `cb1` 을 실행한다. 그러면 `console.log('cb1')`이 콜 스택에 들어오게 된다.  
<br/>
<br/>

![14](https://miro.medium.com/v2/resize:fit:720/format:webp/1*lvOtCg75ObmUTOxIS6anEQ.png)
`console.log('cb1')`이 실행되고 브라우저 콘솔에 'cb1'이 찍히게 된다.
<br/>
<br/>

![15](https://miro.medium.com/v2/resize:fit:720/format:webp/1*Jyyot22aRkKMF3LN1bgE-w.png)
`console.log('cb1')`이 실행되었으므로 콜스택에서 제거된다.
<br/>
<br/>

![16](https://miro.medium.com/v2/resize:fit:720/format:webp/1*t2Btfb_tBbBxTvyVgKX0Qg.png)
`cb1`도 다 실행되었기 때문에 콜스택에서 빠져나간다.

<br/>

레퍼런스  
https://medium.com/sessionstack-blog/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5  
https://www.youtube.com/watch?v=zi-IG6VHBh8  
https://velog.io/@yangddu/JS-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EB%8F%99%EC%9E%91%EC%9B%90%EB%A6%AC  
https://joshua1988.github.io/web-development/translation/javascript/how-js-works-inside-engine/
