# Frame이란?

**Frame**은 OSI 7계층 중 **2계층(Data Link Layer)**에서 사용하는 **데이터 전송 단위**입니다.  
Frame은 전송 계층보다 아래에서, 물리적인 데이터 전송을 책임집니다.

---

# Frame 기반 프로토콜이란?


**Frame 기반 프로토콜**이란, 전송할 데이터가 **명확한 단위(Frame)**로 구분되어 있고,  
이 단위가 **어떤 목적의 메시지인지**를 명시적으로 정의하는 프로토콜입니다.

예시:
- `STOMP`: 텍스트 기반의 Frame (명령 + 헤더 + 바디)
- `WebSocket`: 바이너리 기반의 Frame (Opcode, Payload, Length 등)
- `HTTP/2`: Frame 단위로 메시지를 나눠 전송

---

# 특징

- **명확한 구분**: 데이터의 시작과 끝을 명확하게 구분함
- **의미 부여**: 각 Frame이 어떤 종류의 메시지인지 명시함
- **프레임 헤더**: 대부분의 Frame 기반 프로토콜은 헤더에 메타정보를 포함함

---

# 궁금했던 점 정리

> 대부분의 고급 프로토콜은 결국 내부적으로 Frame 단위를 가지고 있습니다.  
> 다만, "Frame 기반 프로토콜"이라 **굳이 명시적으로** 부르는 경우는 다음과 같은 특성이 있을 때입니다:

- 데이터 단위를 명확히 구분
- 각 단위(Frame)에 의미를 부여
- Frame 구조가 스펙에 명시됨

---

# 정리

| 항목             | 내용                                                        |
|------------------|-------------------------------------------------------------|
| Frame            | 2계층에서의 데이터 전송 단위                                |
| Frame 기반 프로토콜 | 명확한 단위와 구조로 메시지를 나누고, 목적을 정의하는 프로토콜 |
| 대표 예시        | STOMP, WebSocket, HTTP/2 등                                 |




