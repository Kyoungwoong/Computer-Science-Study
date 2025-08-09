# 💻 Transport Layer (전송 계층)


## Transport Layer란?

- TCP/IP 4계층 중 **2번째 계층**, OSI 7계층 중 **4번째 계층**
- 호스트 간 프로세스 간의 논리적 통신을 담당
- 실제 데이터 전송은 하위 계층(네트워크 계층 이하)에서 수행하며, 전송 계층은 **신뢰성, 흐름 제어, 혼잡 제어 등 고급 기능** 제공
- **데이터 단위: Segment (TCP), Datagram (UDP)**


## 주요 기능 및 역할

### Multiplexing / Demultiplexing

- 여러 프로세스가 전송 계층을 공유할 수 있도록 포트 번호 기반 식별
- ex) 1개의 호스트에 여러 클라이언트(브라우저, 메일 등)

### 신뢰성 있는 데이터 전송 (TCP)

- 손실, 오류, 순서 뒤바뀜 등을 감지하고 복구
- Sequence number, ACK, Retransmission 사용

### 흐름 제어 (Flow Control)

- 수신 측의 처리 속도에 맞춰 송신 속도 조절
- 수신 버퍼가 오버플로우 되지 않도록 `rwnd` 값으로 제어

### 혼잡 제어 (Congestion Control)

- 네트워크 혼잡 발생 시 송신 속도 조절
- `cwnd`를 통해 혼잡 상태를 탐지하고 대응 (AIMD, Slow Start 등)

## 3. UDP (User Datagram Protocol)

### 특징

- 비연결형, 신뢰성 없음, 순서 보장 없음
- 헤더가 간단 (8 byte)
- 빠르고 가벼움, 오버헤드 최소화
- **용도: DNS, DHCP, 스트리밍, 실시간 게임 등**

### 헤더 구조

| Source Port | Destination Port |
|     Length  |     Checksum     |
|     → Payload (Application Data) |


### Checksum

- 16-bit 정수로 나눈 후 1의 보수 합 (one's complement sum)
- 오류 검출 기능

## TCP (Transmission Control Protocol)

### 특징

- **연결 지향형 (3-way handshake)**
- **신뢰성 보장** (순서 보장, 손실 복구, 중복 제거)
- **흐름 제어**, **혼잡 제어**
- **Full-duplex 통신 가능**
- **Byte Stream 기반 전송** (메시지가 아닌 바이트 단위 전송)

### TCP 세그먼트 구조

```
| Source Port | Destination Port |
| Sequence Number               |
| Acknowledgment Number         |
| Header Len | Flags | Window Size |
| Checksum   | Urgent Pointer     |
| Options (optional)            |
| → Payload (Application Data) |
```

### 주요 플래그

- SYN: 연결 요청
- ACK: 응답 확인
- FIN: 연결 종료
- RST: 연결 재설정

### 시퀀스 번호 / ACK 번호

- 시퀀스 번호: 해당 세그먼트의 첫 바이트의 번호
- ACK 번호: **다음에 기대하는 바이트 번호**

### Cumulative ACK

- ACK 번호보다 작은 시퀀스 번호의 모든 데이터가 수신되었음을 의미


## 신뢰성 보장 메커니즘

### Checksum

- 데이터 오류 감지

### Sequence Number / ACK

- 데이터 순서 파악 및 손실 감지

### Retransmission

- Timeout or 중복 ACK 기반 재전송

### Fast Retransmit

- 중복 ACK 3회 수신 시 타임아웃 기다리지 않고 재전송


## 흐름 제어 (Flow Control)

- 수신자 버퍼 용량(rwnd)을 TCP 헤더에 포함
- 송신자는 min(cwnd, rwnd)만큼만 데이터를 전송
- → 수신 측 과부하 방지



## 혼잡 제어 (Congestion Control)

### AIMD (Additive Increase, Multiplicative Decrease)

- ACK 수신 시 cwnd += 1
- 손실 시 cwnd /= 2

### Slow Start

- 초기에는 cwnd = 1 → 2배씩 
- ssthresh를 기준으로 Slow Start → Congestion Avoidance로 전환

### Fast Recovery

- 중복 ACK 3번 수신 → cwnd 절반 감소 후 선형 증가 유지


## TCP 연결 관리

### 3-Way Handshake

1. SYN (Client → Server)
2. SYN-ACK (Server → Client)
3. ACK (Client → Server)

### 4-Way Termination

1. FIN (Client)
2. ACK (Server)
3. FIN (Server)
4. ACK (Client)

## Multiplexing / Demultiplexing

### UDP 소켓 식별자: (Destination IP, Destination Port)

- 간단한 구조

### TCP 소켓 식별자: (Source IP, Source Port, Destination IP, Destination Port)

- 클라이언트마다 다른 소켓으로 연결



## TCP의 신뢰성 메커니즘

- **Checksum**: 비트 오류 감지
- **Sequence Number**: 누락된 데이터 및 순서 판별
- **Retransmission**: ACK 또는 timeout 기반 재전송

## Automatic Repeat reQuest (ARQ)

### Stop-and-Wait ARQ

- 한 번에 하나의 패킷 전송 → ACK 수신 대기
- Timeout 발생 시 재전송


## TCP 재전송 시나리오

1. ACK 손실
2. 중복 ACK
3. Premature timeout
4. Cumulative ACK 활용


## TCP Fast Retransmit

- 3 중복 ACK 수신 시 → 손실로 간주하고 즉시 재전송
- 타임아웃 발생 전 재전송하여 속도 개선


## Sliding Window & Flow Control

### 문제점

- Stop-and-wait 방식: 링크 사용률 저조 (특히 BDP 클 경우)

### 해결: Sliding Window

- 다수의 세그먼트를 "in-flight" 상태로 유지
- 수신자 제어 기반 → Flow Control


## TCP Flow Control 메커니즘

- 수신자는 가용 버퍼 공간 (`rwnd`)을 TCP 헤더에 포함
- 송신자는 `rwnd` 만큼만 미확인 데이터 전송
- 수신 버퍼 오버플로 방지


## TCP Connection Management

### TCP 3-Way Handshake

1. **SYN**: 클라이언트가 연결 요청
2. **SYN-ACK**: 서버가 수락 응답
3. **ACK**: 클라이언트가 확인 → 연결 성립

### 연결 종료

- FIN → ACK 기반
- 양방향 종료 필요

## SYN Flood 공격 & 방어

### 공격 방식

- ACK 없이 SYN만 지속 전송 → 서버 자원 고갈
- Half-open connection 발생

### 방어: SYN Cookies

- SYN 수신 시 실제 연결 생성 안 함
- 해시 기반 시퀀스 번호로 응답
- ACK 수신 시 유효성 확인 후 연결 확정



## 로스와 딜레이가 어떻게 생기나

- 패킷 도착률이 링크 용량 초과 → 큐에 대기 → 지연/손실 발생
- **Queueing Delay**는 상황에 따라 달라짐


## Packet Loss와 혼잡

- 버퍼 부족 시 패킷 손실 발생
- TCP는 손실 감지 후 재전송
- 손실이 많아질수록 처리량 감소


## 혼잡 시나리오 1: 무한 버퍼

- Throughput = R/2
- 지연만 증가, 손실은 없음

## 혼잡 시나리오 2: 유한 버퍼 + 재전송

- 손실 발생 시 재전송 → 처리량은 같지만, 작업량 증가
- 불필요한 재전송 발생 가능 → “wasted bandwidth”
- 혼잡 → **Congestion Collapse** 발생 가능

## TCP Congestion Control

- **End-to-End 방식**
- 네트워크 상태에 따라 송신률(cwnd) 조절
  - ACK 수신 → 증가
  - Timeout / Duplicate ACK → 감소

### Congestion Window (cwnd)

- 미확인(in-flight) 바이트의 최대 수
- 송신 윈도우: `min(cwnd, rwnd)`
- 전송률 ≈ `cwnd / RTT`


## TCP Congestion Control 알고리즘

### AIMD (Additive Increase, Multiplicative Decrease)

- 성공 시: `+1 MSS per RTT`
- 손실 시: `cwnd /= 2`

### Slow Start

- 초기에 cwnd = 1 MSS → 2배씩 증가
- 손실 발생 시 → ssthresh 설정 후 cwnd=1로 초기화

### Congestion Avoidance

- cwnd >= ssthresh 이후 → 선형 증가

### Fast Recovery (TCP Reno)

- **Triple Duplicate ACK** → cwnd /= 2 (ssthresh 설정), 선형 증가 계속


## TCP 상태 전이 예시

| 이벤트             | cwnd 변화            | 설명                          |
|------------------|---------------------|-------------------------------|
| Timeout          | cwnd = 1 MSS        | Slow Start 재시작             |
| 3 Duplicate ACKs | cwnd /= 2           | Fast Retransmit / Recovery    |
| ACK 수신         | cwnd += 1 MSS/RTT   | AIMD에 따라 증가              |


## TCP CUBIC (2008)

- AIMD보다 더 효율적 전송률 증가
- **Wmax** 기준으로 느리게 접근
- 리눅스 기본 TCP 알고리즘


## Bottleneck Link

- 혼잡 대부분은 병목 링크에서 발생
- RTT 증가 → 혼잡의 신호
- Throughput은 일정 수준 이상 증가하지 않음


## Delay-Based Congestion Control

- RTT 증가 없이 처리량 증가 목표
- BBR (2016) → Google이 배포한 delay-based TCP

## ECN: Explicit Congestion Notification

- 네트워크에서 명시적으로 혼잡 신호 전달
- IP 헤더의 ECN 비트를 이용해 수신자 → 송신자에게 혼잡 알림
- TCP ACK에서 ECE 비트 사용


## QUIC (Quick UDP Internet Connections)

- Application-layer에서 동작하는 신뢰성 프로토콜 (over UDP)
- **HTTP/3의 기반**
- 1-RTT handshake: connection + TLS 보안 + congestion control
- 중복 재전송 없이 parallel stream 처리 → HOL blocking 방지
- TCP 대비 빠른 연결 설정 및 낮은 지연

## 🔍 TCP에서 신뢰성 보장 방법


## TCP의 신뢰성 메커니즘

- **Checksum**: 비트 오류 감지
- **Sequence Number**: 누락된 데이터 및 순서 판별
- **Retransmission**: ACK 또는 timeout 기반 재전송

## Automatic Repeat reQuest (ARQ)

### Stop-and-Wait ARQ

- 한 번에 하나의 패킷 전송 → ACK 수신 대기
- Timeout 발생 시 재전송


## TCP 재전송 시나리오

1. ACK 손실
2. 중복 ACK
3. Premature timeout
4. Cumulative ACK 활용


## TCP Fast Retransmit

- 3 중복 ACK 수신 시 → 손실로 간주하고 즉시 재전송
- 타임아웃 발생 전 재전송하여 속도 개선


## Sliding Window & Flow Control

### 문제점

- Stop-and-wait 방식: 링크 사용률 저조 (특히 BDP 클 경우)

### 해결: Sliding Window

- 다수의 세그먼트를 "in-flight" 상태로 유지
- 수신자 제어 기반 → Flow Control


## TCP Flow Control 메커니즘

- 수신자는 가용 버퍼 공간 (`rwnd`)을 TCP 헤더에 포함
- 송신자는 `rwnd` 만큼만 미확인 데이터 전송
- 수신 버퍼 오버플로 방지


## TCP Connection Management

### TCP 3-Way Handshake

1. **SYN**: 클라이언트가 연결 요청
2. **SYN-ACK**: 서버가 수락 응답
3. **ACK**: 클라이언트가 확인 → 연결 성립

### 연결 종료

- FIN → ACK 기반
- 양방향 종료 필요

## SYN Flood 공격 & 방어

### 공격 방식

- ACK 없이 SYN만 지속 전송 → 서버 자원 고갈
- Half-open connection 발생

### 방어: SYN Cookies

- SYN 수신 시 실제 연결 생성 안 함
- 해시 기반 시퀀스 번호로 응답
- ACK 수신 시 유효성 확인 후 연결 확정



## 로스와 딜레이가 어떻게 생기나

- 패킷 도착률이 링크 용량 초과 → 큐에 대기 → 지연/손실 발생
- **Queueing Delay**는 상황에 따라 달라짐


## Packet Loss와 혼잡

- 버퍼 부족 시 패킷 손실 발생
- TCP는 손실 감지 후 재전송
- 손실이 많아질수록 처리량 감소


## 혼잡 시나리오 1: 무한 버퍼

- Throughput = R/2
- 지연만 증가, 손실은 없음

## 혼잡 시나리오 2: 유한 버퍼 + 재전송

- 손실 발생 시 재전송 → 처리량은 같지만, 작업량 증가
- 불필요한 재전송 발생 가능 → “wasted bandwidth”
- 혼잡 → **Congestion Collapse** 발생 가능

## TCP Congestion Control

- **End-to-End 방식**
- 네트워크 상태에 따라 송신률(cwnd) 조절
  - ACK 수신 → 증가
  - Timeout / Duplicate ACK → 감소

### Congestion Window (cwnd)

- 미확인(in-flight) 바이트의 최대 수
- 송신 윈도우: `min(cwnd, rwnd)`
- 전송률 ≈ `cwnd / RTT`


## TCP Congestion Control 알고리즘

### AIMD (Additive Increase, Multiplicative Decrease)

- 성공 시: `+1 MSS per RTT`
- 손실 시: `cwnd /= 2`

### Slow Start

- 초기에 cwnd = 1 MSS → 2배씩 증가
- 손실 발생 시 → ssthresh 설정 후 cwnd=1로 초기화

### Congestion Avoidance

- cwnd >= ssthresh 이후 → 선형 증가

### Fast Recovery (TCP Reno)

- **Triple Duplicate ACK** → cwnd /= 2 (ssthresh 설정), 선형 증가 계속


## TCP 상태 전이 예시

| 이벤트             | cwnd 변화            | 설명                          |
|------------------|---------------------|-------------------------------|
| Timeout          | cwnd = 1 MSS        | Slow Start 재시작             |
| 3 Duplicate ACKs | cwnd /= 2           | Fast Retransmit / Recovery    |
| ACK 수신         | cwnd += 1 MSS/RTT   | AIMD에 따라 증가              |


## TCP CUBIC (2008)

- AIMD보다 더 효율적 전송률 증가
- 리눅스 기본 TCP 알고리즘


## Bottleneck Link

- 혼잡 대부분은 병목 링크에서 발생
- RTT 증가 → 혼잡의 신호
- Throughput은 일정 수준 이상 증가하지 않음


## Delay-Based Congestion Control

- RTT 증가 없이 처리량 증가 목표
- BBR (2016) → Google이 배포한 delay-based TCP

## ECN: Explicit Congestion Notification

- 네트워크에서 명시적으로 혼잡 신호 전달
- IP 헤더의 ECN 비트를 이용해 수신자 → 송신자에게 혼잡 알림
- TCP ACK에서 ECE 비트 사용


## QUIC (Quick UDP Internet Connections)

- Application-layer에서 동작하는 신뢰성 프로토콜 (over UDP)
- **HTTP/3의 기반**
- 1-RTT handshake: connection + TLS 보안 + congestion control
- 중복 재전송 없이 parallel stream 처리 → HOL blocking 방지
- TCP 대비 빠른 연결 설정 및 낮은 지연

