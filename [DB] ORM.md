# ORM (Object Relational Mapping)



#### ORM (Object-Relational Mapping) 

OOP 관점에서 데이터베이스(RDMBS)에 저장될 데이터(record) 또한 하나의 객체이며, 이 객체와 테이블 간의 관계 설정을 통해 DB 테이블 내의 데이터를 객체로 전달받아 사용 가능. 따라서 이와 같은 객체 지향적인 코드로 인해 절차적이며 순차적인 접근이 요구되는 SQL보다 더 직관적, 높은 생산성. 각 객체에 대한 코드를 독립적으로 작성하기 때문에, 해당 객체들의 재사용 및 유지보수 편의성이 증가하며 MVC 디자인 패턴에도 유리. 또한 ORM 솔루션은 DB에 종속적이지 않기 때문에, DBMS 교체 작업에도 비교적 적은 리스크와 시간 소요.



#### MVC 프레임워크를 기반으로 한 express.js에서의 sequelize 연동 방법

모델 정의 및 모듈 불러오기

[ 참고 ] https://sequelize.readthedocs.io/en/1.7.0/articles/express/