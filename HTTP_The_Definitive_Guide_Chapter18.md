# 18장 - 웹 호스팅

- 콘텐츠 리소스를 저장, 중개, 관리하는 일
  - 웹 서버의 가장 중요한 기능 중 하나
  - 호스팅 업체가 지원해 주기도 함

## 18.1 호스팅 서비스

- 개인이 냉난방 장치가 있는 서버실이나 도메인, 네트워크 대역폭을 구하기엔 한계
- 물리적인 장비 관리부터 고객이 직접 콘텐츠를 제공할 수 있는 여러 가지 서비스들을 제공

### 18.1.1 간단한 예 : 전용 호스팅

- 고성능 웹 서버들로 구성된 랙을 ISP가 가지고 있으며 이를 제공
- 클라이언트는 ISP가 구매해 유지 보수하고 있는 전용 웹 서버를 임대
  - 안정적으로 검증된, 비용이 저렴한 장비 선택 가능

## 18.2 가상 호스팅

- 가상 호스팅 or 공유 호스팅
  - 컴퓨터 한 대를 여러 고객이 공유하게 해서 저렴한 웹 호스팅 서비스를 제공하는 것
- 최종 사용자는 물리적으로 분리된 것인지를 구분할 수 없어야 함
- 호스팅 업자는 복제 서버 더미를 만들고 서버 팜에 부하 분산 가능

### 18.2.1 호스트 정보가 없는 가상 서버 요청

- HTTP/1.0 명세는 공용 웹 서버가 호스팅하고 있는 가상 웹 사이트에 누가 접근하고 있는지 식별하는 기능 제공 X
  - 완전히 다른 문서를 요청하더라도 요청 자체는 같을 수 있음
  - 대리 서버(리버스 프락시)와 인터셉트 프락시 또한 어떤 사이트를 요청하는지에 관한 정보가 필요

### 18.2.2 가상 호스팅 동작하게 하기

- 가상 호스팅을 고려하지 않은 초기 명세 때문에, 웹 호스팅 업자는 이에 대한 차선책과 컨벤션을 개발
- 네 가지 기술
  - URL 경로를 통한 가상 호스팅
  - 포트 번호를 통한 가상 호스팅
  - IP 주소를 통한 가상 호스팅
  - Host 헤더를 통한 가상 호스팅
- URL 경로를 통한 가상 호스팅
  - 공용 서버의 각 가상 사이트에 서로 다른 URL 경로를 할당 후 각각을 강제로 구분
  - 호스트 명 정보가 요청에 포함되지는 않지만 경로에 있는 정보로 서버가 이해
  - 좋은 해결책이 아님 (불필요한 접두사, index.html 등으로 끝나는 URL)
- 포트 번호를 통한 가상 호스팅
  - 웹 서버에 각각 다른 포트 번호를 할당
  - 사용자는 URL에 비표준 포트를 쓰지 않고서도 리소스를 찾기 원하는 한계
- IP 주소를 통한 가상 호스팅
  - 위의 방법들 보다 훨씬 더 좋은 접근
  - 각 가상 웹사이트에 유일한 IP 주소(가상 IP)를 한 개 이상 부여
  - 널리 쓰이는 방식
  - 컴퓨터 시스템에 연결할 수 있는 장비의 IP 개수의 제한, IP 주소는 희소 상품 , 복제가 늘어남 등의 문제가 있음
- Host 헤더를 통한 가상 호스팅
  - 서버가 원 호스트 명을 받아 볼 수 있게 HTTP를 확장
  - 대부분의 서버는 경로 컴포넌트만 받아 요청을 처리하기 때문에 이 땐 전체 URL도 소용 X
  - 모든 요청에 호스트 명과 포트를 Host 확장 헤더에 기술해서 전달
  - Http/1.1 명세를 따르려면 반드시 Host 헤더를 기술

### 18.2.3 HTTP/1.1 Host 헤더

- 가상 서버가 매우 흔하므로 대부분의 HTTP 클라이언트가 Host 헤더 구현
- 문법과 사용 방법
  - 원본 URL에 있는 리소스에 대한 호스트와 포트 번호를 기술
  - `Host = "Host" ":"호스트[ ":"포트 ]`
  - 포트가 기술되어 있지 않다면 기본 포트
  - URL에 IP 주소가 있으면 Host 헤더는 같은 주소를 포함해야 함
  - URL에 호스트 명 기술되어 있을 시 Host 헤더는 같은 호스트 명을 포함해야 함
  - URL에 호스트 명 기술되어 있을 시 Host 헤더는 IP 주소를 포함하면 안 됨
  - Host 헤더에 프락시 서버가 아닌 원 서버의 호스트 명과 포트 기술
  - 웹 클라이언트는 모든 요청 메시지에 Host 헤더를 기술
  - 웹 프락시는 요청을 전달하기 전 요청 메시지에 Host 헤더 추가
  - HTTP/1.1 웹 서버는 요청에 Host 헤더 필드가 없을 시 400 상태 코드로 응답
- Host 헤더의 누락
  - 현재는 모든 주요 브라우저가 Host 헤더를 전송
  - 낡은 브라우저들이 Host 헤더를 보내지 않을 시 서버는 사용자를 기본 웹페이지로 보내거나 브라우저 업그레이드 제안
- Host 헤더 해석하기
  - 가상 호스팅을 지원하지 않는 원 서버는 Host 헤더 값 무시
  - 호스트를 기준으로 리소스를 구분하는 모든 웹 서버는 규칙을 갖고 사용
  - HTTP 요청 메시지에 전체 URL이 기술되어 있으면 Host 헤더 값은 무시
  - HTTP 요청 메시지에 전체 URL이 기술되어 있지 않고 Host 헤더가 있으면 Host 헤더 값 사용
  - HTTP 요청 메시지에 전체 URL이 기술되어 있지 않고 Host 헤더가 없으면 400 Bad Request 응답 반환
- Host 헤더와 프락시
  - 버전이 오래된 클라이언트들은 실수로 원 서버의 이름이 아닌 프락시의 이름을 Host 헤더에 담아 전송

## 18.3 안정적인 웹 사이트 만들기

- 웹 사이트에 장애가 생기는 몇 가지 상황
  - 서버 다운
  - 트래픽 폭증 (특정 시간, 특정 상황) -> 과부하 유발
  - 네트워크 장애나 손실

### 18.3.1 미러링 된 서버 팜

- 서버 팜은 서로 대신할 수 있고 식별할 수 있게 설정된 웹 서버들의 집합
  - 한 곳에 문제가 생기면 다른 한 곳에서 대신 전달할 수 있게 미러링
- 미러링 된 서버는 보통 계층적 관계
  - 원 서버는 콘텐츠의 원본 제작자같이 행동
  - 원 서버로부터 콘텐츠를 받은 서버는 복제 원 서버
  - 네트워크 스위치를 사용해서 서버에 분산 요청 보냄
  - 각 웹 사이트의 IP 주소는 스위치의 IP 주소
  - 스위치는 서버에게 요청을 전송해야 하는 책임
  - 미러링 된 웹 서버에는 다른 위치에 있는 콘텐츠와 정확히 같은 복제본 소유
- 클라이언트의 요청이 특정 서버로 가는 두 가지 방법
  - HTTP 리다이렉션 (마스터 서버를 가리키고 마스터 서버가 요청을 복제 서버로 리다이렉트)
  - DNS 리다이렉션 (다른 IP 주소들을 가리키고 DNS 서버는 클라이언트에게 전송할 IP 주소 선택 가능)

### 18.3.2 콘텐츠 분산 네트워크

- 콘텐츠 분산 네트워크(CDN)는 특정 콘텐츠의 분산을 목적으로 하는 단순한 네트워크
- 네트워크의 노드는 서버, 대리 서버, 프락시 서버가 될 수 있음

### 18.3.3 CDN의 대리 캐시

- 대리 캐시는 복제 원 서버를 대신해 사용될 수 있음
  - 리버스 프락시라고도 불림
  - 미러링 된 웹 서버처럼 콘텐츠에 대한 요청을 받음
  - 특정 원 서버 집합을 대신해 요청을 받음
  - 미러링 된 서버와 차이점은 보통 수요에 따라서 동작하며 원 서버의 전체 콘텐츠를 복사하지는 않음, 분산되는 방식 또한 요청에 따라 다름

### 18.3.4 CDN의 프락시 캐시

- 대리 서버와는 다르게 전통적인 프락시 캐시는 어떤 웹 서버 요청이든지 다 받을 수 있음
  - 대리 서버를 사용하면 요청이 있을 때만 저장될 것이고 원본 서버 콘텐츠를 정확히 복제한다는 보장 X
  - 어떤 프락시는 요청을 많이 받는 콘텐츠를 미리 로딩하기도 함
- 프락시 캐시는 레이어 2, 레이어 3의 중간 장비가 웹 트래픽을 가로채 처리하기도 함
- 가로채기 설정은 클라이언트와 서버 사이의 HTTP 요청 설정에 따라 달라짐

## 18.4 웹 사이트 빠르게 만들기

- 서버 팜이나 분산 프락시 캐시나 대리 서버는 혼잡을 조절하고 네트워크 트래픽을 분산
  - 클라이언트로의 전송 시간 단축
- 콘텐츠를 인코딩하는 것도 웹 사이트 속도를 높이는 방법이 될 수 있음
