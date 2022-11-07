# React Router 6.4.2+ Tutorial

## 목차

- Handling Not Found Errors

## Handling Not Found Errors

- 애플리케이션이 렌더링 하고 있을 때나 데이터를 로딩하고 있을 때 아니면 데이터를 가지고 `mutation`을 수행하고 있는 동안 에러가 발생할 때마다 **React Router**가 에러를 감지하고 에러 페이지를 렌더링한다.
- `useRouteError`는 발생한, 즉 **React Router**가 감지한 에러를 제공한다. 예를 들어, 사용자가 존재하지 않는 경로로 이동했을 때 `statusText` 속성에 **Not Found**가 담긴 `error response`를 전달받는다.

## Nested Routes

- `children` 속성에 경로와 컴포넌트를 위치시키면 추후에 해당 경로와 일치하는 경우 상위 컴포컴포넌트에서 `Outlet` 컴포넌트를 이용하여 경로에 담긴 컴포넌트를 렌더링할 수 있다.

## Client Side Rendering

- **Single Page Application** 그리고 **Client Side Rendering**을 하고 있는 이상 경로를 이동할 때(브라우저 URL 탭에서 직접 입력 제외) 백엔드 서버에게 새로운 네트워크 요청을 발생시켜 전체 `Document`를 받아오는 것이 아니라 **React Router**가 내부 컨텐츠를 변경하도록 만들어줘야 한다.
- 따라서, `a` 태그 대신 `Link` 컴포넌트를 이용하여 애플리케이션이 **React Router**를 이용하여 새로운 UI를 렌더링하도록 만든다.

## Loading Data

- 데이터를 로딩하는데 사용할 두 가지 API가 추가됐다. 바로 `loader`와 `useLoaderData`

### `loader`

각 경로는 `loader` 함수를 정의하여 `element` 속성에 담긴 컴포넌트가 렌더링되기 전에 해당 컴포넌트에게 데이터를 전달할 수 있다.

- 사용자가 애플리케이션을 탐색할 때 일치하는 경로에 대한 `loader`가 병렬로 호출되고 `loader`의 반환하는 값을 `useLoaderData`를 통해서 각 컴포넌트에서 사용이 가능하다.
- `params`
  - 동적 세부 경로(`dynamic segment`)에 대한 값이 담긴 매개변수
- `request`
  - `Fetch Request` 인스턴스가 담긴 매개변수
  - 예를 들어, `a` 태그를 사용하는 경우 해당 링크를 클릭하는 순간 브라우저가 경로를 가지고 서버에 요청을 만들어 낸다. **React Router**를 사용하고 있고 `Link` 컴포넌트를 사용 중이라면 브라우저가 만들어낸 요청 인스턴스를 서버에 보내는 대신 **React Router**가 해당 인스턴스를 `loader`에게 보내게 만든다.

### `useLoaderData`

`loader`가 반환한 값을 사용할 수 있게 하는 훅

- `useLoaderData`는 데이터 패칭을 발생시키지 않는다. **React Router**가 내부적으로 관리하고 있는 데이터 패칭의 결과값을 단순히 읽어올 뿐이기 때문에 컴포넌트가 리렌더링될 때 `useLoaderData`으로 인한 `refetch`는 고려하지 않아도 된다.
- 이 말인 즉슨 `useLoaderData`로 반환받은 값은 렌더링 간에 안정적이므로 안심하고 `useEffect`와 같은 훅의 의존성 배열에 집어넣어도 된다. 이는 오직 `action` 함수가 호출되거나 특정 경로로 이동하여 `loader`가 다시 호출된 경우에만 변경될 것이다.
- `action` 함수가 호출된 후에 `useLoaderData`를 통해 반환받은 값은 자동으로 `revalidate` 되고 `loader`에서 가장 마지막으로 반환한 값이 담긴다.
- `useLoaderData는` 아무 훅이나 컴포넌트에서 사용해도 상관없다. 이는 `Context` 내에서 해당 훅을 호출한 컴포넌트의 가장 근처에 있는 `Route`의 `loader` 함수가 반환한 데이터를 가져올 것이다.

### `useRouteLoaderData`

페이지 내에 활성화되어 있는 Route의 `loader` 함수가 반환한 데이터를 가져올 때 사용할 수 있는 훅

## Data Writes + HTML Forms

- 일부 웹 개발자들에게는 생소할 수도 있지만 **HTML** `form`은 `link`를 클릭하는 것과 같이 브라우저에 네비게이션을 일으킨다. `link`와 `form`의 차이점은 `link`는 **URL**만을 바꾸지만 `form`은 `request method`(**GET** vs **POST**)와 `request body`(**POST** `form` 데이터)도 바꿀 수 있다.
- 클라이언트 사이드 라우팅이 없다면 브라우저는 `form` 데이터를 직렬화하여 **POST** 요청의 `body`에 담아 서버에게로 보내고 `URLSearchParams`의 경우 **GET** 요청에 **Query String Parameter**에 담아 보낸다. `React Router`도 동일한 작업을 한다. 단지 서버에게 요청을 보내는 대신 클라이언트 사이드 라우팅을 사용하고 `form` 데이터나 `URLSearchParams`를 `Route` 컴포넌트의 `action` 함수에게로 보낸다.

### `action`

> `createBrowserRouter`를 사용해야만 `action`을 사용할 수 있다.

`Route`의 `action`은 `loader`가 하는 `읽기`(reads)의 `쓰기`(wrties) 버전이다.  
`action`은 비동기 **UI**와 `revalidation`의 복잡성은 제거하는 반면 간단한 **HTML**과 **HTTP** 시멘틱으로 애플라케이션이 데이터 `mutation`을 수행하는 방법을 제공한다. 이는 우리에게 현대의 **SPA**의 동작 및 **UX** 기능과 함께 브라우저가 비동기와 `revalidation`을 처리하는 **HTTP** + **HTML**의 `mental model`을 제공한다.

- `params`
  - 동적 세부 경로(`dynamic segment`)에 대한 값이 담긴 매개변수
- `request`
  - `Fetch Request` 인스턴스가 담긴 매개변수
  - 예를 들어, `a` 태그를 사용하는 경우 해당 링크를 클릭하는 순간 브라우저가 경로를 가지고 서버에 요청을 만들어 낸다. **React Router**를 사용하고 있고 `Link` 컴포넌트를 사용 중이라면 브라우저가 만들어낸 요청 인스턴스를 서버에 보내는 대신 **React Router**가 해당 인스턴스를 `loader`에게 보내게 만든다.

### useActionData

`action`이 반환한 값을 사용할 수 있게 하는 훅

## Creating Contacts

- **HTML** `form` 태그를 `Form` 컴포넌트로 변경하면 앞서 언급했듯이 브라우저가 서버로 요청을 보내는 대신 `Route`의 `action`으로 해당 요청을 보낸다.
- 웹 시멘틱에서 **POST** 요청은 주로 어떠한 데이터를 변경하는 것을 의미한다. 컨벤션에 따라 **React Router**는 이를 힌트로 삼아 `action`이 완료된 후에 페이지의 데이터를 자동으로 `revalidate` 할 것이다. 이 말인 즉슨 모든 `useLoaderData` 훅이 재호출되어 자동으로 업데이트된 데이터를 이용하여 **UI**를 동기화한다는 말이 된다.
