Query params, 즉 쿼리 파라미터는 웹 URL의 일부로, 클라이언트가 서버에 특정 정보를 요청할 때 추가적인 데이터를 전달하는 데 사용된다. 이 파라미터는 URL의 끝부분에 위치하며, 주로 검색, 필터링, 페이지네이션 등에 사용되어 결과를 세분화하는 데 도움을 준다.

쿼리 파라미터는 URL에서 '?' 문자 뒤에 나열된다. 각 파라미터는 '키=값' 형태를 가지며, 여러 개의 파라미터가 있는 경우 '&' 문자로 구분된다.

예를 들어, 사용자가 온라인 쇼핑몰에서 특정 제품을 검색할 때 다음과 같은 URL이 생성될 수 있다:

```
https://example.com/products?category=sneakers&color=black&size=10
```

위 URL에서,

- `category=sneakers`, `color=black`, `size=10`은 각각 쿼리 파라미터다.
- `category`, `color`, `size`는 파라미터의 키다.
- `sneakers`, `black`, `10`는 각 키의 값이다.
- 이 쿼리 파라미터들은 웹 서버에게 사용자가 '스니커즈' 카테고리에서 검은색, 사이즈 10의 제품을 찾고 있음을 알려준다.

웹 애플리케이션은 이러한 파라미터를 사용하여 서버로부터 받은 요청을 해석하고, 요청한 정보에 따라 적절한 결과를 클라이언트에 반환한다. 쿼리 파라미터는 GET 요청과 함께 사용되며, URL의 일부이기 때문에 사용자가 브라우저에서 URL을 공유하거나 저장할 때 요청한 동일한 정보를 다시 볼 수 있게 한다.

---

### 예시

다음은 GET 요청에서 쿼리 파라미터를 사용하는 방법에 대한 예시다.

#### **쿼리 파라미터를 사용한 GET 요청 예시:**

가정: 사용자가 특정 카테고리의 게시물만을 검색하고 싶어하는 상황

```javascript
import axios from "axios";

// 사용자로부터 입력 받은 카테고리 정보
const category = "news";

// axios를 사용하여 해당 카테고리의 게시물을 요청하는 함수
async function fetchPostsByCategory() {
  try {
    const response = await axios.get(
      `https://api.example.com/posts?category=${category}`
    );
    console.log(response.data);
  } catch (error) {
    console.error("서버로부터 데이터를 가져오는 데 실패했습니다:", error);
  }
}

fetchPostsByCategory();
```

위의 코드에서는 `axios.get` 함수를 사용하여 특정 카테고리의 게시물을 조회하는 GET 요청을 보낸다. URL의 끝에 `?category=${category}`를 추가하여 쿼리 파라미터를 전달한다.
