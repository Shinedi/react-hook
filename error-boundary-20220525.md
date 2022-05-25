#### Error-Boundary 捕获边界错误
1. 全局的一个loading或者错误展示
```
import styled from "@emotion/styled";
import { Spin, Typography } from "antd";

const FullPage = styled.div`
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
`;

export const FullPageLoading = () => (
  <FullPage>
    <Spin size={"large"} />
  </FullPage>
);

export const FullPageErrorFallback = ({ error }: { error: Error | null }) => (
  <FullPage>
    <Typography.Text type="danger">{error?.message}</Typography.Text>
  </FullPage>
);
```

2. 错误边界
* 错误边界是 React 组件，它可以 在子组件树的任何位置捕获 JavaScript 错误，记录这些错误，并显示一个备用 UI ，而不是使整个组件树崩溃。 错误边界(Error Boundaries) 在渲染，生命周期方法以及整个组件树下的构造函数中捕获错误
* 错误边界 无法 捕获如下错误:  
事件处理 （了解更多）  
异步代码 （例如 setTimeout 或 requestAnimationFrame 回调函数）  
服务端渲染  
错误边界自身抛出来的错误 （而不是其子组件）
* 如果一个类组件定义了生命周期方法中的任何一个（或两个）static getDerivedStateFromError() 或 componentDidCatch()，那么它就成了一个错误边界。 使用static getDerivedStateFromError()在抛出错误后渲染回退UI。 使用 componentDidCatch() 来记录错误信息。
* 实现一个简版的error-boundary
```
import React, { ReactNode } from "react";

// children
// fallbackRender 渲染发生异常时要渲染的组件

type FallbackRender = (props: {error: Error | null}) => React.ReactElement
export class ErrorBoundary extends React.Component<{children: ReactNode, fallbackRender: FallbackRender}, {error: Error | null}> {
  state = {error: null};

  //当子组件抛出异常，这里会接收到并且调用
  static getDerivedStateFromError(error: Error) {
    return {error}
  }

  render() {
    const {error} = this.state;
    const {fallbackRender, children} = this.props
    if (error) return fallbackRender({error});
    return children;
  }
}
```
* 而后，可以像一个普通组件一样使用
```
... tsx
  <ErrorBoundary fallbackRender={FullPageErrorFallback}>
      {user ? <AuththenticatedApp /> : <UnauththenticatedApp/>}
  </ErrorBoundary>
```
