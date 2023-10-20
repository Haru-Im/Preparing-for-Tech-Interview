# Recoil에 대하여

Recoil은 Facebook에서 개발한 상태 관리 라이브러리로, React 애플리케이션에서 상태를 효율적으로 관리할 수 있는 도구이다. Recoil을 사용하면 컴포넌트 간의 상태 공유, 비동기 데이터 쿼리, 성능 최적화 등을 더 쉽고 직관적으로 처리할 수 있다.

## 1. **Recoil의 개념**

1.1. **기본 개념**: Recoil은 React의 상태 관리를 위해 만들어진 라이브러리이다. 기존 React의 Context API가 가진 문제점을 해결하고, 전역 상태 관리, 비동기 작업 관리, 복잡한 상태 간의 의존성 관리 등을 수행한다.

## 2. **Atom, Selector 및 예시**

2.1. **Atom**: Atom은 Recoil의 기본 빌딩 블록이다. 각 Atom은 상태의 일부를 나타내며, 이를 구독하는 컴포넌트에 자동으로 반영된다. 컴포넌트에서 Atom을 읽거나 쓸 수 있다.

```javascript
import { atom } from "recoil";

// Atom 정의
export const counterState = atom({
  key: "counterState", // 고유한 ID (Atom을 식별하는 데 사용됨)
  default: 0, // 기본값
});
```

2.2. **Selector**: Selector는 파생된 상태를 나타낸다. 기본적으로 다른 Atom 또는 Selector로부터 상태를 받아 변환한다. 이는 계산된 값 또는 비동기 쿼리와 같은 부수 효과를 다룰 수 있는 방법이다.

```javascript
import { selector } from "recoil";
import { counterState } from "./atom";

// Selector 정의
export const doubledCounterState = selector({
  key: "doubledCounterState",
  get: ({ get }) => {
    const count = get(counterState);
    return count * 2;
  },
});
```

## 3. **'구독' 개념 및 예시**

3.1. **구독**: Atom이나 Selector의 상태가 변경되면, 이를 '구독'하는 컴포넌트가 리렌더링된다. 이 구독 시스템은 React의 상태 변경과 유사하게 작동하며, 상태가 변경되면 의존하는 컴포넌트만 업데이트된다.

```javascript
import { useRecoilState } from "recoil";
import { counterState } from "./state";

function CounterComponent() {
  const [count, setCount] = useRecoilState(counterState);

  // counterState가 변경되면 CounterComponent는 자동으로 리렌더링된다.
  return (
    <div>
      <button onClick={() => setCount(count - 1)}>-</button>
      <span>{count}</span>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  );
}
```

## 4. **AtomFamily 및 동적인 키 할당**

4.1. **AtomFamily**: AtomFamily는 비슷한 Atom들을 매개변수화하여 생성할 수 있게 해주는 유틸리티이다. 이를 사용하면 동적인 키를 기반으로 상태를 생성하고 관리할 수 있다. 이 방법은 아이템 리스트나 ID를 기반으로 하는 개별적인 상태를 효율적으로 관리할 수 있다.

```javascript
import { atomFamily } from "recoil";

// AtomFamily 정의
export const itemStateFamily = atomFamily({
  key: "itemStateFamily",
  default: (initialValue) => ({
    text: "",
    isComplete: false,
    ...initialValue,
  }),
});

// 컴포넌트에서 사용할 때, 특정 ID를 기반으로 상태를 가져온다.
const [item, setItem] = useRecoilState(itemStateFamily(someUniqueId));
```

## 5. **useRecoilCallback 및 비동기 작업 관리**

5.1. **useRecoilCallback**: useRecoilCallback은 Recoil 상태에 접근하거나 수정하는 커스텀 훅을 생성할 수 있다. 이를 사용하여 외부 API 호출과 같은 비동기 작업을 수행하고 상태를 업데이트할 수 있다.

```javascript
import { useRecoilCallback } from "recoil";

function useFetchData() {
  const fetchData = useRecoilCallback(({ snapshot, set }) => async () => {
    try {
      const response = await fetch("https://api.example.com/data");
      const data = await response.json();

      // 비동기 작업 후, Atom을 업데이트한다.
      const currentCounter = await snapshot.getPromise(counterState);
      set(counterState, currentCounter + data.increment);
    } catch (error) {
      console.error("Failed to fetch data: ", error);
    }
  });

  return fetchData;
}
```

위의 useRecoilCallback 예시에서는 외부 API를 호출한 후, 반환된 데이터를 기반으로 상태를 업데이트한다. 이러한 패턴은 Recoil 상태와 비동기 작업을 더욱 유연하고 효과적으로 관리할 수 있도록 해준다.
