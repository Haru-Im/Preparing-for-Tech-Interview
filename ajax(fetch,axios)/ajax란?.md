# AJAX란?

AJAX는 `"Asynchronous JavaScript and XML"`의 약자로, 웹 페이지가 전체 페이지를 다시 로드하지 않고도 서버와 데이터를 교환할 수 있도록 해주는 기술이다. 이 기술은 사용자의 경험을 크게 향상시키며, 특히 웹 애플리케이션의 성능 개선에 중요한 역할을 한다.
<br/>

### 1. **AJAX의 탄생 배경**:

- 과거 웹 페이지는 `SSR(Server-Side Rendering)` 방식을 사용했다. 이 방식은 사용자가 요청할 때마다 새로운 페이지가 서버로부터 로드되고 전체 페이지가 새로고침되었다. 이로 인해 사용자 경험은 끊겨 보일 수 있었고, 서버에 불필요한 부하를 주는 원인도 되었다.
- `AJAX`는 이러한 문제를 해결하기 위해 고안되었다. 페이지의 일부분만 비동기적으로 업데이트할 수 있도록 함으로써 사용자 경험을 원활하게 하고, 서버 부하를 줄이는 효과를 가져왔다.
  <br/>

### 2. **AJAX의 개념 및 정의**:

- AJAX는 비동기적인 웹 애플리케이션 제작을 위한 기술이다. "비동기적"이란, 웹 페이지가 서버로부터 응답을 기다리는 동안 다른 작업(예: 입력)을 계속할 수 있다는 것을 의미한다.
- JavaScript를 사용해 서버와 브라우저가 백그라운드에서 데이터를 교환할 수 있게 하며, 페이지의 일부만을 업데이트한다. 이 과정은 페이지 새로고침 없이 이루어지므로 빠른 퍼포먼스와 더 나은 사용자 경험을 제공한다.
  <br/>

### 3. **전통적인 AJAX의 사용 예시**:

- 원래 AJAX는 `XMLHttpRequest` 객체를 이용해 구현했다. 이 객체를 이용하여 서버로 요청을 보내고, 반환된 데이터(초기에는 주로 XML 형식)를 사용하여 웹 페이지 일부를 업데이트했다.

```javascript
var xhr = new XMLHttpRequest(); // XMLHttpRequest 객체 생성
xhr.open("GET", "/api/data", true); // 비동기적으로 서버에게 데이터 요청

xhr.onreadystatechange = function () {
  if (xhr.readyState === 4) {
    // 요청이 완료되었고, 응답이 준비된 상태
    if (xhr.status === 200) {
      // 서버로부터 정상적인 응답 수신
      console.log(xhr.responseText); // 서버로부터 받은 데이터 출력
    } else {
      console.error("에러 발생: " + xhr.status); // 오류 메시지 출력
    }
  }
};

xhr.send(); // 요청 전송
```

<br/>

### 4. **현대적인 AJAX 사용 방법**:

최근에는 여러 가지 새로운 API와 라이브러리가 등장하여 AJAX를 더욱 쉽고 효율적으로 사용할 수 있게 되었다.

#### 4-1. **Fetch**:

- `fetch`는 AJAX를 위한 모던한 API다. 간결하고 유연한 문법을 제공하여 비동기 HTTP 요청을 처리한다. `fetch`는 Promise 기반으로, 코드가 훨씬 깔끔해지고 콜백 지옥에서 벗어날 수 있다.

<br/>
fetch를 이용한 AJAX 요청:

```javascript
fetch("/api/data")
  .then((response) => {
    if (!response.ok) {
      throw new Error("네트워크 응답이 올바르지 않음");
    }
    return response.json(); // JSON 형태로 파싱
  })
  .then((data) => {
    console.log(data); // 서버로부터 받은 데이터 출력
  })
  .catch((error) => {
    console.error("데이터 가져오는 중 문제 발생:", error);
  });
```

<br/>

#### 4-2. **Axios**:

- `axios`는 HTTP 클라이언트 라이브러리로, 브라우저와 Node.js에서 모두 사용할 수 있다. `fetch`보다 더 많은 기능을 제공하며, HTTP 요청을 쉽게 처리할 수 있도록 도와준다.

<br/>
axios를 이용한 AJAX 요청:

```javascript
axios
  .get("/api/data")
  .then((response) => {
    console.log(response.data); // 서버로부터 받은 데이터 출력
  })
  .catch((error) => {
    console.error("에러 발생:", error);
  });
```

<br/>
AJAX는 현대 웹 개발에 있어 표준적인 기술로 자리 잡았으며, 위의 방법들을 사용하면 웹 애플리케이션의 사용성과 퍼포먼스를 대폭 개선할 수 있다. 여러 다양한 라이브러리와 도구를 활용하여 더 나은 사용자 경험을 제공하고, 개발 프로세스를 보다 효율적으로 관리할 수 있다.
