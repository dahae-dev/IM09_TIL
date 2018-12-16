# MySQL 설치 및 환경설정



### 설치 시 고려사항

1. 환경변수 설정 (터미널에서 mysql 설치 폴더 외의 경로에서도 `mysql` 명령어가 가능하도록 설정)
2. mysql 데몬 실행 (백그라운드에서 mysql 서버데몬이 실행중이어야 클라이언트로 접속 가능)
3. charset utf8 설정
4. 보안 - root 비밀번호, 원격 접속 허용 여부 등 설정 (mysql에서 root사용자는 초기 비밀번호 미설정 상태)
5. user 생성 및 권한 부여 



### HomeBrew를 통한 설치

#### * how to

1. [HomeBrew](http://brew.sh/) 설치
2. mysql 설치 : `$ brew install mysql`
3. mysql 시작 : `$ mysql.server start`
4. root 비밀번호 설정 : `$ mysql_secure_installation`

- `Would you like to setup VALIDATE PASSWORD plugin?` : 비밀번호 가이드. 복잡한 비밀번호를 사용하도록 제한해주는 플러그인을 사용하려면 `yes`, ~~보안은 무시하고~~ 그냥 쓰던 비밀번호 제한받지 않고 쓰고 싶다면 `no`.
- `Remove anonymous users?` : 익명사용자 삭제할지/계속 사용할지 여부. `no` 하면 `$ mysql -uroot`가 아니라 `$ mysql`만으로도 접속 가능. `yes`하면 `-u` 옵션 필수.
- `Disallow root login remotely?` : localhost외에 다른 ip에서 root 아이디로 원격접속 가능하게 할지. `yes`하면 원겹접속 불가.
- `Remove test database and access to it?` : mysql에 기본적으로 설정된 test 디비 삭제 여부.
- `Reload privilege tables now?` : 하나라도 권한 변경을 했다면 `yes` 해서 하는 게 정신건강에 좋을 것입니다.

5. charset 설정 : `$ mysql -uroot -p`로 로그인하고, `> status;`로 charset 확인.

- 기본적으로 charset 4개가 모두 utf8이어서 그대로 두면 됨.
- 혹시나 latin등 다른 값이 있다면 위의 **4. charset UTF-8로 바꾸기** 따라하기. `my.cnf`가 기본적으로 존재하지 않으므로 `$ vi /etc/my.cnf` 등을 통해 새로 생성.

6. 데몬 실행 `$ brew services start mysql` ([참고](https://robots.thoughtbot.com/starting-and-stopping-background-services-with-homebrew))

- 로그인시 데몬 자동 실행 (To have launchd start mysql at login): `$ ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents`

 (+) brew로 설치했다면 환경변수 설정은 필요 없음!



### MySQL 서버 실행 및 접속

```
$ mysql.server start
$ mysql -u USERNAME - p 
// enter mysql password prompt

$ mysql.server stop
```



### 접속 설정

```javascript
// connection.js
const path = require('path');
var mysql = require('mysql2');

const env = process.env.NODE_ENV || 'development';
const config = require(__dirname + '/../config/config.json')[env];

var conn = mysql.createConnection({
  host: 'localhost',
  user: config.username,
  password: config.password,
  database: config.database
});

conn.connect((err) => {
  if (err) {
    return console.error('error: ' + err.message);
  }
  console.log('Connected to MySQL server');
});

module.exports = {
  conn: conn
}

// app.js
const db = require('./connection'); // require만 해주면 해당 파일을 읽으면서 연결

// config.json --> username & password는 반드시 별도 환경변수 파일에 저장
{
  "development": {
    "username": "root",
    "password": "1234",
    "database": "iyagi",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}
```



### [참고]versions

brew는 기본적으로 최신 버전(5.7)을 제공하지만, 다른 버전(5.6 등)을 설치하고 싶은 경우 [brew versions](https://github.com/Homebrew/homebrew-versions)를 사용합니다.

1. `$ brew tap homebrew/versions`로 brew versions를 설치 후,
2. `$ brew search mysql`로 검색, 원하는 버전의 formula 이름을 확인합니다. (예: `homebrew/versions/mysql56`)
3. `$ brew install <설치할 formula>`으로 mysql 설치. (예: `$ brew install homebrew/versions/mysql56`)



### [참고] 공식 사이트를 통해 .dmg로 설치

#### * 주의

- [공식 사이트](http://dev.mysql.com/downloads/mysql/)의 목록을 보시면.. OS X 10.10(Yosemite) 까지만 지원합니다.
- 사실 운영체제의 버전을 떠나서, (환경변수 설정 필요없는 등) 여러가지로 편리한 [brew로 설치](https://github.com/helloheesu/SecretlyGreatly/wiki/%EB%A7%A5%EC%97%90%EC%84%9C-mysql-%EC%84%A4%EC%B9%98-%ED%9B%84-%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0#homebrew%EB%A5%BC-%ED%86%B5%ED%95%9C-%EC%84%A4%EC%B9%98)하시길 추천합니다.

#### * how to

[1번 링크](http://mirwebma.tistory.com/5), [2번 링크](http://nsnotification.blogspot.kr/2013/05/mac-osmountain-lion-mysql.html)

1. MySQL 설치하기

- **2번링크 2.mysql 다운로드 ~ 4.점검** 참조. (새로 설치하는 경우 굳이 **1. 기존 인스톨된 mysql 삭제**는 따라할 필요 없음.)

1. 환경변수 설정하기

- **1번링크 1. MySQL 패치 설정하기!** 참조.
- 기본 터미널 외의 다른 쉘은 파일명(`.profile`)이 다를 수 있음. (예: zsh은 [`.zshrc`](http://superuser.com/questions/187639/zsh-not-hitting-profile))

1. root 사용자의 비밀번호 설정

- **2번링크 5. 터미널로 mysql 붙은 김에 할일** 참조.

1. 기본 charset을 UTF-8로 바꾸기

- **2번링크 [mysql 을 UTF-8 환경으로 설정]** 참조.

1. 혹시 기본 charset을 설정하기 전에 이미 생성한 테이블과 데이터베이스가 있다면, charset 바꾸는 방법.

- [stackoverflow 답변](http://stackoverflow.com/questions/6115612/how-to-convert-an-entire-mysql-database-characterset-and-collation-to-utf-8#answer-6115705) 참조하여 ALTER DATABASE / ALTER TABLE.
- 기본 charset을 설정하기 전에 이미 데이터베이스와 테이블을 설정했다면, 위 글을 따라 데이터베이스와 테이블의 charset을 바꿔줍니다.
- 특정 컬럼만 설정할 수도 있습니다. [참고 링크](http://devday.tistory.com/entry/MySQL%EC%97%90%EC%84%9C-%EC%9D%B4%EB%AF%B8-%EC%83%9D%EC%84%B1%EB%90%9C-DB-Database-%ED%85%8C%EC%9D%B4%EB%B8%94-Table-%EC%BB%AC%EB%9F%BC-Column%EC%9D%98-Character-Set-%EB%B3%80%EA%B2%BD%ED%95%98%EA%B8%B0)
- +, 이미 깨진 데이터는 복구할 수가 없어요 ㅜㅜ



*** 잘못 설치 했을 때**

https://github.com/appkr/l5code/issues/4