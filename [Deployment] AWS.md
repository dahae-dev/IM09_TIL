# AWS



#### cloud computing

클라우드 컴퓨팅은 인터넷(클라우드)을 통해 서버, 데이터베이스, 스토리지, 어플리케이션 및 각종 IT 자원들, 즉 컴퓨팅 서비스를 제공하는 것으로,

기존에는 서버를 직접 구축해야 했으나 최근에는 aws와 같은 대기업의 서버구축 노하우를 이용하여 서버를 직접 구축하지 않고도 적은 비용과 빠른 시간 내에 서버를 구축할 수 있도록 해주며, 거기에 사용되는 컴퓨터들이 cloud computer이다.

서버를 직접 구축하는 데에는 비용적인 측면에서 부담이 큰데, 규모가 작거나 서버가 많이 필요하지 않은 경우, cloud computer를 빌려 유동적으로 데이터 공간을 빌릴 수 있기 때문에 비용이 절감되고 효율적인 인프라 관리가 가능하다. 또한 국내 뿐만 아니라 여러 국가에 cloud computer가 배치되어 있어 해외에도 서버를 두고 사용하기에 용이한 등 확장성 또한 좋다.



#### deployment

CI (Continuous Integration)

CD (Continuous Delivery | Deployment)



#### service block 

S3 (Simple Storage Service) : static resource (client-side)

EC2 (Elastic Cloud Computing) : dynamic resource (server-side)



#### 2 ways of deploying web services

1) S3 serves static resources, EC2 serves dynamic resources

* easy to update for client-side

2) EC2 serves dynamic resources and dynamic resource serves static resources

* manage two github repository; server-side & client-side => git sub-module 사용 (server 코드 내부에 client 코드가 있는 경우)



LTS (Long-Term Service) : stable version



#### PM2