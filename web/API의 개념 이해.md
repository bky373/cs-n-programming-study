# API의 개념 이해

# 목차
  1. [API란](#1-API란) 
  2. [API의 장점](#2-API의-장점)
  3. [SOAP과 REST API](#3-SOAP과-REST-API)

# 1. API란

> Application Programming Interfaces,  API

### 조금 간단한 정의

- API는 **특정 서비스의 리소스를 다른 서비스에 전달할 수 있도록 도와준다.** 즉, 서비스 리소스의 접근 권한을 누구에게, 어떤 방식으로 제공할지에 대해 구현해놓은 서비스 툴이다.  

### 어려운 정의

- API는 **애플리케이션 소프트웨어를 빌딩하고 통합하기 위한 툴**이다. (또는 **프로토콜 세트**로 여긴다) 
API를 통해 **서비스와 리소스를 다른 서비스에 연결, 전달**할 수 있도록 한다.
- API는 **문서화된 계약**으로 비유되기도 한다. API에 의거하여, 한쪽 당사자가 특정 방식의 요청을 보내면 다른 쪽 당사자(소프트웨어)는 이에 응답하는 방식으로 동작한다.
- 다양한 사용자와 애플리케이션의 요구사항에 맞춰 **새로운 애플리케이션을 그때그때 만드는 게 아니라**, API를 만들어 제공함으로써 빠르게 변화하는 **비즈니스 요구사항**에 **신속하게 대응**할 수 있다.


![](https://images.velog.io/images/bky373/post/6ac89fba-5a41-4b3e-a119-4f41b93ffc30/image.png)

> Ref. [API(애플리케이션 프로그래밍 인터페이스)란 - 개념, 기능, 장점 - Red Hat](https://www.redhat.com/ko/topics/api/what-are-application-programming-interfaces)

- **도서를 유통하는 회사**를 예로 들어보자.
    - 도서 유통업체에서 고객에게 애플리케이션을 제공하여, 서점 직원이 유통업체의 도서 재고를 확인하도록 할 수 있지만, 애플리케이션을 개발하고 이를 지속적으로 관리하는 데 많은 비용과 시간이 발생한다.
    - 이를 대신해, **재고 확인용 API**를 제공하는 것으로 아래의 이점을 얻을 수 있다.
        1. 고객이 API를 통해 데이터에 접근할 수 있으므로 한 곳에서 재고 정보를 통합할 수 있다.
        2. API 작동에 변화가 없는 한, 도서 유통업체는 고객에게 영향을 미치지 않고 내부 시스템을 변경할 수 있다.
        3. 도서 유통업체의 개발자, 도서 판매자 또는 제3자가 오픈 API를 이용하여 고객이 원하는 도서를 찾도록 도와주는 애플리케이션을 개발할 수 있고, 이는 매출을 늘리거나 기타 비즈니스 기회를 창출할 수 있도록 도와준다.
        - 참고로, API를 공유할 때 아래와 같은 이점을 얻을 수 있다.
            1. **새로운 수익 채널을 확보**하거나 **기존 수익  채널을 확장**할 수 있다.
            2. **브랜드의 인지도**를 확대할 수 있다.
            3. 외부 개발 및 협업을 통해  **혁신을 촉진**할 수 있고 **효율성을 증대**시킬 수 있다.

# 2. API의 장점

- **통합**이 핵심이다. IT 조직 전반에서 데이터, 애플리케이션 및 기기를 연결하여 기술 전체가 서로 원활히 통신하고 잘 연동되도록 도와준다. (애자일 통합의 핵심 기능 중 하나)

![](https://images.velog.io/images/bky373/post/4ea433ac-cd37-4d96-bb7f-0146016c5f56/image.png)

> Ref. [API란 - 개념, 기능, 장점](https://www.notion.so/%5B%3Chttps://www.redhat.com/ko/topics/api/what-are-application-programming-interface%3E%5D(%3Chttps://www.redhat.com/ko/topics/api/what-are-application-programming-interfaces%3E)s)

- 구현 방식을 알지 못해도, 새로운 연결 인프라를 지속적으로 구축하지 않아도, 
**제품 및 서비스가 서로 커뮤니케이션**할 수 있도록 도와준다.
- **애플리케이션 개발을 간소화하여 시간과 비용을 절약**할 수 있게 한다.
- 새로운 툴과 제품을 설계하거나 기존 툴과 제품을 관리하는 경우에 유연성을 제공한다.

# 3. SOAP과 REST API

**SOAP**(Simple Object Access Protocol)와 **REST**(Representational State Transfer)는 **API 설계 편의성을 높이기 위해 사용**되었다. 

## SOAP

- **SOAP**은 **프로토콜** 중 하나로, 웹 API가 널리 사용되면서, **메시지 형식 및 요청**을 **표준화**하기 위해 개발되었다. 다양한 환경에 있거나 다양한 언어로 작성된 애플리케이션이 보다 쉽게 통신할 수 있도록 도와준다.
- SOAP으로 설계된 API는 **XML 메시지 형식을 사용**하며 HTTP 또는 SMTP를 통해 요청을 수신한다.
> SOAP은 **XML을 근간으로 헤더와 바디를 조합**하는 디자인 패턴으로 설계되어 있다. 「헤더」는 선택사항으로 반복이나 보안 및 트랜잭션을 정보로 하는 메타 정보를 가지고 있다. 「바디」부분은 주요한 정보를 가지고 있다.
[출처](https://ko.wikipedia.org/wiki/SOAP)

## REST API

- **REST**는 **아키텍처 스타일** 중 하나이다. **RESTful API**는 **REST 아키텍처의 제약 조건을 준수하는 웹 API**를 가리켜 말한다.
- SOAP는 프로토콜이지만 REST는 아키텍처 스타일이라는 근본적인 차이가 있으며 따라서 RESTful 웹 API에는 **공식적인 표준이 없다**. REST는 규정된 프로토콜을 따르는 것보다 훨씬 간단한 **6가지 원칙**을 기본으로 한다. 이러한 이유로 RESTful API는 SOAP보다 더 많이 사용되고 있다.

# **References**

- [API(애플리케이션 프로그래밍 인터페이스)란 - 개념, 기능, 장점 - Red Hat](https://www.redhat.com/ko/topics/api/what-are-application-programming-interfaces)
- [API의 이해 - Red Hat](https://www.redhat.com/ko/topics/api)
