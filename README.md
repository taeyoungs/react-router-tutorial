# React Router 6.4.2+ Tutorial

## 목차

- Handling Not Found Errors

## 정리

### Handling Not Found Errors

- 애플리케이션이 렌더링 하고 있을 때나 데이터를 로딩하고 있을 때 아니면 데이터를 가지고 `mutation`을 수행하고 있는 동안 에러가 발생할 때마다 **React Router**가 에러를 감지하고 에러 페이지를 렌더링한다.
- `useRouteError`는 발생한, 즉 **React Router**가 감지한 에러를 제공한다. 예를 들어, 사용자가 존재하지 않는 경로로 이동했을 때 `statusText` 속성에 **Not Found**가 담긴 `error response`를 전달받는다.
