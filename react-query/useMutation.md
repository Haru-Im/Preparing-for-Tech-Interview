# `useMutation`에 대해

## 1. **useMutation의 개념:**

- `useMutation`은 React Query 라이브러리에 속한 함수로, 서버에 변형을 가하는 비동기 작업들을 쉽게 처리하고 이에 대한 상태를 관리할 수 있게 해준다. 이는 주로 POST, PUT, DELETE 등의 HTTP 요청을 포함하며, 이 훅을 통해 데이터의 생성, 수정, 삭제 같은 작업을 수행한다. 이 과정에서 해당 작업의 상태(로딩, 에러, 성공)와 관련 데이터, 함수 등을 제공받는다.

## 2. **간단한 사용 예시:**

- 예를 들어, 특정 사용자 데이터를 서버에 업데이트하는 작업을 수행한다고 가정하겠다.

```javascript
import { useMutation } from "react-query";

function UserProfile({ userId }) {
  // useMutation 훅 사용
  const mutation = useMutation((newUserData) =>
    updateUserOnServer(userId, newUserData)
  );

  const handleSubmit = (newUserData) => {
    // mutation.mutate 메서드를 호출하여 비동기 작업 수행
    mutation.mutate(newUserData);
  };

  // 로딩, 에러, 데이터 상태에 따라 UI 렌더링
  return (
    <div>
      {mutation.isLoading ? (
        "Updating user data..."
      ) : (
        <>
          {mutation.isError ? (
            <div>An error occurred: {mutation.error.message}</div>
          ) : null}

          {mutation.isSuccess ? <div>User update successful!</div> : null}

          <button onClick={() => handleSubmit({ name: "New Name" })}>
            Update User Data
          </button>
        </>
      )}
    </div>
  );
}

// 서버와의 통신을 담당하는 함수 (예시)
async function updateUserOnServer(userId, newUserData) {
  // 서버에 요청 보내기
  // ...
}
```

이 코드에서 `useMutation`은 `updateUserOnServer`라는 비동기 함수를 인자로 받아 사용하며, 해당 함수는 서버에 사용자 데이터를 업데이트하는 요청을 보낸다. `useMutation` 훅은 현재 요청의 상태를 반영하는 여러 속성값(예: `isLoading`, `isError`, `error`, `isSuccess`)을 제공한다. 이를 통해 사용자에게 요청의 진행 상태나 결과를 안내하는 UI를 적절히 제공할 수 있다.
