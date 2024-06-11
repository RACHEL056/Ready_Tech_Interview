## Wireless and Mobile Network 무선 / 모바일 네트워크

“선이 없는 것” = wireless

“한 네트워크에서 다른 네트워크로 이동, AP가 변경” = mobility 이동통신

### Wireless

첫 hop이 무선인 경우, 이후의 라우터간의 연결은 유선연결(link layer)

특징

- cable로 신호가 전달되는 유선과 달리, (외부의 noise에 강함, 신호 방해 X)
    
    무선은 Open된 매체로 거리가 멀수록 신호 세기가 줄어듬(반비례)
    
- hidden terminal problem
    
    전송 반경에 있지 않은 매체는 다른 매체의 존재를 알지 못함
    
- signal attenuation
    
    전송 반경에 있지 않은 매체에게 신호 전달을 할 수 없음
    
    내가 말하는 신호는 매우 크고 다른 매체의 소리는 상대적으로 작게 들림 ⇒ collision detection 어려움
    

**IEEE 802.11 Wireless LAN “Wi-Fi”**

Wi-Fi - Wireless Fidelity 무선이지만 유선에 가깝게 목표

구조 

무선 host와 AP(base station)과 소통

BSS(Basic Service Set) - 무선 호스트, AP, ad hoc mode

multiple access

유선의 CSMA/CD는 ACK가 존재 X (충돌이 나지 않음 = 데이터가 잘 전달됨)

무선에서는 충돌이 났는데 감지하지 못함 ! → ACK를 통해 전달 유무를 알려줌

*TCP에서는 end-to-end이라면, 위의 ACK는 링크 레이어 ACK

⇒ CSMA/CA

1. sender(수신자)는 DIFS(정해진 시간)만큼 채널이 조용하면 데이터 전송
2. receiver는 SIFS(특정 정해진 시간) 이후에 수신이 잘 되었으면 ACK를 보냄

만약 채널이 busy할 경우 기다렸다가, 이후 random backoff time 이후에 데이터 전송

? CSMA/CD와 어떤 차이가 있을까

CSMA/CD에서 충돌이 발생 시 즉각적으로 멈춤

CSMA/CA 경우 충돌 감지가 안되기 때문에 데이터가 끝까지 전달될 때까지 멈추지 않음

CSMA/CA 충돌에 대한 대응이 전혀 X 충돌이 일어날 경우 많은 양의 데이터 피해가 발생함

⇒ RTS/CTS exchange

RTS - ready to send  CTS - clear to send

1. AP에게 RTS control frame을 보냄
이때 충돌이 일어날 경우 noise로 판단한 후 ap는 작동X, 충돌이 발생 안할 경우 CTS를 보냄
2. CTS를 받을 경우 데이터를 전송
3. 데이터를 다 받았을 경우 ACK 전송
