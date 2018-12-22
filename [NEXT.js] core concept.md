# Next.js framework



#### Why Next.js?

* SSR ( Server Side Rendering )

  ```text
  - Server Side Rendering
  - SEO(Search Engine Optimization: 검색 엔진 최적화)에 유리하다
  - View 초기 로딩 속도가 빠르다.
  - View를 서버에서 부터 렌더링하여 가져오기 때문에 서버에 부담을 준다.
  ```

* CSR ( Client Side Rendering )

  ```text
  - Client Side Rendering
  - SSR보다는 비교적 느리다.
  - HTML과 자바스크립트 등 각종 리소스를 다운 받은 후 브라우저에서 렌더링한다.
  ```

* 결론

  React JS 라이브러리를 핵심으로 한 NEXT JS 프레임워크를 이용하면, 처음 페이지를 요청할 때에는 서버 사이드 렌더링을 하고, 그 이후에는 내부에서 페이지가 이동될 때 데이터를 가져와서 페이지를 브라우저에서 렌더링함으로써 SSR과 CSR의 장점을 모두 취할 수 있다.

  Server rendering is very convenient in your first page load, for all the reasons we saw above, but when it comes to navigating inside the website, client-side rendering is key to speed up the page load and improve the user experience.



#### Main Features

- **Hot Code Reloading**: Next.js reloads the page when it detects any change saved to disk.

- **Automatic Routing**: any URL is mapped to the filesystem, to files put in the `pages`folder, and you don’t need any configuration (you have customization options of course).

- **Single File Components**: using [styled-jsx](https://github.com/zeit/styled-jsx), completely integrated as built by the same team, it’s trivial to add styles scoped to the component.

- **Server Rendering**: you can (optionally) render React components on the server side, before sending the HTML to the client.

- **Ecosystem Compatibility**: Next.js plays well with the rest of the JavaScript, Node and React ecosystem.

- **Automatic Code Splitting**: pages are rendered with just the libraries and JavaScript that they need, not more.

- **Prefetching**: the `Link` component, used to link together different pages, supports a `prefetch` prop which automatically prefetches page resources (including code missing due to code splitting) in the background.

- **Dynamic Components**: you can import JavaScript modules and React Components dynamically (<https://github.com/zeit/next.js#dynamic-import>).

- **Static Exports**: using the `next export` command, Next.js allows you to export a fully static site from your app.


#### How to start

```
$ mkdir [프로젝트 디렉토리]
$ cd [프로젝트 디렉토리]
$ yarn init -y
$ yarn add next react react-dom
$ mkdir pages

// 실행
$ yarn run dev
```

*** Hot Module Replacement (HMR)** 가 적용되어 있어 파일 수정시 페이지를 리로딩하지 않고 변화를 감지해 즉시 적용하여 브라우저가 자동으로 갱신

Hot Module Replacement (HMR) exchanges, adds, or removes modules while an application is running, without a full reload. This can significantly speed up development in a few ways



#### Pages Directory : SSR

Can create a page by exporting a React component, and putting that component inside the `pages` directory. Then it will have a fixed URL based on the file name.

Simply put, pages are inside a `pages` folder, and the page URL is determined by the page file name. The filesystem is the pages API.

In order to support client-side navigation, we need to use Next.js's Link API, which is exported via `next/link`.



#### Directory Structure

```text
├── .next             # 빌드 파일. next.config.js 파일 내에서 경로 지정 가능!
├── components        # React 컴포넌트 저장 디렉토리
│   ├── Header.js
│   ├── Footer.js
│   └── Layout.js
├── node_modules
├── pages             # 실질적으로 보여지는 페이지
				  		언더바(_)가 붙은 파일을 제외하고, 각각의 파일이 하나의 경로(route에 매핑되는 페이지)
│   ├── board.js      # '/board' 경로에 해당하는 페이지
│   └── index.js      # '/' 경로에 해당하는 페이지
├── package.json
```



#### Cliend-side Navagation

* `next/link` : 페이지 이동 시 사용

  cf) 기존 React JS `<Link to="연결할 페이지..">` 

  ​     Next JS에서는 `<Link href="이동할 페이지...>"` 

  ```javascript
  import Link from 'next/link'
  
  // 단순 링크
  <Link href="/about">
      <a>About Page</a>
  </Link>
  
  // parameter 전달
  <Link href={ pathname: '/about', query: { name: 'Zeit' } }>
      <a>About Page</a>
  </Link>
  ```

  * options

    1) replace: navigation 스택에 해당 경로를 새로 추가하지 않고 현재의 스택에 경로를 재 수정하여 링크 값에 대한 내용을 렌더링

    2) prefetch: 프리패치된 문서를 방문할 때, 브라우저 캐쉬를 이용해 빠른 페이지 로딩 가능

    3) passHref: 강제로 Href 링크를 하위 자식 노드에 노출

    4) as: `<Link as={page/${page.id}} href="이동할 페이지">` 브라우저 주소창에 보여질 이름을 alias 를 정해 사용 가능 [=> url masking]

  * features

    1) Hot reloading: A linked page loads immediately, without a page refresh.

    2) Client-side History Support

    This is client-side navigation working correctly, with complete support for the History API, which means your users back button won’t break.

    3) Styling Link

    The style prop on `next/link` has no effect. That's because `next/link`is just a higher order component(HOC) which only accepts the "href" and some similar props. If you need to style it, you need to do it to the underlying component.

    4) Any Components work

    Can place any of your custom React components or even a `div` within a Link. The only requirement for components placed inside a Link is they should accept an `onClick` prop.


* `next/router`

  * options
    * Router.route: 현재 경로 반환
    * Router.pathname: queryString 을 포함한 전체 경로 반환
    * Router.query: queryString이 파싱되어 저장된 객체로 빈 값은 {}로 반환
    * Router.push(url, as=url): 주어진 url 파라미터에 따라 pushState() 메서드를 호출
    * Router.replace(url, as=url): 주어진 url 파라미터에 따라 replaceStete() 메서드 호출

  * events
    * routeChangeStart(url): 라우팅이 시작될 때 호출
    * routeChangeComplete(url): 라우팅이 끝나면 호출
    * routeChangeError(err, url): 라우팅 도중 에러 발생 시 호출
    * beforeHistoryChange(url): 브라우저 내의 히스토리가 바뀌기 직전에 호출
    * appUpdated(nextRoute): 페이지가 업데이트 되었는 데 새 버전의 어플리케이션이 사용 가능한 경우 호출



#### Shared Components (template)

components 디렉토리에서 Header.js, Footer.js 등 공유할 컴포넌트를 만든 다음 Layout.js 하나의 모듈로 합쳐서 export.

to render the content we put inside the `Layout` element, use  `{props.children}`

```javascript
import Header from './Header'

const layoutStyle = {
  margin: 20,
  padding: 20,
  border: '1px solid #DDD'
}

const Layout = (props) => (
  <div style={layoutStyle}>
    <Header />
    {props.children}
  </div>
)

export default Layout
```

Can use components for styling, page layouts, and any other tasks you like. Additionally, can import components from NPM modules and use them.



#### Rendering child components

```javascript
// 1)
export default () => (
    <Layout>
       <p>This is the about page</p>
    </Layout>
)

// 2)
import withLayout from '../lib/layout'

const Page = () => (
  <p>This is the about page</p>
)

export default withLayout(Page)

// 3)
const Page = () => (
  <p>This is the about page</p>
)

export default () => (<Layout page={Page}/>)

// 4) 
const content = (<p>This is the about page</p>)

export default () => (<Layout content={content}/>)
```



#### Dynamic pages with query strings

Display some data based on some parameters.

```javascript
import Layout from '../components/MyLayout.js'
import Link from 'next/link'

const PostLink = (props) => (
  <li>
    <Link href={`/post?title=${props.title}`}>
      <a>{props.title}</a>
    </Link>
  </li>
)

export default () => (
  <Layout>
    <h1>My Blog</h1>
    <ul>
      <PostLink title="Hello Next.js"/>
      <PostLink title="Learn Next.js is awesome"/>
      <PostLink title="Deploy apps with Zeit"/>
    </ul>
  </Layout>
)
```

```javascript
import {withRouter} from 'next/router'
import Layout from '../components/MyLayout.js'

const Content = withRouter((props) => (
  <div>
    <h1>{props.router.query.title}</h1>
    <p>This is the blog post content.</p>
  </div>
))

const Page = (props) => (
    <Layout>
       <Content />
    </Layout>
)

export default Page
```

- We import and use the "withRouter" function from "next/router" this will inject the Next.js router as a property
- In this case, we are using the router's “query” object, which has the query string params.
- Therefore, we get the title with `props.router.query.title`.



#### Route Masking

Will show a different URL on the browser than the actual URL that your app sees.

* Query string  형태의 url 주소를 가독성 좋은 API 패턴의 url로 표현 ---> element에서 'as' 속성 사용하여 지정 가능

```javascript
import Layout from '../components/MyLayout.js'
import Link from 'next/link'

const PostLink = (props) => (
  <li>
    <Link as={`/p/${props.id}`} href={`/post?title=${props.title}`}>
      <a>{props.title}</a>
    </Link>
  </li>
)

export default () => (
  <Layout>
    <h1>My Blog</h1>
    <ul>
      <PostLink id="hello-nextjs" title="Hello Next.js"/>
      <PostLink id="learn-nextjs" title="Learn Next.js is awesome"/>
      <PostLink id="deploy-nextjs" title="Deploy apps with Zeit"/>
    </ul>
  </Layout>
)
```

* refresh 또는 hard-reloading 시 404 에러가 발생 (there is no such page to load on the server)

  ---> 서버 API 커스텀 후 경로 맵핑

  [참고] http://webframeworks.kr/tutorials/nextjs/nextjs-004/



#### Custom Server API

```javascript
$ npm install --save express

const express = require('express')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare()
.then(() => {
  const server = express()

  server.get('/p/:id', (req, res) => {
    const actualPage = '/post'
    const queryParams = { title: req.params.id } 
    app.render(req, res, actualPage, queryParams)
  })

  server.get('*', (req, res) => {
    return handle(req, res)
  })

  server.listen(3000, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
})
.catch((ex) => {
  console.error(ex.stack)
  process.exit(1)
})
```



#### Universal Data Fetching

```javascript
$ npm install --save isomorphic-unfetch // browser fetch API

import Layout from '../components/MyLayout.js'
import fetch from 'isomorphic-unfetch'

const Post = (props) => (
    <Layout>
       <h1>{props.show.name}</h1>
       <p>{props.show.summary.replace(/<[/]?p>/g, '')}</p>
       <img src={props.show.image.medium}/>
    </Layout>
)

Post.getInitialProps = async function (context) {
  const { id } = context.query
  const res = await fetch(`https://api.tvmaze.com/shows/${id}`)
  const show = await res.json()

  return { show }
}

export default Post
```



#### Styled-jsx

Styles should go inside template strings. Styled jsx works as a babel plugin. It will parse all of the CSS and apply it in the build process. (With that our styles get applied without any overhead time)

```javascript
export default () => (
  <div>
    <p>
      <a href="mailto:my@email.com">Contact us!</a>
    </p>
    <style jsx>{`
      p {
        font-family: 'Courier New';
      }
      a {
        text-decoration: none;
        color: black;
      }
      a:hover {
        opacity: 0.8;
      }
    `}</style>
  </div>
)

```

CSS rules are scoped.

Can also edit global styles adding `global` to the `style` element.



#### Deploy

As you can see, you need to only build your app once. Then you can start it on as many ports as you wish.

```
"scripts": {
  "build": "next build",
  "start": "next start -p $PORT"
}

$ npm build

$ PORT=8000 npm start
$ PORT=9000 npm start
```

ZEIT Now will always use 443

Even if you start your app on port 8000, once deployed to now, you can access it with port 443 (the default port for "https" websites).

That's a feature of ▲ZEIT Now. You only need to start your app on any port you like elsewhere. ▲ZEIT Now will map it to port 443 always.



#### Typescript Integration

https://itnext.io/next-js-awesome-typescript-integration-d05cfe9af057



#### Reference

https://flaviocopes.com/nextjs/

https://nextjs.org/docs/