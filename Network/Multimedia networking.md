## Multimedia networking

애플리케이션 관련

### Multimedia: Audio

아날로그 신호를 전달하는 것 → 이를 전달할 때 디지털 신호로 변환해서 전송 = Sampling

sampling rate가 높을수록 원음과 비슷하다

### Multimedia: Video

이미지의 연속(이때 이미지는 픽셀의 배열, frame)
coding rate가 높을수록 이미지가 선명하며, 낮을수록 뿌연 이미지

CBR(constant bit rate): 고정된 인코딩 rate

VBR(variable bit rate)

### Multimedia networking 유형

1. Streaming, stored 오디오, 비디오(youtube)
2. conversational voice/video over IP(skype)
3. streaming live 오디오, 비디오(실시간)

**Streaming stored video**

frame 연속된 파일을 서버에 저장 → 비디오 전송 → 네트워크 딜레이를 가지며 비디오를 client에게 전송

*jitter: 네트워크 delay가 일정하지 않고 왔다갔다 하는 현상

buffering을 통해 서버에서 받은 비디오에서 일정 시간(tp)을 기다려 전송받은 데이터를 버퍼에 저장해 이후 client로 데이터 보냄(playout)

Transport 시 TCP? UDP?

Youtube와 같은 멀티미디어 서비스는 TCP but 네트워크 환경을 고려함

⇒ DASH: Dynamic, Adaptive, Streaming over HTTP

작은 단위의 chunk(256kB 정도)로 나눔
다음의 chunk를 다른 속도로 인코딩한 버전을 여러개 만들어둠(저장된 장소 url 저장)
⇒ manifest file(url table)
네트워크 환경에 따라 높은 버전과 낮은 버전을 요청하며 데이터 전송

이때 유입이 너무 많은 것을 제어하기 위해 

**“CDN(Content Distribution Network)”** 이용

이를 통해 데이터를 사용자 주위 CDN에 전송시켜둔 후 사용자 요청 시, local CDN에서부터 데이터 전달받음
