# reactQuery

## 장점
- 캐싱
- get을 한 데이터에 대해 update를 하면 자동으로 get을 다시 수행한다. (예를 들면 게시판의 글을 가져왔을 때 게시판의 글을 생성하면 게시판 글을 get하는 api를 자동으로 실행 )
- 데이터가 오래 되었다고 판단되면 다시 get (invalidateQueries)
- 동일 데이터 여러번 요청하면 한번만 요청한다. (옵션에 따라 중복 호출 허용 시간 조절 가능)
- 무한 스크롤 (Infinite Queries (opens new window))
- 비동기 과정을 선언적으로 관리할 수 있다.
- react hook과 사용하는 구조가 비슷하다.


## 설치 및 설정
```
npx create-react-app my-app
cd my-app
yarn install react-query
yarn install && yarn start
```

```
//index.js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import { QueryClient, QueryClientProvider } from "react-query";
import { ReactQueryDevtools } from "react-query/devtools";

const queryClient = new QueryClient();

ReactDOM.render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      {/* devtools */}
      <ReactQueryDevtools initialIsOpen={true} />
      <App />
    </QueryClientProvider>
  </React.StrictMode>,
  document.getElementById("root")
);
```

## useQuery
- 데이터를 get 하기 위한 api
- 첫번째 파라미터로 unique Key가 들어가고, 두번째 파라미터로 비동기 함수(api호출 함수)가 들어갑니다. (당연한 말이지만 두번째 파라미터는 promise가 들어가야합니다.)
- useQuery는 비동기로 작동
- enabled를 사용하면 useQuery를 동기적으로 사용 가능

```
const { isLoading, isError, data, error } = useQuery("todos", fetchTodoList, {
    refetchOnWindowFocus: false, // react-query는 사용자가 사용하는 윈도우가 다른 곳을 갔다가 다시 화면으로 돌아오면 이 함수를 재실행합니다. 그 재실행 여부 옵션 입니다.
    retry: 0, // 실패시 재호출 몇번 할지
    onSuccess: data => {
      // 성공시 호출
      console.log(data);
    },
    onError: e => {
      // 실패시 호출 (401, 404 같은 error가 아니라 정말 api 호출이 실패한 경우만 호출됩니다.)
      // 강제로 에러 발생시키려면 api단에서 throw Error 날립니다. (참조: https://react-query.tanstack.com/guides/query-functions#usage-with-fetch-and-other-clients-that-do-not-throw-by-default)
      console.log(e.message);
    }
  });


//enabled
const { data: todoList, error, isFetching } = useQuery("todos", fetchTodoList);
const { data: nextTodo, error, isFetching } = useQuery(
  "nextTodos",
  fetchNextTodoList,
  {
    enabled: !!todoList // true가 되면 fetchNextTodoList를 실행한다
  }
);
```
## useQuery 동기적으로 실행
```
const { data: todoList, error, isFetching } = useQuery("todos", fetchTodoList);
const { data: nextTodo, error, isFetching } = useQuery(
  "nextTodos",
  fetchNextTodoList,
  {
    enabled: !!todoList // true가 되면 fetchNextTodoList를 실행한다
  }
);
```
## useQueries
```
const result = useQueries([
  {
    queryKey: ["getRune", riot.version],
    queryFn: () => api.getRunInfo(riot.version)
  },
  {
    queryKey: ["getSpell", riot.version],
    queryFn: () => api.getSpellInfo(riot.version)
  }
]);
```
