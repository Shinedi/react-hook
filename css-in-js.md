#### css-in-js
1. antd的颜色风格是可以进行统一修改的  
  a. 使用create-react-app创建的项目，可以通过安装`yarn add @craco/craco`，根目录创建`craco.config.js`文件来进行配置，具体可以参考<a href="https://ant.design/docs/react/use-with-create-react-app-cn">antd文档</a>  
2. 扩展学习-<a href="https://coding.imooc.com/lesson/482.html#mid=41843">css in js</a>  
3. css-in-js  
<ul>
  <li>
    <div>/src/index.tsx中引入App.tsx,App.tsx文件引入App.css,App.css是负责全局样式的文件</div>
    
    html {
      /* rem em */
      /* em 相对于父元素的font-size */
      /* rem 相对于根元素的font-size  r就是root的意思 */
      /* 16 * 62.5% = 10px */
      /* 1rem === 10px */
      font-size: 62.5%;
    }

    html body #root .App {
      min-height: 100vh;
    }
  </li>
  <li>
    <div>安装emotion: yarn add @emotion/react @emotion/styled</div>
  </li>
  <li>
    <div>引入styled, 可以参考以下代码片段，</div>

    import styled from "@emotion/styled";
    
  </li>
</ul>

```
import { useState } from "react";
import { LoginScreen } from "./login";
import { RegisterScreen } from "./register";
import {Button, Card, Divider} from 'antd';
import styled from "@emotion/styled";
import logo from 'assets/logo.svg';
import left from 'assets/left.svg';
import right from 'assets/right.svg';

export const UnauththenticatedApp = () => {
  const [isRegister, setIsRegister] = useState(false);
  return <Container>
    <Background />
    <Header />
    <ShadowCard>
      <Title>{isRegister ? '请注册' : '请登录'}</Title>
      {isRegister ? <RegisterScreen /> : <LoginScreen />}
      <Divider></Divider>
      <a onClick={() => setIsRegister(!isRegister)}>{isRegister ? '已经有账号了？直接登录' : '没有账号？直接注册新账号'}</a>
    </ShadowCard>
  </Container>
};

export const LongButton = styled(Button)`
  width: 100%;
`

const Title = styled.h2`
  margin-bottom: 2.4rem;
  color: rgb(94, 108, 92);
`

const Background = styled.div`
  position: absolute;
  width: 100%;
  height: 100%;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: left bottom, right bottom;
  background-size: calc((100vw - 40rem)/2 - 3.2rem), calc((100vw - 40rem)/2 - 3.2rem), cover;
  background-image: url(${left}), url(${right});
`

const Header = styled.header`
  background: url(${logo}) no-repeat center;
  padding: 5rem 0;
  background-size: 8rem;
  width: 100%;
`

const ShadowCard = styled(Card)`
  width: 40rem;
  min-height: 56rem;
  padding: 3.2rem 4rem;
  border-radius: 0.3rem;
  box-sizing: border-box;
  box-shadow: rgba(0, 0, 0, 0.1);
  text-align: center;
`

const Container = styled.div`
display: flex;
flex-direction: column;
align-items: center;
min-height: 100vh;
`
```

4. grid和flex的区别  
a.要考虑是一维布局 还是 二维布局： 一维布局用flex,二维布局用grid  
b.是从内容出发还是从布局出发？  
从内容出发：你先有一组内容（数量不固定），然后希望他们均匀的分布在容器中，由内容自己的大小决定占据的空间   
从布局出发：先规划网格数量（数量一般比较固定），然后再把元素往里填充   
从内容出发用flex,从布局出发用grid

5. 示例
*  grid布局，参考以下代码
```
  display: grid;
  grid-template-rows: 6rem 1fr 6rem; // 每行的高度
  grid-template-columns: 6rem 1fr 6rem; // 每列的宽度，1fr代表自适应
  grid-template-areas:  // 布局
  "header header header"
  "nav main aside"
  "footer footer footer"
  ;
```
* 页面中经常会有一行中所有元素垂直居中，每个有一定间距，所以可以写个通用的组件，例如下面代码中的Row(styled中也支持传入参数)
```
import { useAuth } from "context/auth-context";
import React from "react";
import styled from "@emotion/styled";
import { ProjectListScreen } from "screens/project-list";
import { Row } from './components/lib';

/*

grid和flex区别
1. 要考虑，是一维布局 还是 二维布局
一般来说， 一维布局用flex, 二维布局用grid
2. 是从内容出发还是布局出发
从布局出发: 先规划网格，然后再把元素往里面填充 使用grid
*/

export const AuththenticatedApp = () => {
  const {logout} = useAuth();
  return <Container>
    <Header between={true}>
      <HeaderLeft gap={true}>
        <h2>logo</h2>
        <h2>项目</h2>
        <h2>用户</h2>
      </HeaderLeft>
      <HeaderRight>
        <button onClick={() => logout()}>登出</button>
      </HeaderRight>
    </Header>
    <Main>
      <ProjectListScreen />
    </Main>
  </Container>
};


const Container = styled.div`
  display: grid;
  grid-template-rows: 6rem 1fr 6rem; // 每行的高度
  grid-template-columns: 6rem 1fr 6rem; // 每列的宽度，1fr代表自适应
  grid-template-areas:  // 布局
  "header header header"
  "nav main aside"
  "footer footer footer"
  ;
  height: 100vh;

`
const Header = styled(Row)`
  grid-area: header; // grid模块名称
`
const HeaderLeft = styled(Row)``
const HeaderRight = styled.div``
const Main = styled.main`
  grid-area: main;
`
```
* lib.tsx
```
import styled from "@emotion/styled";

export const Row = styled.div<{
  gap?: number | boolean,
  between?: boolean,
  marginBottom?: number
}>`
display: flex;
align-items: center;
justify-content: ${props => props.between ? 'space-between' : undefined };
margin-bottom: ${props => props.marginBottom + 'rem'};
> * {  // 防止有些元素写了margin-top或者margin-bottom,导致不能垂直居中
  margin-top: 0;
  margin-bottom: 0;
  margin-right: ${props => typeof props?.gap === 'number' ? props.gap + 'rem' : props.gap ? '2rem' : undefined};
}
`
```
6. svg可以以svg形式来渲染，这样可以设置样式，具体实现方式如下：引入svg作为react组件
```
import {ReactComponent as SoftWareLogo} from './assets/software-logo.svg';

export const A = () => {
  return <>
    <SoftWareLogo width={'18rem'} color={'rgb(38, 152, 255)'} />
  </>;
}
```
