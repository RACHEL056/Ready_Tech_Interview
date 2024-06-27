## Network Security

요구조건

1. confidentiality: 주고받는 메세지를 제 3자가 알 수 없다
2. authentication: 내가 말하는 대상이 신뢰성이 있다
3. message integrity: 메세지가 변경되지 않고 메세지를 받는다
4. access and availability: 서비스를 제공하는 사람은 24시간 내내 제공이 가능해야 한다

### Public key cryptography 공개키 암호화

Alice가 Bob에게 메세지를 보낼 때,

Bob의 public key(공개키)를 이용해 암호화한 후, Bob이 Bob의 개인키로 이를 복호화해 메세지 확인

그렇다면 공개키가 정말 Bob의 공개키인지를 확신해야함

인증기관의 Digital Signiture이 담겨진 Digital 인증서를 확인(안에 공개키가 담겨져 있음)

### Authentication

Alice가 Bob에게 메세지를 보낼 때,

Bob이 challenge(랜덤 넘버)를 줘서 public키로 암호화해 보내서 인증

### Integrity

메세지가 무결한가? 

Digital signature ⇒ 메세지를 보낼 때 밥의 비밀키로 암호화해서 보냄, 이후 공개키로 이를 복호화

Message digests ⇒ 메세지가 너무 길 경우 해시 함수를 이용해서 메세지 용량을 줄임

### SSL: Securing TCP connection

TCP에서 암호화하는 기능이 없기 때문에 HTTP에서 TCP로 내려올 때 암호화 ⇒ SSL Library

SSL Library를 통해 message를 암호문으로 변환

이는 새로운 계층이 아니라 Application 계층 안에 속해있음

⇒ **TLS** (Transport Layer Security)

⇒ HTTPS - HTTP와 같은 format의 메세지지만 SSL 소켓을 지난 버전 (HTTP + SSL)

통신 준비 동작

1. Handshake
TCP 연결 이후 공개키 인증서(인증 기관의 키와 본인의 공개키 포함)를 보내줌
이에 따라 보내는 사람은 비밀키를 만들어 받는 사람의 공개키로 암호화해 전송
    
    송신자의 비밀키는 4 종류의 키를 만듬
    
    1. C → S로 가는 데이터 암호화 키
    2. C → S로 갈 때 메세지의 integrity check 메세지 생성 키
    3. S → C로 가는 데이터 암호화 키
    4. S → C로 갈 때 메세지의 integrity check 메세지 생성 키
2. key derivation
3. data transfer
MAC ⇒ H(data | K c-s | sequence) 해시 함수를 이용해 만듬
기존에 HDR + data(application 데이터) 형태 → data 부분을 Length + Data + MAC 형태로 변형
attacker은 data와 MAC 부분을 보지 못함
4. connection closure

### Firewalls

한 네트워크의 게이트웨이에 위치해 외부에서 오는/나가는 패킷 감시
