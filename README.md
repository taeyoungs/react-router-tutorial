# React Router 6.4.2+ Tutorial

**목차**

- [React Router 6.4.2+ Tutorial](#react-router-642-tutorial)
  - [Handling Not Found Errors](#handling-not-found-errors)
  - [Nested Routes](#nested-routes)
  - [Client Side Rendering](#client-side-rendering)
  - [Loading Data](#loading-data)
    - [`loader`](#loader)
    - [`useLoaderData`](#useloaderdata)
    - [`useRouteLoaderData`](#userouteloaderdata)
  - [Data Writes + HTML Forms](#data-writes--html-forms)
    - [`action`](#action)
    - [useActionData](#useactiondata)
  - [Creating Contacts](#creating-contacts)
  - [URL Parmas in Loaders](#url-parmas-in-loaders)
  - [Updating Data](#updating-data)
  - [Updating Contacts with FormData](#updating-contacts-with-formdata)
  - [Mutation Discussion](#mutation-discussion)
  - [Active Link Styling](#active-link-styling)
    - [NavLink](#navlink)
  - [Global Pending UI](#global-pending-ui)
    - [`useNavigation`](#usenavigation)
    - [`navigation.state`](#navigationstate)
    - [`navaigation.formData`](#navaigationformdata)
    - [`navigation.location`](#navigationlocation)

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

## URL Parmas in Loaders

`Route`의 `path` 속성에 준 `contacts/:contactId` 값에서 `:contactId`는 **URL** 세그먼트다. `:`을 통해서 해당 세부 경로를 **dynamic segment**로 변환하며 이를 `URL Parmas`, 짧게는 `params`라고 부른다.

- `loader`에게 전달하는 콜백 함수의 매개변수를 통해서 해당 `Route`에 전달되는 `URL Params`에 접근할 수 있다.

## Updating Data

- 새로운 데이터를 생성할 때와 동일하게 `Form` 컴포넌트를 이용하여 데이터를 업데이트할 수도 있다.
- 예제에서는 `contactLoader`를 `contacts/:contactId/edit`에도 전달하고 있지만 일반적으로 `loader`를 공유하는 일은 거의 없다. 이는 단지 튜토리얼이기 때문이고 실무에서는 웬만하면 `Route` 마다 고유의 `loader`가 존재할 것이다.
- 세부 경로로 진입하면서 `Root` 컴포넌트 내 `Outlet` 컴포넌트에 전달되는 컴포넌트가 변경된다. 즉, 경로와 일치하는 `element`가 `Outlet`에서 렌더링된다.

## Updating Contacts with FormData

- `route`에 `action`을 연결하면 `Form` 컴포넌트에서 발생한 **POST** 요청이 `action`으로 전달될 것이고 데이터는 자동으로 `revalidate` 될 것이다.

## Mutation Discussion

- **JavaScript** 없이 `form`을 `submit`하면 브라우저는 `FormData` 생성할 것이고 이를 서버에 보낼 요청의 `body`에 집이 넣는다.
- 이전에도 언급했듯이 **React Router**는 이러한 서버에게로 가는 요청을 막고 `FormData`를 포함하고 있는 이 요청을 `action`에게로 보낸다.
  - `FormData`의 각 필드는 `formData.get(name)`를 사용하여 접근이 가능하다. 여기서 `name`은 `input`이 갖고 있던 `name` 속성이다.
  - 여기서 `request`, `request.formData`, `Object.fromEntries`는 **React Router**가 제공하는 것이 아니라 웹에서 제공하고 있는 것들이다.
- `loader`와 `action`은 둘 다 `Response`를 반환할 수 있다. `Response`를 반환하는 상황이 이상한 게 아닌 것이 이미 `loader`와 `action`은 **React Router**를 통해 `Request`를 받았다.
  - `redirect`를 통해서 애플리케이션에게 경로가 바뀌었다는 것을 쉽게 알려줄 수 있다.
- 클라이언트 사이드 라우팅이 없다면 서버에게 POST 요청을 보낸 후에 `redirect` 되고 새로운 페이지는 최신 데이터를 패칭한 후 새로 렌더링하게 될 것이다.
  - 이전에 배웠듯이 **React Router**는 `action` 이후에 자동으로 데이터를 `revalidate` 한다.
  - 그렇기에 **Sidebar**의 데이터들도 업데이트가 된다.

## Active Link Styling

### NavLink

- `Link` 컴포넌트와 동일하지만 현재 경로와 일치하는지 여부를 추가적으로 알 수 있는 컴포넌트다.
  - 현재 선택된 링크가 무엇인지 사용자에게 알리고자 할 때 그리고 **Screen Reader**에게 정보를 전달하고자 할 때도 유용하다.
- `NavLink`가 활성화되어 있는 상태라면 기본적으로 `active` 클래스가 추가된다.
- 이전 버전과 다른 점이 하나 있다면 `NavLinkProps`를 없애고 `style`과 `classname` 속성에 콜백 함수를 전달할 수 있게끔 만들었다.
  - 해당 콜백 함수의 매개변수에는 `isActive`가 존재하기 때문에 이를 가지고 추가적인 스타일링이나 클래스 네임 부여가 가능하다.

## Global Pending UI

- **React Router**는 페이지를 이동할 때 다음 페이지가 로딩 상태인 동안 이전 페이지(이동하기 전 페이지)를 보여준다. 이는 사용자로 하여금 페이지가 아무런 동작도 하지 않고 멈춰버렸다고 생각하게끔 만들 수도 있다.
  - 따라서, **React Router**는 이러한 상황을 대처하는 데 필요한 다양한 정보들을 `useNavigation` 훅으로 제공한다.
- **data model**이 클라이언트 사이드 캐시를 사용하고 있기 때문에 두 번째 방문 때부터는 로딩 상태가 보이지 않는다.

### `useNavigation`

`useNavigation`은 페이지 이동할 때 사용하는 **navigation indicator**(ex. 로딩 스피너) 그리고 데이터 `mutation`에 관한 낙관적 **UI**를 빌드하기 위한 모든 정보를 제공한다.

- **Global loading indicators**
- `mutation`이 수행하는 동안 `form` 비활성화
- submit 버튼에 로딩 스피너와 같은 **busy indicator** 추가
- 서버에서 데이터를 생성하는 동안 낙관적 업데이트를 통한 데이터 표시
- 서버에서 데이터가 업데이트 되는 동안 낙관적 업데이트를 통한 데이터 표시

`useNavigation`이 반환하는 객체(`navigation` 식별자에 담겨 있다고 가정)에는 다음과 같은 정보들이 담겨있다.

### `navigation.state`

- `idle`: `Pending` 상태 (네비게이션 발생 X)
- `submitting`: **POST**, **PUT**, **PATCH**, **DELETE** 중 하나로 `form`의 ₩이 발생하여 `route`의 `action`이 호출된 상태
- `loading`: 페이지 이동(네비게이션)이 발생하여 `loader`가 호출된 상태

일반적인 네비게이션이나 `form`의 `submit`이 **GET** 요청으로 발생했다면 `navigation.state`는 다음과 같은 흐름을 가진다.

```
idle -> loading -> idle
```

**POST**, **PUT**, **PATCH**, **DELETE** 중 하나로 `form`의 `submit`이 발생한 상태라면 `navigation.state`는 다음과 같은 흐름을 가진다.

```
idle -> submitting -> loading -> idle
```

`useNavigation`이 제공하는 정보들을 조합하면 다음과 같은 고차원의 상태 체크가 가능하다.

```javascript
// Is this just a normal load?
let isNormalLoad = navigation.state === 'loading' && navigation.formData == null;

// Are we reloading after an action?
let isReloading =
  navigation.state === 'loading' &&
  navigation.formData != null &&
  navigation.formAction === navigation.location.pathname;

// Are we redirecting after an action?
let isRedirecting =
  navigation.state === 'loading' &&
  navigation.formData != null &&
  navigation.formAction !== navigation.location.pathname;
```

### `navaigation.formData`

- `<Form>` 또는 `useSubmit`에서 시작된 **POST**, **PUT**, **PATCH**, **DELETE** 요청이라면 해당 요청의 `body`에 담긴 데이터가 `navaigation.formData`에 담긴다.
- 이 데이터는 주로 `action` 발생 후 낙관적 업데이트를 통한 **UI** 변경에 사용된다.

### `navigation.location`

이동할 경로에 대한 정보가 담겨 있는 속성

`form`이 링크가 가리키고 있는 **URL**로 `submit` 되는 경우 링크는 `pending`로 표시되지 않는다. 왜냐하면 `navigation.state`가 `loading` 상태일 때만 링크를 `pending` 상태로 만드는 작업을 수행하기 때문이다. 따라서, `navigation.state`가 `submitting`이다가 action이 완료되면 링크가 `pedning` 상태가 된다.

> Note that this link will not appear "pending" if a form is being submitted to the URL the link points to, because we only do this for "loading" states. The form will contain the pending UI for when the state is "submitting", once the action is complete, then the link will go pending.
