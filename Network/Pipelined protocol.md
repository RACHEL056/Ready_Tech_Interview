RDT는 한 패킷만 전송하게 되므로 비효율적 ⇒ Pipelined protocols (한번에 여러 패킷을 보내자)

    ** go-Back-N, selective repeat

### Go-Back-N

Window(N) = 얼마 만큼의 패킷을 한번에 보내는 양(즉각적 피드백 X), 버퍼에 들고있는 양

ACK(s) = 누적/축적된 ACK들  ex) ACK11 → 11번 패킷까지 잘 받았고 12번 패킷을 기다리고 있음

Reciever은 정말 멍청하다! = 내가 받은 패킷 넘버 이후의 패킷 넘버만을 기다린다

따라서 순차적으로 들어온 패킷만 저장하고 순서에 벗어난 경우 모두 discard

하지만 계속 이미 보내진 패킷들을 버리고 문제 지점부터 다시 모든 패킷을 보내는 것은 비효율적 → 도착을 안한 패킷만 다시 받을 수 없을까?

### Selective Repeat

재전송 할 때 안 보내진 것만 selective하게 보내주는 방법

모든 sequence #에 타이머를 걸어줌

Reciever → 버퍼에 순차적인 패킷이 아니더라도 저장하는 역할

→ Dilemma

    최소한의 시퀀스 넘버를 사용하는 범위는 어떻게 정할 수 있을까

    윈도우가 N일때 시퀀스 넘버의 최소 사이즈는 얼마일까? 약 2배
