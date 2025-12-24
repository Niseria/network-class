

## STP를 이용한 부하분산(load blancing) 

### 부하분산이란
여러개의 경로로 트래픽을 분산시켜서 효율성과 안정성을 높이는 방법이다.<br/>
L3 방식: 라우팅을 이용한 부하분산<br/>
L2 방식: VLAN, STP를 이용한 부하분산<br/>

<img width="1084" height="526" alt="image" src="https://github.com/user-attachments/assets/4641f1a1-c8ab-4dbb-b27c-f2fc72473f34" />



#### 1단계 문제
1. 스위치 사이에 트렁크 설정
- DOT1Q
- allowed vlan 10,20
- native vlan 999
- dtp off

>interface GigabitEthernet0/0<br/>
>switchport trunk allowed vlan 10,20<br/>
>switchport trunk encapsulation dot1q<br/>
>switchport trunk native vlan 999<br/>
>switchport mode trunk<br/>
>switchport nonegotiate<br/>


2. VTP 설정
- Domain ccna
- password cisco
- SW1: Server / 나머지: client
- Server에서 vlan 10,20 생성

>vtp domain ccna<br/>
>vtp password cisco<br/>
>vtp mode server<br/>
>vlan 10<br/>
>vlan 20<br/>

3. 각 스위치에서 port 별 access-mode 설정, 해당 vlan 할당

>switchport mode access <br/>
>switchport access vlan 10<br/>

4. 루트스위치 설정
SW1: VLAN 10 root, vlan 20 secondary root<br/>
>spanning-tree vlan 10 root primary<br/>
>spanning-tree vlan 20 root secondary<br/>
SW2: VLAN 20 root, vlan 10 secondary root<br/>
>spanning-tree vlan 20 root primary<br/>
>spanning-tree vlan 10 root secondary<br/>

5. 확인: 같은 네트워크 pc간에 ping 확인

------------------------------------------

#### 2단계 문제
1. 모든 스위치에서 전환시간 단축과 네트워크 보호를 위한 port fast, bpduguard 설정
- 불필요한 부하 차단하기 위한 bpdufilter 설정
>spanning-tree portfast edge<br/>
>spanning-tree bpdufilter enable<br/>
>spanning-tree bpduguard enable<br/>


2. SW3에서 uplink fast 설정으로 차단상태의 포트를 즉시 전송상태로 전환
>SW3(config)#spanning-tree uplinkfast<br/>
3. 모든 스위치에서 max age (20초) 단축을 위한 backbone fast 설정
>spanning-tree backbonefast<br/>

------------------------------------------

#### 3단계: 라우팅 설정
1. SW1를 각 네트워크의 Gateway로 지정하고 라우팅이 가능하게 SVI 설정
- int vlan 10, int vlan 20 생성
2. 확인: 다른 네트워크의 PC간에 연결 확인
3. 라우터를 추가한 후 nat 설정 등으로 외부 연결까지 성공
- ping 8.8.8.8

Router#sh run | s ospf<br/>
router ospf 1<br/>
 network 10.1.1.16 0.0.0.3 area 0<br/>
 default-information originate<br/>
<br/>
SW1#sh run | s ospf<br/>
router ospf 1<br/>
 network 10.1.1.0 0.0.0.7 area 0<br/>
 network 10.1.1.8 0.0.0.7 area 0<br/>
 network 10.1.1.16 0.0.0.3 area 0<br/>

=============================================

## L2 보안 설정

port security, vlan filter <br/>

vlan filter 의 설정 순서가 아래 PBR과 비슷하다.<br/>

1. PBR(정책 라우팅) Policy Based Routing
- ACL(대상지정) → Route-map(if문) → (적용할 내용을) 인터페이스에 적용
- ACL(대상지정) → access-map → (적용할 내용을) 인터페이스에 적용

### port security

허용할 맥주소를 인터페이스에 적용한다.

Switch(config)#int f0/1<br/>
Switch(config-if)#switchport mode access<br/>
Switch(config-if)#switchport access vlan 1<br/>

Switch(config-if)#switchport port-security ← 동작 선언<br/>
Switch(config-if)#switchport port-security mac-address AAAA.AA00.0001   ← 수동(static)으로 직접 mac 주소 지정<br/>

Switch(config-if)#do sh port


interface FastEthernet0/1<br/>
switchport mode access<br/>
switchport port-security<br/>
switchport port-security maximum 2    ← 동적(dynamic)으로 지정<br/>
switchport port-security mac-address sticky   ← 이 후에 동적(dynamic)으로 학습된 mac주소를 스위치의 재부팅 후에도 secure mac address table에서 유지 시켜주겠다.
<br/>
switchport port-security violation restrict    ← 허용하지 않은 맥주소가 침입할 경우 로그메시지를 남기며 차단<br/>

switchport port-security mac-address sticky AAAA.AA00.0001   ← sticky 설정 이후에 학습된 mac주소<br/>
switchport port-security mac-address sticky AAAA.AA00.0002<br/>






































































































