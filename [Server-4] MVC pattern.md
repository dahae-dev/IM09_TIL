# MVC Pattern



#### MVC란?

Model View Controller의 약자로, 에플리케이션을 세가지의 역할로 구분한 소프트웨어 아키텍쳐 디자인 패턴.

Controller의 조작을 통해 Controller는 Model을 통해서 데이터를 가져오고, 그 데이터를 바탕으로 시각적인 표현을 담당하는 View를 제어하여 클라이언트에게 전달. 

- Controller

  사용자가 접근 한 URL에 따라서 사용자의 요청사항을 파악한 후에 그 요청에 맞는 데이터를 Model에 의뢰하고, 데이터를 View에 반영해서 사용자에게 알려준다. Model과 View 사이에서 중개자 역할을 하며, Model과 View는 직접적으로 소통하지 않는다.

- Model

  일반적으로 데이터베이스 테이블에 대응. 

- View

  클라이언트 측의 html/css/javascript들을 모아둔 컨테이너. 

