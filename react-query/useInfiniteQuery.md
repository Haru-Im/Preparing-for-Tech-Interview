`useInfiniteQuery`는 React Query의 한 기능으로, 스크롤을 내릴 때마다 추가 콘텐츠를 로드하는 무한 스크롤링을 구현하는 데 쓰인다. 이 방법은 페이지의 끝에 도달했을 때 추가 데이터를 자동으로 로드하여, 사용자가 계속해서 새로운 콘텐츠를 탐색할 수 있게 해준다.

### `useInfiniteQuery` 사용 방법:

1. **데이터 페치 함수 설정**: 서버에서 페이지나 데이터 세트를 가져오는 함수를 정의한다. 이 함수는 페이지 번호나 커서와 같은 페이징 정보를 인수로 사용할 수 있다.

2. **`useInfiniteQuery` 사용**: 해당 함수와 함께 `useInfiniteQuery`를 사용하여 쿼리를 수행하고, 데이터와 페이지를 관리한다. 이 때 `getNextPageParam` 함수를 통해 다음 페이지 요청 정보를 설정할 수 있다.

### 코드 예시:

다음은 `useInfiniteQuery`를 사용한 무한 스크롤 구현 예시이다.

```javascript
import React from 'react';
import { useInfiniteQuery } from 'react-query';
import axios from 'axios';

// 무한 스크롤을 위한 컴포넌트
function Posts() {
  // 포스트를 가져오는 함수
  const fetchPosts = ({ pageParam = 0 }) =>
    axios.get(`/api/posts?page=${pageParam}`);

  // useInfiniteQuery 사용
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
  } = useInfiniteQuery('posts', fetchPosts, {
    // 다음 페이지 정보를 가져오는 함수
    getNextPageParam: (lastPage, allPages) => {
      const morePagesExist = /* 다음 페이지 존재 여부 */;
      if (!morePagesExist) return false;
      return lastPage.nextPage;  // 다음 페이지 번호 반환
    },
  });

  // 스크롤 이벤트 핸들러
  const handleScroll = () => {
    // 스크롤이 페이지 하단에 도달했는지 체크하고 다음 페이지 로드
    if (window.innerHeight + document.documentElement.scrollTop !== document.documentElement.offsetHeight) return;
    if (hasNextPage) fetchNextPage(); // 다음 페이지가 있다면 fetch
  };

  // 스크롤 이벤트 리스너 등록
  React.useEffect(() => {
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  }, []);

  // 포스트 렌더링
  return (
    <div>
      {data.pages.map((page, index) => (
        // 포스트 데이터 렌더링
        <React.Fragment key={index}>
          {page.data.map(post => (
            <p key={post.id}>{post.title}</p>
          ))}
        </React.Fragment>
      ))}
      {isFetchingNextPage && <span>Loading...</span>}
    </div>
  );
}
```

위 코드는 스크롤 이벤트를 기반으로 페이지 하단에 도달했을 때 `fetchNextPage`를 호출하여 새 데이터를 불러오는 방식을 보여준다. `getNextPageParam` 함수는 현재 로드된 페이지 바탕으로 다음 페이지의 정보를 결정한다.
