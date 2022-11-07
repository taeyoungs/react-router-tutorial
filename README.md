# React Router 6.4.2+ Tutorial

## 목차

- Handling Not Found Errors

## 정리

### Handling Not Found Errors

- 애플리케이션이 렌더링 하고 있을 때나 데이터를 로딩하고 있을 때 아니면 데이터를 가지고 `mutation`을 수행하고 있는 동안 에러가 발생할 때마다 **React Router**가 에러를 감지하고 에러 페이지를 렌더링한다.
- `useRouteError`는 발생한, 즉 **React Router**가 감지한 에러를 제공한다. 예를 들어, 사용자가 존재하지 않는 경로로 이동했을 때 `statusText` 속성에 **Not Found**가 담긴 `error response`를 전달받는다.

### Nested Routes

- `children` 속성에 경로와 컴포넌트를 위치시키면 추후에 해당 경로와 일치하는 경우 상위 컴포컴포넌트에서 `Outlet` 컴포넌트를 이용하여 경로에 담긴 컴포넌트를 렌더링할 수 있다.

### Client Side Rendering

- **Single Page Application** 그리고 **Client Side Rendering**을 하고 있는 이상 경로를 이동할 때(브라우저 URL 탭에서 직접 입력 제외) 백엔드 서버에게 새로운 네트워크 요청을 발생시켜 전체 `Document`를 받아오는 것이 아니라 **React Router**가 내부 컨텐츠를 변경하도록 만들어줘야 한다.
- 따라서, `a` 태그 대신 `Link` 컴포넌트를 이용하여 애플리케이션이 **React Router**를 이용하여 새로운 UI를 렌더링하도록 만든다.
