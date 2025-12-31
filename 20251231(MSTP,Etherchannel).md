
## MSTP



<img width="735" height="310" alt="image" src="https://github.com/user-attachments/assets/b540704c-db6c-4bd3-a825-006efdf2912d" />


SW1(config)#no vlan 2-20<br/>

SW1(config)#int range g0/0-1<br/>
switchport trunk en dot1q <br/>
switchport mode trunk <br/>
switchport trunk allowed vlan 10,20,30,40<br/>
switchport nonegotiate <br/>

vtp DOmain A<br/>
vtp mode server <br/>
vlan 10,20,30,40<br/>
vtp domain A<br/>
vtp mode client<br/>


VLAN 10,30 → MST1 → SW1,SW3 사이 구간을 사용<br/>
VLAN 20,40 → MST2 → SW2,SW3 사이 구간을 사용<br/>

SW1,SW2,SW3 동일하게 적용<br/>

spanning-tree mode mst<br/>
spanning-tree mst configuration <br/>
name A<br/>
instance 1 vlan 10,30<br/>
instance 2 vlan 20,40<br/>
revision 1<br/>

SW1(config)#spanning-tree mst 1 priority 0<br/>
SW1(config)#spanning-tree mst 2 priority 4096<br/>
<br/>
SW2(config)#spanning-tree mst 1 priority 4096<br/>
SW2(config)#spanning-tree mst 2 priority 0 <br/>


## Etherchannel

이더채널(포트채널), 링크통합 

복수개의 물리적인 링크를 묶어서 논리적으로 하나의 링크로 동작하게 하는 기술이다.

STP, RSTP, MSTP → 루핑을 차단해서 네트워크를 보호, L2 부하분산, 이중화를 구현

항상 차단포트가 발생된다.  이중화의 장점은 유지가 되지만 효율성이 떨어진다.

<img width="312" height="64" alt="image" src="https://github.com/user-attachments/assets/ffab97d1-ab2d-41ab-8a7f-ff69509f434b" />

최대 8개의 논리적인 링크를 묶을 수 있다.

conf t<br/>
int range g0/1,g0/2<br/>
channel-group 1 mode on<br/>
(이제 int p1 가능)<br/>

sh etherchannel summary

int p1<br/>
no switchport<br/>
ip address 1.1.12.1 255.255.255.0<br/>

<img width="343" height="235" alt="image" src="https://github.com/user-attachments/assets/298dff11-9207-48d9-9c06-0aa14a92a3be" />

#### 1. S1-S2간의 2개의 링크를 하나의 통합 링크로 구성하라. 링크 통합을 위한 협상 프로토콜을 사용하지 않는다.

1.1 S1-S2 (Static Mode)<br/>
협상 프로토콜을 사용하지 않으므로 mode on을 사용합니다.<br/>
interface range g0/0 - 1<br/>
channel-group 1 mode on<br/>

#### 2. S1-S3간의 링크를 표준 프로토콜을 통해 하나의 논리적인 링크로 구성하라. S1은 협상에 응답만 하고, S3가 협상을 적극적으로 시도한다.

2.1 S1-S3 (LACP - 표준 프로토콜)<br/>
표준 프로토콜은 LACP입니다. S3가 시도(active), S1이 응답(passive)하는 설정입니다.<br/>

SW1<br/>
interface range g0/2 - 3<br/>
channel-group 2 mode passive<br/>

SW3<br/>
interface range g0/2 - 3<br/>
channel-group 2 mode active<br/>

#### 3. S1-S4간의 링크를 시스코 전용 프로토콜을 통해 하나의 논리적인 링크로 구성하라. S1은 협상에 응답만 하고, S3가 협상을 적극적으로 시도한다.

3.1 S1-S4 (PAgP - 시스코 전용 프로토콜)<br/>
시스코 전용은 PAgP입니다. S4가 시도(desirable), S1이 응답(auto)하는 설정입니다. (질문에서 S3라고 하셨지만 흐름상 S4로 해석했습니다.)

SW1<br/>
interface range g1/0 - 1<br/>
channel-group 3 mode auto<br/>

SW4<br/>
interface range g1/0 - 1<br/>
channel-group 3 mode desirable<br/>


#### 4. S2-S4간의 링크는 하나의 논리적인 L3링크로 구성하여 제시한 IP로 설정하라.

4.1 S2-S4 (Layer 3 EtherChannel)<br/>
스위치 포트를 라우터 포트처럼 사용하는 설정입니다. no switchport 명령어가 핵심입니다.<br/>

SW2

interface range g0/2 - 3<br/>
no switchport               # 물리 포트에서 L2 기능 끄기<br/>
channel-group 2 mode on     # (또는 프로토콜 지정 가능)<br/>
exit<br/>

interface port-channel 2<br/>
no switchport               # 가상 인터페이스에서 L2 기능 끄기<br/>
ip address [S2_IP] [Subnet_Mask]<br/>

SW4

interface range g0/2 - 3<br/>
no switchport<br/>
channel-group 2 mode on<br/>
exit<br/>

interface port-channel 2<br/>
no switchport<br/>
ip address [S4_IP] [Subnet_Mask]<br/>





































































































































































