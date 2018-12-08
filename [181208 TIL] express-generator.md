# [181208 TIL] express-generator 이용한 서버 구동



express-generator 패키지를 통한 서버 구동 및 create-react-app과의 연동 테스트.

express-generator를 사용하면, 어플리케이션의 기본 구조를 자동으로 생성(서버 설정, 필수 미들웨어 및 라우트 정의), 실행만을 통한 서버 작동 가능.

```
$ express -h // 옵션 확인

Usage: express [options][dir] // 옵션 추가하여 사용 가능

Options:

 -h, --help output usage information
 -V, --version output the version number
 -e, --ejs add ejs engine support (defaults to jade)
 --hbs add handlebars engine support
 -H, --hogan add hogan.js engine support
 -c, --css add stylesheet support (less|stylus|compass|sass) (defaults to plain css)
 --git add .gitignore
 -f, --force force on non-empty directory
```



### express-generator 설치 방법

```
// test server

$ yarn global add express-generator
$ express test-server

// start the server
$ cd test-server
$ yarn install
$ yarn start
```



### create-react-app 설치 및 서버 연동 방법

```
// 'test-client'

$ yarn global add create-react-app
$ create-react-app test-client

$ cd test-client
$ yarn build

// test-server/app.js에서 PATH_NAME을 client 디렉토리 내부의 build 폴더 경로로 수정
app.use(express.static(path.join(__dirname, '../test-client/build')));

// serve the static files
$ cd test-server
$ yarn start

yarn run v1.12.3
warning ../package.json: No license field
$ node ./bin/www
GET / 200 55.270 ms - 2057
GET /static/css/main.7911d001.chunk.css 200 9.711 ms - 984
GET /static/js/main.bc253f8f.chunk.js 200 13.548 ms - 1342
GET /static/js/1.82dafdb5.chunk.js 200 15.711 ms - 111505
GET /static/media/logo.5d5d9eef.svg 200 2.187 ms - 2671
```

