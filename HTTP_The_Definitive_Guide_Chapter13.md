# 13장 - 다이제스트 인증

- 다이제스트 인증은 기본 인증과 호환되는 더 안전한 대체재
- 다이제스트 인증의 개념은 보안 트랜잭션을 구현하고자 할 때 여전히 유용

## 13.1 다이제스트 인증의 개선점

- 특징
  - 비밀번호를 절대로 평문으로 전송 X
  - 인증 체결을 가로채서 재현하려는 악의적인 사람 차단
  - 구현하기에 따라 메시지 내용 위조를 막을 수 있음
  - 몇몇 잘 알려진 형태의 공격을 막음
- 다이제스트 인증이 가장 안전한 프로토콜은 아님
  - 기본 인증보단 훨씬 강력함

### 13.1.1 비밀번호를 안전하게 지키기 위해 요약 사용하기

- 다이제스트 인증은 절대로 비밀번호를 네트워크를 통해 보내지 않음
  - 비밀번호 대신 클라이언트는 지문(fingerprint)이나 요약(digest)을 보냄
  - 서버는 클라이언트가 보낸 요약이 알맞게 대응하는지 검사할 수 있음
- 클라이언트와 요약을 전달하면 서버가 내부적으로 계산한 요약을 비교
  - 요약 함수는 매우 긴 자릿수의 숫자를 만듦으로 찍어서 맞추기 불가능

### 13.1.2 단방향 요약

- 요약은 '정보 본문의 압축'
  - 단방향 함수로 동작
  - 무한 가지의 모든 값들을 유한한 범위의 압축으로 변환
  - 요약 함수는 보통 암호 체크섬으로 불리며 단방향 해시 함수이거나 지문 함수

### 13.1.3 재전송 방지를 위한 난스(nonce) 사용

- 요약은 비밀번호 자체와 다름없으므로 재전송하여 악용할 수 있음
- 이를 방지하기 위해 클라이언트에게 난스라고 불리는 특별한 증표를 건네줌
  - 난스는 대략 1밀리 초마다 혹은 인증할 때마다 바뀜
- 저장된 비밀번호 요약은 특정 난스 값에 대해서만 유효
- 난스는 WWW-Authenticate 인증 요구에 담김

### 13.1.4 다이제스트 인증 핸드 셰이크

1. 서버가 WWW-Authenticate로 영역, 난스, 지원하는 알고리즘을 보냄
2. 클라언트가 응답 요약을 생성 후 Authorization으로 보낸다. 이때 클라이언트 난스도 보낼 수 있다.
3. 서버가 검증 후 다음번 난스를 생성 후 보낸다.

- 정보 조각들 중 다수가 선택적이며 기본값을 가짐

## 13.2 요약 계산

- 다이제스트 인증의 핵심은 공개된 정보, 비밀번호, 시한부 난스 값을 조합한 단방향 요약

### 13.2.1 요약 알고리즘 입력 데이터

- 다음의 요소들로부터 계산
  - s는 비밀(secret), d는 데이터(data)
  - 단방향 해시 함수 : H(d)
  - 요약 함수 : KD(s,d)
  - 비밀번호 등 보안 정보를 담고 있는 데이터 덩어리를 A1이라 칭함
  - 요청 메시지의 비밀이 아닌 속성을 담고 있는 데이터 덩어리를 A2라 칭함
  - A1, A2 두 조각의 데이터는 요약을 생성하기 위해 H와 KD에 처리

### 13.2.2 H(d)와 KD(s,d) 알고리즘

- 다이제스트 인증은 여러 가지 요약 알고리즘을 선택할 수 있도록 지원
  - MD5가 기본값
- H(<데이터>) = MD5(<데이터>)
- KD(<비밀>,<데이터>) = H(연결(<비밀>:<데이터>))

### 13.2.3 보안 관련 데이터 (A1)

- A1은 사용자 이름, 비밀번호, 보호 영역, 난스와 같은 비밀정보로 이루어져 있음
- 요약을 계산하기 위해 사용
- 두 가지 방법으로 정의
  - MD5 : 모든 요청마다 단방향 해시를 실행, 사용자 이름, 영역, 비밀번호를 콜론으로 연결한 것
  - MD5-sess : 사용자 이름, 영역, 비밀번호에 대한 해시를 계산한 결과 뒤에 현재 난스와 클라이언트 난스를 붙인 것이 A1

### 13.2.4 메시지 관련 데이터 (A2)

- URL, 요청 메서드, 메시지 엔터티 본문과 같은 메시지 자체의 정보
  - 메서드, 리소스, 메시지의 위조를 방지하기 위해 사용
- 보호 수준에 따른 A2의 두 가지 사용법
  - 첫 번째 방법은 HTTP 요청 메서드와 URL만 포함 (qop="auth")
  - 두 번째 방법은 메시지 무결성 검사를 제공하기 위해 메시지 엔터티 본문을 추가하는 것 (qop="auth-int")

### 13.2.5 요약 알고리즘 전반

- 두 가지 방법으로 정의
  - qop 옵션이 빠졌을 때 비밀 정보와 난스가 붙은 메시지 데이터의 해시를 이용해 요약 계산
  - qop가 auth, auth-int 일 때 모두 난스 횟수 집계 및 대칭 인증의 지원을 포함

### 13.2.6 다이제스트 인증 세션

- WWW-Authenticate 인증 요구에 대한 클라이언트의 응답은 그 보호 공간에 대한 인증 세션을 시작하게 함
  - 클라이언트가 또 다른 WWW-Authenticate 인증 요구를 받을 때까지 지속
- 사용자 이름, 비밀번호, 난스, 난스 횟수, 미래의 요청 공간에 들어갈 인증 세션과 관련된 값들을 기억
- 난스가 만료되면 오래된 Authorization 헤더 정보를 받던지 새 난스 값과 함께 401 반환 가능
  - "stale=true" : 클라이언트가 사용자 이름과 비밀번호를 새로 입력하도록 할 필요 없음

### 13.2.7 사전(preemptive) 인가

- 일반적인 인증에선 각 요청은 트랜잭션이 완료되기 전에 요청/인증 요구 사이클을 필요로 함
- 클라이언트가 다음 난스가 무엇인지 미리 알고 Authorization 헤더를 생성할 수 있으면 요청/인증 요구 사이클 생략 가능
  - 사전 인가는 메시지 횟수를 줄임
- 클라이언트가 새 WWW-Authenticate 인증 요구를 기다리지 않고 올바른 난스를 취득하는 몇 가지 방법
  - 서버가 다음 난스를 Authentication-Info 성공 헤더에 담아서 미리 보냄
  - 서버가 짧은 시간 동안 같은 난스를 재사용하는 것을 허용
  - 클라이언트와 서버가 동기화되어 있고 예측 가능한 난스 생성 알고리즘을 사용
- 다음 난스 미리 생성하기
  - 서버는 Authentication-Info 성공 헤더를 통해 다음 난스 값 미리 제공 가능
  - Authentication-Info: nextnonce:="<난스 값>"
  - 사전인가를 통해 요청/인증 요구 사이클에선 벗어나지만 같은 서버에 다중 요청을 파이프라이닝하는 능력은 쓸모가 없어짐
- 제한된 난스 재사용
  - 만료되기 전 제한적으로 재사용하는 방법
  - 만료인 경우 서버는 401 Unauthorized 인증 요구를 보냄 (stale=true)
  - 공격자의 재사용 공격이 쉬워짐
  - 카운터 증가나 IP 주소 검사와 같이 재전송 공격을 더 어렵게 만들 수 있는 다른 기능도 채택 가능(공격을 불편하게는 만들지만 취약점 제거는 불가능)
- 동기화된 난스 생성
  - 공유된 비밀키에 기반하여 클라이언트와 서버가 순차적으로 같은 난스를 생성할 수 있는 시간적으로 동기화된 난스 생성 알고리즘 사용

### 13.2.8 난스 선택

- 난스의 내용은 불투명하고 구현 의존적
- RFC 2617에서 제안한 난스 공식 : BASE64(타임스탬프 H(타임프탬프":" Etag ":"개인 키))
  - 타임스탬프는 반복 불가능한 값
  - Etag는 요청된 엔터티에 대한 Etag 헤더 값
  - 개인 키는 서버만이 알고 있는 값
- 서버는 클라이언트 인증 헤더를 받아 해시 부분을 재계산하여 확인한다.
  - 일치하지 않거나 오래된 타임스탬프는 요청 거절
- ETag 포함으로 갱신된 리소스에 대한 재요청 방지
  - IP 주소를 난스에 포함하는 것은 프락시 팜을 망치거나 IP 주소를 속일 수도 있어서 X
- 이전에 사용된 난스나 요약을 안 받아들일 수도 있음
  - 일회성 난스나 요약도 사용 가능

### 13.2.9 상호 인증

- RFC 2617에서 클라이언트가 서버를 인증할 수 있도록 다이제스트 인증 확장
  - 클라이언스 난스(c난스)를 제공하여 가능해짐
- 상호 인증은 qop 지시자가 존재할 때는 항상 수행
  - 없다면 수행 X
- 응답 요약은 메시지 본문 정보(A2)가 다르다는 것만 제외하면 요청 요약과 같은 방법으로 계산
  - 응답에는 HTTP 메서드가 없고 요청과 응답의 메시지 엔터티 데이터가 서로 다르기 때문

## 13.3 보호 수준(Quality of Protection) 향상

- qop 필드는 WWW-Authenticate, Authorization, Authentication-Info에 모두 존재 가능
- qop 필드는 클라이언트와 서버가 어떤 보호 기법을 어느 정도 수준으로 사용할 것인지 협상할 수 있게 해줌
- 서버가 우선 WWW-Authenticate 헤더에 qop 옵션을 쉼표로 구분된 목록 형태로 보냄
  - 클라이언트가 그 옵션들 중 지원할 수 있으면서 자신에 요구에 맞는 것을 선택 후 Authorization의 헤더의 qop 필드에 담아 돌려줌
- 모든 현대적인 요약 구현은 qop 옵션을 지원해야 함

### 13.3.1 메시지 무결성 보호

- 무결성 보호가 적용되었을 때 계산되는 H(엔터티 본문)는 메시지 본문의 헤시가 아닌 엔터티 본문의 해시
- 송신자에 의해 먼저 계산되고 수신자에 의해 제거

### 13.3.2 다이제스트 인증 헤더

- 기본 인증, 다이제스트 인증 모두 WWW-Authenticate, Authorization 헤더에 담겨 전달되는 요구와 응답 포함
  - 다이제스트 인증은 여기에 선택적인 Authentication-Info 헤더를 추가
- Authentication-Info 헤더는 3단계 핸드 셰이크를 완성하고 다음번 사용할 난스를 전달하기 위해 인증 성공 후에 전송

## 13.4 실제 상황에 대한 고려

### 13.4.1 다중 인증 요구

- 서버는 한 리소스에 대해 여러 인증을 요구할 수 있음
  - 클라이언트는 반드시 지원 가능한 가장 강력한 인증 메커니즘을 선택해야 함
- 사용자 에이전트는 WWW-Authenticate나 Proxy-Authenticate 헤더 필드 값에 주의를 기울여야 함
  - 인증 요구가 둘 이상 포함되거나 헤더가 둘 이상 제공될 수도 있음
- 서버는 기본 인증을 제한적으로만 사용해야 한다.

### 13.4.2 오류 처리

- 다이제스트 인증의 실패할 시 알맞은 응답은 400 Bad Request
  - 요청의 요약이 맞지 않을 시 실패 사항을 로그에 기록해두는 것이 좋음
- 인증 서버는 반드시 'uri' 지시자가 가리키는 리소스가 요청줄에 명시된 리소스와 같음을 확인해야 함
  - 중간의 프락시가 클라이언트의 요청줄을 변조했을 가능성에 대처

### 13.4.3 보호 공간

- 영역 값은 접근한 서버의 루트 URL과 결합되어 보호 공간을 정의
  - 일반적으로 원 서버에 의해 할당되는 문자열
- 보호 공간은 어떤 자격이 자동으로 적용되는 영역을 결정
- 보호 공간을 계산하는 인증 메커니즘
  - 기본 인증에선 클라이언트는 요청 URI와 그 하위의 모든 경로는 같은 보호 공간에 있는 것으로 가정
  - 다이제스트 인증에선 WWW-Authenticate: domain 필드로 보호 공간을 보다 엄밀하게 정의

### 13.4.4 URI 다시 쓰기

- 프락시는 가리키는 리소스의 변경 없이 다음의 경우로 구문만 고쳐서 URI를 다시 쓰기도 함
  - 호스트 명은 정규화되거나 IP 주소로 대체될 수 있음
  - 문자들은 "%" escape 형식으로 대체될 수 있음
  - 특정 원 서버로부터 가져오는 리소스에 영향을 주지 않는 타입에 대한 추가 속성이 URI의 끝에 붙거나 중간에 삽입될 수 있음

### 13.4.5 캐시

- 공유 캐시가 Authorization 헤더를 포함한 요청과 응답을 받았을 때
  - 원 서버의 응답이 "must-revalidate" Cache-Control 지시자를 포함한 경우 : 그 요청의 헤더를 이용해서 재검사를 수행
  - 원 서버의 응답이 "public" Cache-Control 지시자를 포함한 경우 : 응답 엔터티는 그다음에 오는 임의의 요청에 대한 응답으로 반환될 수 있음

## 13.5 보안에 대한 고려 사항

### 13.5.1 헤더 부당 변경

- 헤더 부당 변경에 대비해 헤더에 대한 디지털 서명이 필요
  - 다이제스트 인증은 WWW-Authenticate와 Authorization 헤더에만 보호 수준에 대한 정보가 담겨있다.

### 13.5.2 재전송 공격

- 어떤 트랜잭션에서 엿들은 인증 자격을 다른 트랜잭션을 위해 사용하는 것
  - GET 및 POST, PUT 요청에 대해서도 예방책 필요
- 난스 값을 반복해서 사용하지 말 것
  - 타임스탬프, 리소스의 ETag, 개인 서버 키를 사용
  - 클라이언트의 IP 사용 X
- 트랜잭션마다 유일한 난스 값을 사용하면 재전송 공격을 완전히 피할 수 있음
  - 부하를 가중시킬 수 있지만 사소한 수준

### 13.5.3 다중 인증 메커니즘

- 다중 인증의 강도는 선택지 중 가장 약한 것과 같음
- 클라이언트가 가장 강력한 인증 제도를 선택할 것
- 가장 강력한 인증 제도만을 유지하는 프락시 서버를 사용
  - 클라이언트가 강력한 인증 제도를 지원할 수 있을 경우

### 13.5.4 사전(dictionary) 공격

- 전형적인 비밀번호 추측 공격
- 트랜잭션 엿듣기, 난스/응답 쌍에 대해 비밀번호 추측 프로그램 사용하기 등..
- 크래킹 하기 어렵도록 상대적으로 복잡한 비밀번호 사용하기 & 괜찮은 비밀번호 만료 정책으로 대비

### 13.5.5 악의적인 프락시와 중간자 공격(Man-in-the-Middle Attack)

- 프락시 중 하나가 악의적이거나 보안이 허술하다면 클라이언트는 중간자 공격에 취약한 상태가 될 수 있음
  - 엿듣기 공격, 인증 제도 선택지를 모두 제거하여 기본 인증과 같은 것으로 대체 등..
- 보통 프락시의 확장 인터페이스를 가로채 수정하여 신뢰도가 낮아짐
- 유일한 실패하지 않는 방어법은 SSL을 사용하는 것
  - 완전히 해결할 순 없어도 클라이언트가 사용자에게 인증의 강도를 보여주고 가장 강력한 인증을 선택할 것

### 13.5.6 선택 평문 공격

- 응답을 계산하기 위해 알려진 키를 사용하는 것
  - 응답의 암호 해독이 쉬워짐
- 미리 계산된 사전 공격
  - 사전 공격과 선택 평문 공격의 조합
  - 미리 결정된 난스와 자주 쓰이는 비밀번호들로 응답의 집합을 생성
  - 난스를 클라이언트에게 전송 후 대응되는 응답을 받았다면 특정 사용자의 비밀번호를 얻은 것
- 자동화된 무차별 대입 공격
  - 많은 컴퓨터를 동원해 주어진 범위에서 간으한 모든 비밀번호를 열거
  - 클라이언트가 c난스 지시자를 사용하여 응답을 생성하게 하여 방어

### 13.5.7 비밀번호 저장

- 다이제스트 인증 비밀번호 파일은 유출되면 모든 문서는 즉각 공격자에게 노출
- 이를 완화하는 몇 가지 방법
  - 비밀번호 파일을 안전하게 보호
  - 호스트와 도메인을 포함한 완전한 영역 이름이 유일함을 보장하고 유출 시 피해 영역 국소화
- 진정한 보안 트랜잭션은 SSL을 통해서만 가능
