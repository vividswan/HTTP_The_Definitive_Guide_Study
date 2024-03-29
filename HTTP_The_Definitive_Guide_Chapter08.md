# 8장 - 통합점: 게이트웨이, 터널, 릴레이

- HTTP 애플리케이션은 콘텐츠에 접근하는 동일한 방법 제공
- HTTP는 웹에 있는 모든 리소스에 대한 프로토콜, 기본 구성 요소

## 8.1 게이트웨이

- 리소스와 애플리케이션을 연결하는 역할
- 애플리케이션은 게이트웨이에게 요청을 처리해달라고 할 수 있고 게이트웨이는 그에 응답
- 게이트웨이는 HTTP 트래픽을 다른 프로토콜로 자동 변환 후 클라이언트가 알 필요 다른 프로토콜을 알 필요 없이 서버에 접속할 수 있게 해줌
- 게이트웨이 예제
  - HTTP/FTP 서버 측 FTP 게이트웨이
  - HTTPS/HTTP 클라이언트 측 보안 게이트웨이
  - HTTP/CGI 서버 측 애플리케이션 게이트웨이

### 8.1.1 클라이언트 측 게이트웨이와 서버 측 게이트웨이

- 웹 게이트웨이
  - 한쪽에서는 HTTP, 다른 한쪽에서는 HTTP가 아닌 다른 프로토콜로 통신
  - 클라이언트 측 프로토콜과 서버 측 프로토콜을 빗금으로 구분해 기술
  - <클라이언트 프로토콜>/<서버 프로토콜>
  - 서버 측 게이트웨이는 클라이언트와 HTTP로 통신, 서버와는 외래 프로토콜로 통신
  - 클라이언트 측 게이트웨이는 클라이언트와 외래 프로토콜로 통신, 서버와는 HTTP로 통신

## 8.2 프로토콜 게이트웨이

- 게이트웨이에도 HTTP 트래픽을 바로 보낼 수 있음
  - 브라우저에 명시적 설정 or 대리 서버(리버스 프락시)로 게이트웨이를 설정
- 브라우저는 일반 HTTP 트래픽은 원 서버로 바로 보내지만 설정된 요청은 게이트웨이로 HTTP 요청을 보냄

### 8.2.1 HTTP/\*: 서버 측 웹 게이트웨이

- HTTP 요청이 원 서버 영역으로 들어오는 시점에 클라이언트 측의 HTTP 요청을 외래 프로토콜로 전환
- HTTP/FTP 게이트웨이의 경우 로그인, CWD 명령, 형식 수정, 객체 검색 등등을 게이트웨이가 담당
- 게이트웨이는 객체를 받는 대로 HTTP 응답에 실어서 클라이언트에게 전송할 것임

### 8.2.2 HTTP/HTTPS: 서버 측 보안 게이트웨이

- 기업 내부의 모든 웹 요청을 암호화함으로써 보안을 제공하는 게이트웨이 사용 가능
- 클라이언트는 일반 HTTP를 사용하여 웹을 사용하지만 게이트웨이가 자동으로 사용자의 모든 세션을 암호화

### 8.2.3 HTTPS/HTTP: 클라이언트 측 보안 가속 게이트웨이

- 웹 서버의 앞단에 위치
- 인터셉트 게이트웨이나 리버스 프락시 역할
- 보안 HTTPS 트래픽을 받아서 복호화 후 웹 서버로 보낼 일반 HTTP 요청을 만듦
- 복호화 하는 하드웨어를 내장해서 부하를 줄여주기도 함
- 게이트웨이와 원 서버 간은 암호화하지 않은 트래픽이 전송되기 때문에 네트워크 안전 확인을 해야 함

## 8.3 리소스 게이트웨이

- 일반적인 형태인 애플리케이션 서버는 목적지 서버와 게이트웨이를 한 개의 서버로 결합
  - HTTP를 통해서 클라이언트와 통신
- 클라이언트가 HTTP를 사용하여 서버로 연결 시 애플리케이션 서버는 게이트웨이의 '애플리케이션 프로그래밍 인터페이스(Application Programming Interface, API)'를 통해 서버에 요청을 전달
- 유명했던 최초의 API는 공용 게이트웨이 인터페이스(Common Gateway Interface, CGI)
  - 요청이 들어오면 서버는 헬퍼 애플리케이션을 생성
  - 헬퍼 애플리케이션이 필요한 요청을 전달받음
  - 클라이언트에 전달할 응답, 응답 데이터를 서버에 반환

### 8.3.1 공용 게이트웨이 인터페이스

- CGI는 최초의 서버 확장, 지금까지도 널리 쓰임
- CGI 애플리케이션이 서버와 분리되면서 다양한 언어로 구현 가능해짐
- 거의 모든 HTTP 서버가 지원

### 8.3.2 서버 확장 API

- 서버 확장 API
  - 서버 자체의 동작을 바꾸거나 서버의 처리능력을 끌어올리고자 사용
  - 자신의 모듈을 HTTP와 직접 연결할 수 있는 인터페이스
- 유명한 서버 대부분이 개발자에게 확장 API를 한 개 이상 제공

## 8.4 애플리케이션 인터페이스와 웹 서비스

- 웹 애플리케이션이 더 많은 형식의 서비스를 제공함에 따라 HTTP 가 애플리케이션을 연결하는 도구로 더 활용됨
- 애플리케이션이 정보를 공유하는 데 사용하는 새로운 메커니즘인 웹 서버는 HTTP와 같은 표준 웹 기술 위에서 개발
- 웹 서비스는 SOAP을 통해 XML을 사용하여 정보 교환
  - XML(eXtensible Markup Language) : 데이터 객체를 담는 데이터를 생성 및 해석하는 방식 제공
  - SOAP(Simple Object Access Protocol) : HTTP 메시지에 XML 데이터를 담는 방식에 관한 표준
  - 현대 웹 서비스는 JSON 포맷의 REST 방식을 많이 사용

## 8.5 터널

- 웹 터널은 HTTP 프로토콜을 지원하지 않는 애플리케이션에 HTTP 애플리케이션을 사용해 접근하는 방법 제공
  - HTTP 커넥션을 통해 HTTP가 아닌 트래픽 전송 가능 (가장 일반적인 이유)
  - 다른 프로토콜을 HTTP 위에 올릴 수 있음

### 8.5.1 CONNECT로 HTTP 터널 커넥션 맺기

- 웹 터널은 HTTP의 CONNECT 메서드를 사용하여 커넥션을 맺음
- CONNECT 메서드는 터널 게이트웨이가 임의의 목적 서버와 포트에 TCP 커넥션을 맺고 서버 간에 오는 데이터를 무조건 전달하기를 요청
- CONNECT 메서드를 사용해 게이트웨이로 터널(클라이언트와 게이트웨이 사이)을 연결하는 예시 순서
  - 클라이언트가 CONNECT 요청을 보낸 후 CONNECT 메서드는 TCP 커넥션을 위해 게이트웨이에 터널 연결을 요청
  - 게이트웨이와 서버 사이에 TCP 커넥션이 생성
  - 게이트웨이가 클라이언트에게 알림
  - 이 시점에 터널이 연결 (모든 데이터는 TCP 커넥션으로 바로 전달)
- CONNECT 메서드는 모든 서버나 프로토콜에 TCP 커넥션을 맺는데 사용 가능
- CONNECT 요청
  - 문법은 시작줄을 제외하곤 다른 HTTP 메서드와 동일
  - 요청 URI는 호스트 명이 대신하며 콜론에 이어 포트 기술
- CONNECT 응답
  - 클라이언트는 요청을 전송 후 게이트웨이 응답 기다림
  - 200 응답 코드는 성공을 의미, 사유 구절은 'Connection Established'로 기술
  - 응답에서 Content-Type 헤더를 포함할 필요는 X (메시지 대신 바이트를 그대로 전달하기 때문)

### 8.5.2 데이터 터널링, 시간, 커넥션 관리

- 터널이 연결되면 데이터는 언제 어디로든 흘러가버릴 수 있음
  - 소비하지 않을 시 교착상태 발생 가능
- CONNECT 요청 보낸 후 응답을 받기 전 터널 데이터 전송 가능
  - 게이트웨이가 요청에 이어서 데이터를 적절하게 처리할 수 있는 것이 전재
  - 게이트웨이는 커넥션이 맺어지는 대로 읽어드린 모든 데이터를 서버에 전송해야 함
- 커넥션이 끊어지면 끊어진 곳으로부터 온 데이터는 반대편으로 전달
  - 끝단 반대편의 커넥션도 끊어질 것
  - 전송하지 않은 데이터는 버려짐

### 8.5.3 SSL 터널링

- SSL 같이 암호화된 프로토콜은 낡은 방식의 프락시에서는 처리 X
  - 정보가 암호화되어 있기 때문
- SSL 트래픽이 기존 프락시 방화벽을 통과할 수 있도록 HTTP에 터널링 기능이 추가
  - SSL 트래픽은 일반 SSL 커넥션을 통해 전송되기 전까지는 HTTP 메시지에 담겨 HTTP 포트인 80에 전송
  - HTTP가 아닌 트래픽이 포트를 제한하는 방화벽을 통과할 수 있게 해줌 (악의적인 트래픽의 경로가 될 수도 있음)

### 8.5.4 SSL 터널링 vs HTTP/HTTPS 게이트웨이

- 원격 HTTPS 서버와 SSL 세션을 시작하는 게이트웨이를 두고 클라이언트 측의 HTTPS 트랜잭션을 수행하는 방식
  - 클라이언트-게이트웨이 사이에 보안이 적용되지 않은 일반 HTTP 커넥션이 맺어져 있음
  - 프락시가 담당을 인증하고 있음 -> 클라이언트는 원격 서버에 SSL 클라이언트 인증 불가능
  - 게이트웨이는 SSL을 완벽히 지원해야 함
- SSL 터널링을 사용하면 프락시에 SSL을 구현할 필요가 없음
  - SSL 세션은 클라이언트가 생성한 요청과 목적지 웹 서버 간에 생성
  - 프락시 서버는 트랜잭션의 보안에는 관여하지 않고 암호화된 데이터를 그대로 터널링 할 뿐임

### 8.5.5 터널 인증

- HTTP의 다른 기능들은 터널과 함께 적절히 사용 가능
  - 프락시 인증 기능을 클라이언트가 터널을 사용할 수 있는 권한을 검사하는 용도로 터널에서 사용 가능
  - CONNECT 요청 전송 후 인증 요구를 게이트웨이가 반환하고 클라이언트가 다시 인증과 함께 CONNECT 요청 전송

### 8.5.6 터널 보안에 대한 고려 사항들

- 터널 게이트웨이는 통신하고 있는 프로토콜이 터널을 올바른 용도로 사용하고 있는지 검증할 방법이 없음
- 게이트웨이는 HTTPS 전용 포트인 443과 같이 잘 알려진 특정 포트만을 터널링 할 수 있게 허용해야 함

## 8.6 릴레이

- HTTP 명세를 완전히 준수하지는 않는 간단한 HTTP 프락시
- 커넥션을 맺기 위한 HTTP 통신을 한 다음 바이트를 맹목적으로 전달
- 복잡한 HTTP 대신 맹목적으로 트래픽을 전달하는 간단한 프락시를 구현하는 방식
- 상호 운용 문제가 일어나지 않게 주의해서 배포할 것
  - ex) keep-alive에 대해 이해하지 못하는 릴레이가 클라이언트와 서버에 `Connection: Keep-Alive` 헤더를 전송하여 브라우저는 계속 돌지만 다음 요청이 올 것이라는 걸 예측하지 못하는 릴레이
  - 여러 문제를 예방하기 위해 HTTP를 제대로 준수하는 프락시를 사용하는 게 좋음
