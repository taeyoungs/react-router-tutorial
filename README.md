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
  - [Deleting Records](#deleting-records)
  - [Contextual Errors](#contextual-errors)
  - [Index Routes](#index-routes)
  - [Cancel Button](#cancel-button)
  - [GET Submissions with Client Side Rendering](#get-submissions-with-client-side-rendering)
  - [Synchronizing URLs to Form State](#synchronizing-urls-to-form-state)
  - [Submitting Forms `onChange`](#submitting-forms-onchange)
    - [`useSubmit`](#usesubmit)
  - [Adding Search Spinner](#adding-search-spinner)
  - [Managing the History Stack](#managing-the-history-stack)
    - [Use `replace` in `submit`](#use-replace-in-submit)
  - [Mutation Without Navigation](#mutation-without-navigation)
    - [useFetcher](#usefetcher)
    - [`fetcher.state`](#fetcherstate)
    - [`fetcher.Form`](#fetcherform)
    - [`fetcher.load()`](#fetcherload)
    - [`fetcher.submit()`](#fetchersubmit)
    - [`fetcher.data`](#fetcherdata)
    - [`fetcher.formData`](#fetcherformdata)
    - [`fetcher.formAction` & `fetcher.formMethod`](#fetcherformaction--fetcherformmethod)
    - [Index Query Param](#index-query-param)
  - [Not Found Data](#not-found-data)

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

## Deleting Records

- `<Link to>`, `<Form action>`에다가 전체 경로가 아닌 단순히 value 값만 집어 넣는다면 이는 상대 경로로써 동작한다.
  - 예를 들어, 현재 위치가 `contact/:contactId`이고 `<Form action="destory">`라면 `form`은 `contact/:contactId/destory`으로 `submit` 된다.
- 위와 같은 예일 때 삭제 버튼이 동작하게 만들기 위해서 다음과 같은 요소들이 필요하다.
  - 새로운 `route`
  - `route`에 연결된 `action`
  - `src/contacts.js`의 `deleteContact`
- 삭제 버튼을 누를 경우 다음과 같은 흐름으로 진행된다.

  1. `<Form>`은 브라우저가 기본적으로 **POST** 요청을 만들어서 서버에게 보내는 행동을 막는다.
  2. 막았던 요청은 `<Form>`이 가진 속성과 일치하는 경로로 보내진다.
  3. `redirect` 되고 나서 **React Router**는 최신 데이터를 가져오기 위하여(이를 `revalidation`이라 한다) 페이지의 데이터에 대한 모든 `loader`를 다시 호출한다.
  4. `useLoaderData`는 새로운 값을 반환하고 컴포넌트 리렌더링을 발생시킨다.

## Contextual Errors

- `action` 또는 `loader`에서 발생한 에러는 가장 가까운 `errorElement`까지 버블링된다.

## Index Routes

- `{ path: '' }` 대신 `{ index: true }` 속성을 전달하면 **Router**에게 사용자가 현재 머무르고 있는 경로가 부모 `route`의 경로와 정확히 일치할 경우 `{ index: true }`가 전달되어 있는 컴포넌트를 `Outlet`에 전달하도록 만들 수 있다.
  - `{ index: true }`인 경우는 자식 `route` 중 어느 `route`도 부모 `route`와 일치하지 않는 상황이기 때문에 `{ index: true }`가 표시된 컴포넌트만 `Outlet`에 전달된다.

## Cancel Button

- `<button type="button">`은 겉보기에는 중복되어 보이지만 해당 버튼이 `form`을 `submit` 하지 못하도록 하는 HTML 방식이다.

## GET Submissions with Client Side Rendering

- **JavaScript**가 없다면 `form`에서 **GET** 요청을 발생시켰을 때 `Request` `body`에 `formData`를 담는 **POST** 요청과는 달리 **GET** `Request`의 `URLSearchParams`에 `formData`가 `key=value` 형태로 담긴다.
  - ex. `http://127.0.0.1:5173/?q=ryan`
- 여기서 만약 **React Router**를 사용한다고 하면 서버에게 **GET** 요청을 보내는 대신 해당 `Route`의 `loader`가 호출된다.
  - `action`이 아니고 `loader`가 호출되는 이유는 **POST** 요청이 아닌 **GET** 요청이기 때문이다. **GET** 요청은 `Link`를 클릭하는 것과 동일하다.

## Synchronizing URLs to Form State

현재 다음과 같은 **UX** 이슈가 있다.

1. 브라우저의 뒤로 가기 버튼을 눌렀을 때 검색 목록이 더 이상 보이지 않는 상황 임에도 불구하고 `form` 내 `input` 태그에 입력했었던 값이 계속해서 표시되는 이슈
   - `useEffect`를 활용하여 검색했던 값(`q`)가 변할 때 `input` 태그의 값이 다시 설정되도록 만들면 된다.
   - **React** `State`를 통한 `controlled component`를 사용하고 싶다는 생각이 들 수도 있다.
     - 구현은 가능하지만 동일한 동작임에도 불구하고 코드는 더 복잡해진다.
     - **URL** 자체를 통제할 순 없기에 `controlled component`를 사용하고 싶다면 더 많은 동기화 포인트가 필요하다.
2. 검색을 마친 후 새로고침을 눌렀을 때 `form` 내 `input` 태그에 검색했던 값이 사라지는 이슈
   - `loader`에서 `contact` 뿐만 아니라 검색했던 값(`q`)까지 같이 반환해준다.
   - `useLoaderData`로 검색했던 값을 얻어내서 이를 `form` 내 `input` 태그의 `defaultValue`에 전달한다.
   - 이제 페이지를 새로고침해도 `URLSearchParams`에 값은 존재하기 때문에 이를 이용해 검색했던 값을 `input` 태그에 유지하는 것이 가능해진다.

## Submitting Forms `onChange`

### `useSubmit`

`<Form>`의 명령형 버전으로 프로그래머가 사용자 대신 `form`을 `submit` 할 수 있도록 만들어 준다.

- 첫 번째 인수는 form, form 내에 input 요소 그리고 formData 등 다양한 값들을 넘길 수 있다.
  - null도 가능하다. ex. ` submit(null, { method: "post", action: "/logout" });`
- 두 번째 인수는 form 태그의 속성들이다.
  - `action`, ex. `/logout`
  - `method`, ex. `post`, `get`

## Adding Search Spinner

- `useNavigation`으로부터 반환되는 객체는 `loader`가 호출되고, 즉 `navigation.state`가 `loading`일 때는 의미 있는 데이터(`undefined가` 아님)를 반환해주고 `loader`의 실행이 완료되어 `idle`로 전환됐을 때는 `navigation.state`를 제외한 나머지 값들은 `undefined`로 처리된다.

## Managing the History Stack

```jsx
<input
  id="q"
  aria-label="Search contacts"
  placeholder="Search"
  type="search"
  name="q"
  defaultValue={q}
  onChange={(event) => submit(event.currentTarget.form)}
/>
```

위와 같이 코드를 작성하여 사용자가 검색을 할 수 있게 만들었을 때의 문제점은 글자를 1개 입력할 때마다 **history stack**이 쌓인다는 것이다. `abcd`를 검색하는데 **history stack**에 `a`, `ab`, `abc`, `abcd`와 같이 쌓여서 뒤로 가기를 누를 때마다 이를 거슬러 올라가는 건 사용자도 원하지 않을 것이다.

### Use `replace` in `submit`

따라서, 아래와 같이 `submit` 메서드에 `replace` 속성을 전달하여 **history stack**에 경로가 계속해서 쌓이는 대신 마지막에 쌓인 경로가 대체되는 방식으로 변경한다.

```jsx
<input
  id="q"
  aria-label="Search contacts"
  placeholder="Search"
  type="search"
  name="q"
  defaultValue={q}
  onChange={(event) => {
    const isFirstSearch = q === null;
    submit(event.currentTarget.form, {
      replace: !isFirstSearch,
    });
  }}
/>
```

## Mutation Without Navigation

튜토리얼에서 지금까지 사용했던 `mutation`(데이터를 변경하는)은 `navigate`가 발생하는 `form` 사용하여 **history stack**에 새로운 `entry`를 생성했다. 이러한 사용자 플로우는 일반적인 플로우가 맞긴 하지만 `navigate`가 발생하는 것 없이도 데이터를 변경하는 플로우, 즉 `navigation` 없이 `mutation`을 수행하는 플로우도 일반적인 플로우 중 하나다.

이러한 케이스들을 위해 `useFetcher` 훅을 제공한다. 이 훅은 `navigation` 발생 없이 `loader` 그리고 `action`과 소통할 수 있게 해준다.

### useFetcher

**HTML**/**HTTP**에서 데이터 `mutation`과 `load`(or `fetching`)는 `navigation`과 함께 모델링된다. 브라우저에서 `navigation`을 발생시키는 건 `<a href>`와 <form action> 태그이고 **React Router**에서 이 두 태그는 <Link>와 <Form>에 해당한다.

하지만 때로는 `navigation`을 하지 않고 `loader`를 호출하고 싶고 URL을 변경하지 않고도 `action`을 호출하여 `revalidate`할 페이지의 최신 데이터를 가져오고 싶기도 하다. 아니면 동시에 여러 `mutation`을 수행하고 싶을 수도 있다.

서버와의 많은 인터렉션에 `navigation` 이벤트는 적합하지 않다. `useFetcher` 훅을 사용하면 `navigation` 없이도 UI를 `loader`나 `action`에 연결할 수 있다.

이 훅은 다음과 같은 상황에 유용하다.

- fetch data not associated with UI routes (popovers, dynamic forms, etc.)
- navigation 없이 action에 데이터를 submit하는 경우 (뉴스레터 회원 가입과 같은 공유 컴포넌트)
- 리스트에서 동시다발적인 submission을 처리해야 하는 경우 (typical "todo app" list where you can click multiple buttons and all should be pending at the same time)
- infinite scroll containers
- 등등

**Fetcher**엔 여러 가지 내장된 빌트인 동작들이 존재한다.

- 데이터 패칭이 중단되면 자동으로 취소 처리 한다.
  > Automatically handles cancellation on interruptions of the fetch
- **POST**, **PUT**, **PATCH**, **DELETE로** `submit` 됐을 때 `action`이 제일 먼저 호출된다.
  - `action`의 실행이 끝나면 페이지의 데이터가 `revalidate` 되어 발생할 수 있는 모든 `mutation`을 캡처하여 UI를 서버 상태와 자동으로 동기화한다.
- 다수의 `fetcher`가 한번에 실행됐을 때는
  - 완료되는 `fetcher` 마다 해당 `fetcher`의 최신 그리고 사용 가능한 데이터를 `commit`한다.
  - 응답이 반환되는 순서에 상관없이 **stale load**가 최신 데이터를 덮어쓰지 않도록 한다.
- `<Link>`나 `<Form>`을 통한 일반적인 `navigation`과 같이 가장 가까운 `errorElement`을 렌더링하여 캐치되지 않은 오류들을 처리한다.
- `<Link>`나 `<Form>`을 통한 일반적인 `navigation`과 같이 `action` 이나 `loader`가 `redirect`를 반환하는 경우 애플리케이션은 `redirect` 된다.

### `fetcher.state`

- `idle`
  - 패칭이 발생하지 않은 상태
- `submitting`
  - **POST**, **PUT**, **PATCH**, 또는 **DELETE**를 사용하여 `fetcher`가 `submission` 하기 위해 `action`을 호출한 상태
- `loading`
  - `fetcher`가 `fetcher.load`를 사용하여 `loader`를 호출한 상태
  - 별도의 `submission` 후에 `revalidate` 된 상태
  - `useRevalidator` 훅을 호출한 상태

### `fetcher.Form`

navigation을 발생시키지 않는 점만 제외하면 <Form>과 동일하다.

```jsx
function SomeComponent() {
  const fetcher = useFetcher();
  return (
    <fetcher.Form method="post" action="/some/route">
      <input type="text" />
    </fetcher.Form>
  );
}
```

### `fetcher.load()`

route loader를 통한 데이터 로딩, ex. `fetcher.load("/some/route");`

예시와 같이 **URL**이 다수의 경로가 중첩되어 모두 일치할 수도 있겠지만(예를 들어, `/some`, `/some/route` 두 경로 모두 일치하는 것처럼) `fetcher.load()`는 오직 leaf와 일치하는(예시의 경우 `/some`을 제외하고 `/some/route`만) `loader`만 호출할 것이다. (or parent of index routes).

클릭 핸들러에서 `fetcher.load()`를 사용하는 경우 `fetcher.Form`를 대신 사용하여 코드를 조금 더 간단하게 만들 수도 있다.

> ✋ **주의**
> 페이지 내에 존재하는 모든 실행 가능한 `fetcher.load()`는 revalidation의 일부로, 즉 해당 fetcher와 관련된 revalidation이 실행되면 `fetcher.load()`도 재실행된다. (**navigation submission**, **fetcher submission**, 또는 `useRevalidator()` 호출 후)

### `fetcher.submit()`

`<fetcher.Form>`의 명령형 버전, 사용자와의 상호 작용을 통해 패칭을 실행시키고 싶다면 `<fetcher.Form>`을 사용하면 되지만 사용자가 버튼을 클릭하는 것과 같은 상호 작용을 통해 패칭을 시작하고 싶지 않고 프로그래머가 원하는 시점에 패칭을 시작하고 싶다면 `fetcher.submit()`을 사용하면 된다.

### `fetcher.data`

loader나 action에서 반환된 데이터가 저장되는 장소, 데이터가 한번 저장되고 나면 reload 되거나 resubmission 될지라도 데이터를 가지고 있다.

### `fetcher.formData`

`<fetcher.Form>` 또는 `fetcher.submit()`를 사용하고 있는 경우에 낙관적 UI를 빌드할 수 있게 도와주는, 즉 `formData`가 담겨있는 속성이다.

네트워크 요청이 발생하고 이에 대한 응답이 돌아오기 전까지 `fetcher.formData`를 사용할 수 있다. 이 말인 즉슨 요청에 대한 응답이 도착하는 순간 `fetcher.formData`는 더 이상 사용할 수 없으며 `revalidation`을 이루어진 응답 데이터를 활용해서 UI를 업데이트 해야 한다.

### `fetcher.formAction` & `fetcher.formMethod`

```jsx
<fetcher.Form action="/mark-as-read" method="post" />;

// when the form is submitting
fetcher.formAction; // "mark-as-read"

// when the form is submitting
fetcher.formMethod; // "post"
```

### Index Query Param

```jsx
createBrowserRouter([
  {
    path: '/projects',
    element: <ProjectsLayout />,
    action: ProjectsLayout.action,
    children: [
      {
        index: true,
        element: <ProjectsIndex />,
        action: ProjectsPage.action,
      },
    ],
  },
]);

<Form method="post" action="/projects" />;
<Form method="post" action="/projects?index" />;
```

위와 같이 경로는 같지만 중첩된 자식 `Route`를 구별하고 싶다면 `?index`를 **Query String Parameter**로 사용하면 된다.

## Not Found Data

데이터를 가져왔는데 해당 데이터가 없는 상황(`null`)이라면? `null`을 렌더링하려고 하는 상황에 발생한 에러는 가장 가까운 `errorElement`를 통해서 캐치되기 때문에 현재 코드 상에서는 최상단 `route`가 가지고 있는 `errorElement`에 의해서 에러가 처리된다.

현재 상태로 놔둘 수도 있겠지만 에러 처리 방법을 더 상세화할 수도 있다.

`loader`나 `action`에서 "데이터가 존재하지 않습니다"와 같은 예상 가능한 에러가 발생할 때마다 이에 맞춰 `throw` 할 수 있다. **call stack**을 끊고 이를 **React Router**가 캐치해서 특정 에러 경로가 대신 렌더링되게 할 수 있다. 이렇게 하면 `null`을 렌더링하려는 시도 자체를 하지 않아도 된다.

```jsx
export async function loader({ params }) {
  const contact = await getContact(params.contactId);
  if (!contact) {
    throw new Response('', {
      status: 404,
      statusText: 'Not Found',
    });
  }
  return contact;
}
```

이렇게 하면 `null`을 렌더링하는 시도도 피할 수 있고 사용자에게 더 명확한 에러 사항을 명시해줄 수도 있다.
