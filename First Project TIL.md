# First Project TIL



## [181208 TIL] express-generator 이용한 서버 구동

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



#### express-generator 설치 방법

```
// test server

$ yarn global add express-generator
$ express test-server

// start the server
$ cd test-server
$ yarn install
$ yarn start
```



#### create-react-app 설치 및 서버 연동 방법

```javascript
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





## [181210 TIL] MongoDB + mongoose 공부 

Udemy에서 'Node.js: The Complete Guide to Build RESTful APIs' 강좌 들으면서 공부.

* MongoDB는 NoSQL로, relational database의 tables & rows 가 MongoDB에서는 collections & documents로 대치되며, 각 document는 key-value pair 형태를 지닌다.
* Mongoose라는 ODM 라이브러리의 schema를 이용하여 MongoDB collection의 documents를 정의할 수 있다.
* MongoDB & mongoose를 이용한 CRUD operations 및 Data validation 연습.
* Modelling Relationships 
  * 방법 1) Using References (Normalization) ---> CONSISTENCY
  * 방법 2) Using Embedded Documents (Denormalization) ---> PERFORMANCE
* Authentication & Authorization 공부.





## [181211 TIL]

* server-side 폴더 구조 정리 : models, routes, middleware, client(static file)

* mongoose 사용하여 DB schema 설계

* server-client repository 연결 테스트

  * `git submodule` 커맨드 사용하여 client repository를 server repository의 submodule로 clone.

    ```
    $ git submodule add REMOTE_REPOSITORY_URL client
    ```

  * server repository에서 build한 static 파일 serve.

    ```javascript
    // 'app.js'에서 수정 및 추가
    
    // Serve static files from the React frontend app
    app.use(express.static(path.join(__dirname, 'client/build')))
    // Anything that doesn't match the above, send back index.html
    app.get('*', (req, res) => {
      res.sendFile(path.join(__dirname + '/client/build/index.html'))
    })
    ```

  *  각자 repo에서 개발 후, `git submodule update` 커맨드로 병합 가능

    ```
    // 각자 local repository에서 submodule 초기화
    $ git submodule init
    
    // client-side와 sync
    $ git submodule update 
    ```

  * client side에 proxy 설정을 통해 server-side의 5000번 port와 client-side의 3000번 port 둘 다 사용 가능.

    ```javascript
    // 'client/build/package.json'에서 추가
    "proxy": "http://localhost:5000"
    ```





## [181212 TIL] MVC pattern

express-generator 패키지를 사용하면 자동적으로 MVC 패턴 구조 생성 - models, public(View), routes(Controller) 

* MVC란?

  Model View Controller의 약자로, 에플리케이션을 세가지의 역할로 구분한 소프트웨어 아키텍쳐 디자인 패턴.

  Controller의 조작을 통해 Controller는 Model을 통해서 데이터를 가져오고, 그 데이터를 바탕으로 시각적인 표현을 담당하는 View를 제어하여 클라이언트에게 전달. 

  * Controller

    사용자가 접근 한 URL에 따라서 사용자의 요청사항을 파악한 후에 그 요청에 맞는 데이터를 Model에 의뢰하고, 데이터를 View에 반영해서 사용자에게 알려준다. Model과 View 사이에서 중개자 역할을 하며, Model과 View는 직접적으로 소통하지 않는다.

  * Model

    일반적으로 데이터베이스 테이블에 대응. 

  * View

    클라이언트 측의 html/css/javascript들을 모아둔 컨테이너. 





## [181213 TIL] MySQL Migration

서로 관계가 없는 데이터란 없다. 또한 mongoose 라이브러리를 사용하여 MongoDB를 만지다 보니, 결론적으로는 유연성이라는 MongoDB만의 장점을 느낄 수도 없었다.

따라서 최종적으로 MySQL로 database 변경. DB Schema 및 route별 필요한 query문 작성.





## [181214 TIL] RESTful API

REST API 작성.

* API란?

  * 서버가 정의한 인터페이스

  * HTTP requests를 사용하여 리소스에 접근하기 위해 서버와 상호작용(interact) 하는 방법을 서술해 둔 일련의 규칙

* REST : API 디자인에 있어서 HTTP 프로토콜을 의도에 맞게 사용하도록 정의된 아키텍쳐 스타일

  * URI는 정보의 자원(resource)을 표현해야 한다 --> 명사형 사용

  * 자원에 대한 행위(verbs)는 HTTP Methods로 표현해야 한다

* HTTP Request Methods (CRUD)

  * POST : Creates/adds a new resource, 데이터를 서버로 제출하는 용도로 사용하며, 서버 상태의 변화를 일으킴

  * GET : Reads/retrieves a resource, 특정 리소스를 가져오도록 요청 

  * PUT : Updates a resource, POST와 비슷하나, 연속적인 요청시에도 같은 효과를 가져옴. 기존 데이터를 교체하는 용도로 쓰일 수 있음

  * DELETE : Deletes a resource, 리소스 삭제를 요청할 때 사용



## [181215 TIL] ORM - Sequelize

sequelize 공부 및 refactoring.

* ORM (Object-Relational Mapping) 

  OOP 관점에서 데이터베이스(RDMBS)에 저장될 데이터(record) 또한 하나의 객체이며, 이 객체와 테이블 간의 관계 설정을 통해 DB 테이블 내의 데이터를 객체로 전달받아 사용 가능. 따라서 이와 같은 객체 지향적인 코드로 인해 절차적이며 순차적인 접근이 요구되는 SQL보다 더 직관적, 높은 생산성. 각 객체에 대한 코드를 독립적으로 작성하기 때문에, 해당 객체들의 재사용 및 유지보수 편의성이 증가하며 MVC 디자인 패턴에도 유리. 또한 ORM 솔루션은 DB에 종속적이지 않기 때문에, DBMS 교체 작업에도 비교적 적은 리스크와 시간 소요.

* MySQL & sequelize를 이용한 CRUD operations 및 Data validation 실습.

* MVC 프레임워크를 기반으로 한 express.js에서의 sequelize 연동 방법 - 모델 정의 및 모듈 불러오기

  [ 참고 ] https://sequelize.readthedocs.io/en/1.7.0/articles/express/





## [181217 TIL] Authentication

Authentication middleware 사용하여, 로그인하여 세션 인증된 유저만 포스트 작성할 수 있게 구현.

* Session vs. Cookie

  세션은 서버에서, 쿠키는 클라이언트에서 갖고 있음.
  세션은 메모리에 저장되어 컴퓨터를 끄면 세션이 destroyed되어 사라지지만, 쿠키는 하드드라이브에 저장되어 컴퓨터를 끄더라도 계속 유지되어 HTTP 요청시마다 헤더에 쿠키를 포함시켜서 요청함으로써 사용자 인증 가능.

* JWT

  JWT도 유사한 개념으로, 토큰에 만료기간 및 signature가 포함되어 있어 좀 더 보안성이 높으며, http 헤더 또는 url의 파라미터로 전달할 수 있어, 유저가 요청했을 때에만 토큰 검증을 하면 되므로 세션 관리가 필요없어 서버를 stateless 하게 관리할 수 있다. [참고] https://velopert.com/2389





## [181218-19 TIL] AWS Deployment

AWS deployment flow

- Install Node.js and MySQL server on the EC2 instance

  * EC2 인스턴스 생성 시 부여받는 SSH key 관리 잘 해야함.

    `mv <ssh key name> ~/.ssh`  // .ssh 폴더에서 별도 관리

    `cd !$`

    `cd ~/.ssh`

    `cdmod 400 <ssh key name>`  // ssh에 대한 접근 권한 변경

    `ls -al`  // 변경사항 확인

  * PM2 : 개발 시 nodemon 사용, 배포 시 pm2 사용

    서버가 에러로 인해 꺼졌을 때 서버를 다시 켜주는 기능.

    멀티 프로세싱 -> 하나의 프로세스가 받는 부하가 적어져 서비스 원할하게 운영 가능,

    하지만 서버의 메모리 같은 자원 공유 X (세션 공유 X) -> Redis 사용하여 세션 공유

    [참고] EACCES, permission denied 에러시 `sudo chmod -R 777 .pm2`

  * MySQL 서버 환경설정 변경 

    `sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf` -> bind-address 0.0.0.0 으로 변경

    [참고] https://www.cyberciti.biz/faq/howto-install-mysql-on-ubuntu-linux-16-04/

    서버 터미널에서 MySQL 설치 및 사용자 권한 설정

    [참고] https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04

- clone the code for my server

- install all the node dependencies

  `curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -`

  `sudo apt-get install -y nodejs`

  `npm i`

- start the server app.js

  `pm2 start app.js`

  [참고] cluster mode 사용시`pm2 start app.js -i 0`

주의 사항

* .gitingnore, .env 파일을 이용하여 보안 주의 (.env 파일은 app.js에서 `require('dotenv').config();` 실행시켜 줘야함)

* bcrypt uninstall -> Ubuntu용으로 다시 install -> `sudo apt-get install build-essential` 

  `npm install --save bcryptjs && npm uninstall --save bcrypt` (or bcrypt-nodejs)



## [181220 TIL] 마무리

1. 이른 배포의 중요성

   Agile Scrum 방식을 초반부터 강조했으나, 진행하다 보니 정작 실천하지 못하고 waterfall 방식으로 진행하게 되었다.

   그러다 보니 빠른 배포가 진행되지 못하여 마지막 AWS 배포 시, 개발 환경(mac)과 배포 환경(linux)의 차이로 인한 충돌이 발생하였고, 조금씩 진행된 사항에 대해서는 오류 수정이 가능하였겠지만, 다 완성되고 난 후 한꺼번에 터진 오류를 수정하려고 하니 아무리 수정을 하려고 하여도 끝끝내 모든 오류 수정이 불가하였다.

   따라서 앞으로는 배포는 최대한 일찍, 자주 진행하며, 환경 차이로 인해 충돌이 발생하는 라이브러리들(bcrypt, passport etc.)에 대해 미리 인지하고 충돌 여부를 확인하면서 진행해야 하중에 한꺼번에 터지는 일이 없을 것 같다.

   또한 DB 같은 경우에도 AWS RDS를 이용하여 클라우드 DB 서버를 미리 띄워놓고 사용해야 동일한 데이터에 팀원들이 다같이 접근하여 사용하기에 어려움이 없을 것 같다.

   다시 하게 된다면, 우선 AWS RDS로 MySQL 서버 배포부터 한 다음, scrum 단위별로 지속적인 배포를 통해 테스트를 계속 해보거나 혹은 docker를 사용하여 개발 환경 그대로를 배포하는 방법도 고려해 볼 것 같다. [docker에 대해 좀 더 알아보기]

2. Git Flow 에 대한 이해

   local, origin, upstream의 branch들이 삼선일체를 이루도록!

   conflict이 많은 경우 미리 git pull을 받아서 local에서 해당 feature와 merge 한 후 다시 origin으로 push하면 PR이 자동 업데이트 되어 충돌이 없어진다. (git pull -- rebase upstream dev : merge 커밋없이 fetch만 해 옴)

3. ESLint 사용

   팀웍 작업시 서로 간의 코드 스타일이 너무 달라 merge 시 그로 인해 불필요한 conflict가 많이 발생하는 경우를 방지하기 위해서라도 반드시 필요!

4. MVC 패턴

   express-generator가 MVC 패턴을 지원해주나, 결국 라우팅 구현에 급급하다 보니 최종적으로는 MVC 패턴을 고려하지 못한 채 route에 model과 controller 부분이 혼재하게 되었다. 다음 번에는 꼭 MVC 패턴에 맞게 제대로 구현해 볼 것!



[reference]

https://hackernoon.com/setting-up-node-js-with-a-database-part-1-3f2461bdd77f

https://medium.com/@tinabu/how-to-deploy-your-node-js-server-using-mysql-database-on-aws-mac-e64aaa2c86c