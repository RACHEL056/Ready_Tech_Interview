## Network layer

TCP에서의 segment들이 어떻게 전달이 될까? 배송에 관해서 공부하는 레이어

IP 프로토콜로 라우터의 개입으로 이루어진다

⇒ 라우터는 네트워크 레이어까지 존재한다(physical, link, network layer)

주요기능

1. Routing   2. Forwarding

### IP: Internet Protocol

### IP Adress(IPv4)

34 bit 숫자, 인터페이스를 식별하는 값(호스트, 라우터 등등에 존재…)

IP 주소를 배정하는 방법 ⇒ 계층화(Network portion + Host portion)

Network(prefix, subnet ID, network ID) - 24bit

Host - 8bit

*subnet mask → 네트워크 크기를 알 수 있음

과거

IP 주소에 class를 나눠 두었음

- Class A - prefix /8 ⇒ host 24bit
- Class B - prefix /16 ⇒ host 16bit
- Class C - prefix /24 ⇒ host 8bit

하지만 유연성이 떨어져 Classless Inter-Domain Routing(CIDR) 사용

- 서브넷 부분이 동적인 길이를 가짐
- 주소 형태: a.b.c.d/x, 이때 x는 서브넷 주소 공간의 비트 수

### Longest Prefix Match Forwarding

여러 매칭되는 IP 주소 중 가장 길게 유사한 prefix 가진 것끼리 매칭해주는 것

### Subnet

- 라우터들을 거치지 않고 접근 가능한 호스트 집합
- 같은 prefix를 가진 집합

**라우터는 여러개의 서브넷에 속한 걸쳐진 존재로, 여러 서브넷을 가짐

이후 인터넷이 상용화되면서 IPv4 주소공간이 부족해져 IPv6를 만듬

but 현재까지 IPv4주소를 사용

### ⇒ NAT(Network Address Translation) 방식

외부에서는 접근 불가능한 local address를 사용(local address는 다른 네트워크에서 재사용 가능)

gateway router를 통해 source 주소, port #(기존 프로세스 찾아가는 주소)를 바꿈

이 경우 port #가 host를 찾아가는 용도로 사용되기 때문에 서버를 사용할 수 없음 

문제점

디자인 상 문제점(layering violation) → layer가 무너짐, IP packet 및 data를 고치게 된다

서버 이용 불가능 → port#를 찾아가는 용도로 사용되어 프로세스에 접근할 수 없음

### Dynamic Host Configuration Protocol(DHCP)

“Host Configuration” → 호스트를 식별해준다 !

어디로 이동하던지 간에 네트워크 접속 시 동적으로 호스트를 식별해준다.

따라서 rental 해주는 느낌으로 IP를 할당한 다음 일정 시간 이후 다시 회수한다.

↔ Static IP: 고유/고정 IP 

**DHCP client-server scenario**

client→server: DHCP discover(broadcast-해당 포트넘버를 가지는 DHCP 서버만 응답, 나머지는 drop)

server→client: DHCP offer(broadcast- transaction ID와 포트넘버로 요청한 client만 응답)

client→server: DHCP request(broadcast- offer을 했지만 선택되지 않은 DHCP 서버들에게 간접적으로 알림)

server→client: DHCP ACK

*GWR(GateWay Router) → NS, DHCP server, NATs, firewall과 같은 기능을 한다

### IP header - identifier(ID) & flag & fragment offset

네트워크 링크에는 MTU(Maximum Transfer Size)가 있는데, 이때 MTU 보다 큰 packet이 들어왔을때?

⇒ fragmentation을 진행해 나중에 reassemble 된다

ID를 통해 동일한 packet 확인

fragflag로 뒤에 fragmentation 패킷 유무(1이면 존재 0 이면 X)

offset으로 순서 확인(/8로 필드 수를 줄임)
