---
layout: single
title: "CAN(Controller Area Network) 기초개념 PART2"
---

### CAN Error 개요

* 차량 부품의 오작동은 생명과 직결되므로, 차량 통신의 **신뢰성**이 매우 중요  
→ CAN 통신은 **고장 감지** 및 이에 대한 CAN 컨트롤러의 **대응 메커니즘**을 프로토콜에 정의하여 신뢰성 확보
* Error 예시
    - 데이터를 `1`로 보내야 하는데 잘못 `0`으로 보냄
    - 데이터 포맷(ID, DLC, 데이터 등)을 벗어난 메시지를 보냄
    - 보낸 데이터 `1`을 `0`으로 잘못 해석
* 에러 처리 메커니즘
    - 에러의 종류를 감지
    - 에러 발생 시 동작 정의
    - 반복된 에러 발생 시 추가적인 대응책


### CAN Error 처리 메커니즘
* CAN 프로토콜에서는 **3가지 Error State**를 정의하고 있음  
→ CAN 네트워크를 구성하는 각 제어기(CAN Controller)는 Error State를 가지고 있으며, 이에 따라 오류를 감지하고 관리하는 방식이 달라짐
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-04-CAN-Basics-Part2/2025-12-09-23-31-51.png" style="width:45%;" />
</div>
<p align="center"><strong>CAN Controller Error State</strong></p>

1. **Error Active**
    - **특징**: 제어기에서 **적극적**으로 에러를 감지하고 네트워크에 전달하는 상태
    - **동작**:
        - 메세지 송, 수신 중 에러 감지 시 `TEC`(Transmit Error Counter) 또는 `REC`(Receive Error Counter)를 증가시키며 **Active Error Frame** 송신
        - **Active Error Frame**은 **6개의 Dominant 비트(우선 순위 높음)** 를 포함하여 **Bit Stuffing Rule**을 의도적으로 어기며, Stuff Error를 발생시킴으로써 다른 제어기에 에러를 **무조건** 알림
2. **Error Passive**
    - **특징**: 에러 발생 횟수가 많아져 통신 참여 우선순위가 낮아진 상태
    - **동작**:
        - 에러 감지 시 동일하게 `TEC` 또는 `REC`를 증가시키고 **Passive Error Frame** 송신
        - **Passive Error Frame**은 **14개의 Recessive 비트(우선 순위 낮음)** 로 구성되어 Stuff Error를 발생시키지만, 네트워크의 다른 메시지에 의해 덮일 수 있어 **Error 알림의 강도가 약함**  
        → 에러 발생 횟수가 많아지면 다른 제어기 통신을 위해 알림의 강도를 약하게 전환하고 일정 시간 후에도 계속 에러 발생 시 Bus off 상태로 전환  
        → Error Passive State에서 메시지 송신 후 일정 시간 대기 시간(8 bit time)을 가짐으로써 Error Active State인 제어기 보다 우선순위가 낮아짐  
3. **Bus Off**
    - **특징**: 통신에서 완전히 배제된 상태
    - **동작**:
        - `TEC`이 255를 초과할 경우 버스 오프 상태로 전환되며 CPU에 Bus Off 상태 알림
        - 메시지 송신 및 수신이 불가능
        - 버스 오프 발생 시 **CAN 컨트롤러를 리셋**하여 통신에 재참여하도록 설정
* **State Transition**
    - **Error Active → Error Passive**: TEC 또는 REC ≥ 128
    - **Error Passive → Bus Off**: TEC ≥ 255
    - **정상적인 통신 시 카운터 감소**

### Bit Stuffing Rule
* **정의**: **버스 상**에 **동일한** 비트가 **6개 이상 연속**으로 출력될 수 없음  
→ CAN 컨트롤러에서 5개의 동일 비트를 연속하여 송신하게 될 경우 6번째 비트에 **강제로 반대 비트**를 삽입하여 버스에 출력  
→ 수신자는 5개의 동일 비트를 연속으로 수신하게 될 경우 6번째 삽입된 비트를 무시하고 데이터를 해석
* **의도적인 Bit Stuffing 위반**: Error 발생 시, CAN Controller는 현재 Error State(Active/Passive)에 따라 **Active Error Frame(6 Dominant + 8 Recessive)** 또는 **Passive Error Frame(14 Recessive)** 비트를 강제로 연속 전송하여 네트워크에 Stuff Error 발생 → 다른 제어기에서 에러 검출 인지
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-04-CAN-Basics-Part2/2025-12-09-23-56-49.png" style="width:65%;" />
</div>
<p align="center"><strong>Bit Stuffing Rule</strong></p>

### CAN Error 종류
* CAN 프로토콜에서는 5가지 에러를 정의하고 있으며, CAN Controller가 에러를 감지하면 에러 처리 메커니즘이 동작

1. **Bit Error**
    - **개념**: CAN 컨트롤러는 송신자가 전송한 비트를 Bus에서 다시 읽어 실제 출력값과 비교하여 에러 여부 판단 (ex. 1을 보냈는데 0이 수신되거나, 0을 보냈는데 1이 수신된 경우)  
    - **에러 예외 상황**: 메세지 **ID 영역에서의 Arbitration** 과정 또는 **Ack 영역**에서는 비트 에러가 아닌 정상 동작으로 간주  
    → ex. 우선순위가 낮은 메시지를 보내던 제어기가 우선순위가 높은 메시지를 감지하면 자신이 보낸 메세지 ID 비트값(1)을 다시 읽었을 때 0으로 나타날 수 있지만, 이를 에러로 처리하지 않음

2. **Stuff Error**
    - **개념**: 버스상에서 같은 비트가 6번 연속으로 나타나면 에러로 간주

3. **CRC Error (Cyclic Redundancy Check Error)**
    - **CRC**: 전송 중 **메세지 값 변경 여부**를 검출하기 위한 대표적인 통신 오류 검출 방식으로 CAN을 포함한 다양한 데이터 통신에서 널리 사용됨
    - 송신자는 메시지 데이터를 기반으로 CRC를 계산해 **CRC Field**에 포함하여 전송
    - 수신자는 수신된 메시지로 CRC를 다시 계산하고, 전송된 CRC 값과 비교하여 데이터 변조 여부를 판단
    - **Checksum**: Checksum은 단순한 덧셈 방식으로 에러 검출 능력이 낮지만, CRC는 나눗셈 기반으로 더 높은 정확도로 데이터 변조를 감지

4. **Acknowledge Error**
    - **Acknowledge**: 메세지 송신시, 송신 제어기는 **Acknowledge Slot Bit(ACK)** 를 **1**로 채워 전송하고, 수신 제어기는 CRC값을 확인하여 데이터가 정상임을 확인한 후 **0 (Dominent)** 으로 다시 출력  
    → 송신 제어기가 1을 썼는데 다시 읽었을 때 0이라면, 이는 메시지가 정상적으로 수신되었음을 의미  
    → 수신 제어기에서 CRC 에러를 검출하면 ACK는 1로 유지되며, 송신 제어기는 Acknowledge Slot Bit 1값을 통해 이를 에러로 간주
    - **에러 예외 상황**: 버스에 **수신자가 없으면**(Acknowlege 슬롯 비트 1로 수신) 송신 제어기는 Acknowledge Error로 검출하여(TEC 증가) Error Passive State로 전환되지만 이후 Bus Off State로는 전환되지 않음 (TEC 유지, 에러로 처리하지 않음)
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-04-CAN-Basics-Part2/2025-12-10-01-31-59.png" style="width:55%;" />
</div>
<p align="center"><strong>Acknowledge(ACK)</strong></p>

5. **Form Error**
    - Invalid CAN Message Format

---

### CANFD (Flexible Data-rate)

* **도입 배경**
    - CAN 통신 프로토콜은 크게 Low-Speed CAN, High-Speed CAN, CAN FD로 구분
    - 기존 High-Speed CAN 통신과 같은 네트워크에서 **호환(사용) 가능**하면서 High-Speed CAN의 **낮은 BaudRate**와 **작은 데이터 영역** 의 한계 보완

* **특징**
    - 데이터 영역 크기 및 BaudRate 증가
        - High-Speed CAN: 최대 **8바이트 (1Mbps)**
        - CAN FD: 최대 **64바이트 (8Mbps)**
    - 메세지 전체 영역이 아닌 **데이터 영역**에서만 **빠른 Baudrate**이 적용
        - **Arbitration Baudrate**: 데이터 영역 외 구간에서의 Baudrate
        - **Data Baudrate**: 데이터 영역에서의 Baudrate
    - **샘플링 포인트**도 Baudrate에 따라 별도로 설정 필요
    - 같은 네트워크에서 CAN FD와 High-Speed CAN를 함께 사용 가능하며 이를 위해 High-Speed CAN과 CAN FD를 모두 지원하는 트랜시버가 필요
        - ex. TJA1043 트랜시버
    - 데이터 영역의 Baudrate에 따라 **종단 저항 크기가 달라질 수 있음** (통신속도에 따른 임피던스 따라 저항 크기 결정)
    - DLC(Data Length Code)는 메세지내 데이터 영역의 크기를 나타내는 필드이며 **4bit로 표현**  
    → 4bit DLC만으로는 CAN FD의 실제 데이터 길이(최대 64바이트)를 표현할 수 없으므로 CAN FD에서는 확장된 데이터 길이를 표현할 수 있도록 DLC값을 아래와 같이 재정의하여 사용
    - 이외에도 CAN FD는 CRC 필드 계산 방식 및 길이, Control 필드 구성 등 메시지 포맷이 High-Speed CAN과 일부 다르게 정의
    <br>
    <div style="text-align:center;">
        <img src="../images/2025-12-04-CAN-Basics-Part2/2025-12-10-10-39-40.png" style="width:55%;" />
    </div>
    <p align="center"><strong>CANFD DLC</strong></p>

* ※ **Low-Speed CAN**
    - 최대 125kbps Baudrate 지원
    - CAN HIGH와 CAN LOW 전선 중 하나가 끊어져도 통신 가능
    - 버스 양 끝단에 120옴 저항을 사용하지 않고, 각 제어기 CAN HIGH, CAN LOW 전선에 개별 저항이 달림
    - 현재 **거의 사용되지 않음**

### CAN DB & CAN DBC
* **CAN DB**(Communication Matrix, K-Matrix): CAN 네트워크와 관련된 필수 정보를 포함한 데이터베이스
* **CAN DBC**: 벡터(Vector)라는 독일 회사에서 만든 **CAN DB를 저장하는 파일 형식(확장자)**
    - 벡터(Vector)사의 **CANdb++ Editor**를 활용하여 CAN DBC 작성 가능
* **포함 주요 정보**
    - **메시지 ID**: 네트워크에서 사용되는 각 메시지의 고유 ID
    - **메시지 이름**: 각 메시지의 식별 이름
    - **데이터 길이**: 메시지 데이터 영역의 길이
    - **송수신 제어기 정보**:
        - 메시지를 송신하는 제어기
        - 메시지를 수신하는 제어기
    - **메시지 전송 방식**:
        - 주기적으로 메시지를 전송하는 방식 (Send Type: Periodic)
        - 메세지 내 특정 시그널값이 변경될 때만 메시지를 전송하는 방식 (Send Type: On Change)
    - **메시지 내 시그널 정보**:
        - 시그널의 위치, 크기, 팩터, 오프셋 정보
        - 데이터 영역에서 시그널의 배치와 크기
    - 그외 **CAN 프로토콜 정의**(High-Speed CAN, CAN FD), **Baudrate**, **Sampling Time** 등을 포함

### CANoe
* CANoe는 Vector사가 개발한 소프트웨어로, CAN 네트워크 분석, 시뮬레이션, 검증 작업에 활용
* PC에는 CAN Controller가 내장되어 있지 않아 CAN 통신이 불가능하며, CANoe와 같은 네트워크 인터페이스 장비를 통해 CAN 네트워크와 PC간 통신을 가능하게 함
* 주요 기능
    - **CAN 메시지 로깅 및 분석**: 네트워크에서 주고받는 메시지를 실시간으로 기록하고 분석 (DBC 파일 임포트)
    - **가상 제어기 시뮬레이션**: 가상의 제어기(네트워크)를 만들어 CAN 메시지를 송신해 제어기 테스트
    - **버스 로드 측정**
    - **에러 프레임 감지**
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-04-CAN-Basics-Part2/2025-12-10-11-17-03.png" style="width:65%;" />
</div>
<p align="center"><strong>CANoe</strong></p>