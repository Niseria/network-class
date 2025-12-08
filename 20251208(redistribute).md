
## redistribute


<img width="1132" height="528" alt="image" src="https://github.com/user-attachments/assets/2ca78c97-cb04-471a-899b-e5bba2407a5a" />



#### R7

ip route 200.1.0.0 255.255.255.0 null 0<br/>
ip route 200.1.1.0 255.255.255.0 null 0<br/>
ip route 200.1.2.0 255.255.255.0 null 0<br/>
ip route 200.1.3.0 255.255.255.0 null 0<br/>

R7(config)#do sh ip route static<br/>
S    200.1.0.0/24 is directly connected, Null0<br/>
S    200.1.1.0/24 is directly connected, Null0<br/>
S    200.1.2.0/24 is directly connected, Null0<br/>
S    200.1.3.0/24 is directly connected, Null0<br/>

R7#sh run | s rip<br/>
router rip<br/>
 version 2<br/>
 redistribute connected metric 5(경로로 받았을때 5)<br/>
 redistribute static metric 10(경로로 받았을때 10)<br/>
 network 1.0.0.0<br/>
 no auto-summary<br/>

#### metric 제거할때

redistribute connected<br/>
redistribute static<br/>


#### 매트릭을 삭제하고 route-map을 이용해서 다양한 조건을 적용시켜본다.

#### ACL(대상지정) → Route-map(if문) → (적용할 내용을) rip설정에 적용

200.1.0.0/24의 메트릭만 10으로, <br/>
200.1.3.0/24만 재분배에서 제외를 시킨다.<br/>
기타 기본 재분배 메트릭은 3으로 하겠다.<br/>
<br/>

### ACL(대상지정) 
ip access-list standard METRIC<br/>
permit 200.1.0.0 0.0.0.255<br/>
<br/>
ip access-list standard FILTER<br/>
deny 200.1.3.0 0.0.0.255<br/>
permit any<br/>

### Route-map(if문)

#### 200.1.0.0/24의 메트릭만 10으로,
R7(config)# route-map POLICY permit 10<br/>
R7(config-route-map)#match ip address METRIC<br/>
R7(config-route-map)#set metric 10   ← 1번 적용<br/>
<br/>

#### 200.1.3.0/24만 재분배에서 제외를 시킨다.
R7(config-route-map)#route-map POLICY permit 20<br/>
R7(config-route-map)#match ip address FILTER  ← 2번 적용<br/>

#### 기타 기본 재분배 메트릭은 3으로 하겠다.

R7(config)#router rip<br/>
R7(config-router)#default-metric 3   ← 3번 적용<br/>

### (적용할 내용을) rip설정에 적용

redistribute static route-map POLICY   ← 1,2번을 rip에 적용시킨다.


#### PBR을 활용해서, R3의 내부망(1.1.30.0/24)와 R4의 내부망(172.16.10.0/24)간의 통신은 R3,R4사이에 연결된 FastEthernet 구간을 사용하자.
#### (현재 R3에 설정된 offset-list 설정보다 우선순위가 앞선 PBR을 적용시킨다.)

#### R4
ip access-list extanded R3TOR4<br/>
permit ip 1.1.30.0 0.0.0.255 172.16.10.0 0.0.0.255<br/>
route-map PBR<br/>
match ip add R3TOR4<br/>
set ip next-hop 1.1.34.4<br/>
int f0/1<br/>
ip policy route-map PBR<br/>

#### R7
ip access-list extended R4TOR3<br/>
permit ip 172.16.10.0 0.0.0.255 1.1.30.0 0.0.0.255<br/>
route-map PBR<br/>
match ip add R4TOR3<br/>
set ip next-hop 1.1.34.3<br/>
int f0/0<br/>
ip policy route-map PBR<br/>


## 최종 정리 문제: RIP 기반 네트우크 설계, 구현 및 인증, 재분배, PBR 정책 적용

<img width="906" height="385" alt="image" src="https://github.com/user-attachments/assets/155e665c-3d5f-44aa-91df-5841ac846949" />



RIP 기반 네트워크 설계 및 구현 요구사항 정리 <br/>
1. RIP을 사용해 네트워크 설계 및 구현<br/>
<br/>
새롭게 추가된 R5는 Serial 인터페이스를 사용한다.<br/>
<br/>
2. ISP 라우터와 R5 사이의 통신 보안을 위해 RIP 인증 설정 적용<br/>
3. R3의 Serial interface 3/1 방향에 L0 ~ L3 네트워크를 RIP 추가 주소(summary) 로 전달<br/>
<br/>
summary-address 사용<br/>
<br/>
4. R3의 Serial interface 3/1 방향에 L4 ~ L7 네트워크는 RIP 광고 전달에서 제외<br/>
distribute-list, prefix-list 사용<br/>
<br/>
5. R5 추가로 인해 R3의 내부 사용자 트래픽이 느린 R5 방향으로 가지 않도록 조정<br/>
RIP의 특성상 잘못된 경로로 갈 수 있으므로,<br/>
빠른 경로인 R2 방향을 메인(Primary) 으로 사용하게 설정<br/>
<br/>
offset-list 사용<br/>
<br/>
6. R5의 내부 네트워크는 아래 조건으로 재배분(redistribute) 설정<br/>
static 100.1.0.0/24 → metric 10 적용<br/>
static 100.1.3.0/24 → 재배분에서 제외<br/>
<br/>
위 두 개를 제외한 나머지는 metric 3 적용<br/>
connected 네트워크는 metric 5 적용<br/>
<br/>
7. PBR Policy-Based Routing 설정<br/>
R5 내부망과 R3 내부망 간의 통신은 반드시 R5–R3 Serial 구간을 통하도록 정책 구성<br/>
access-list + route-map + interface 적용<br/>

#### 2. ISP 라우터와 R5 사이의 통신 보안을 위해 RIP 인증 설정 적용

ISP 

conf t<br/>
key chain RIPKEY<br/>
key 1<br/>
key-string cisco<br/>
int s3/0<br/>
ip rip authentication key-chain RIPKEY<br/>
ip rip authentication mode MD5<br/>

R5 

conf t<br/>
int f0/1<br/>
key chain RIPKEY<br/>
key 1<br/>
key-string cisco<br/>
int s3/0<br/>
ip rip authentication key-chain RIPKEY<br/>
ip rip authentication mode md5<br/>

#### 3. R3의 Serial interface 3/1 방향에 L0 ~ L3 네트워크를 RIP 추가 주소(summary) 로 전달

conf t<br/>
int s3/1<br/>
ip summary-address rip 10.0.0.0 255.255.252.0<br/>


#### 4. R3의 Serial interface 3/1 방향에 L4 ~ L7 네트워크는 RIP 광고 전달에서 제외

conf t<br/>
router rip<br/>
distribute-list prefix PREFIX out s3/1<br/>

#### 5. R5 추가로 인해 R3의 내부 사용자 트래픽이 느린 R5 방향으로 가지 않도록 조정

router rip<br/>
 offset-list 0 in 10 s3/1<br/>
 offset-list 0 out 10 s3/1<br/>

#### 6. R5의 내부 네트워크는 아래 조건으로 재배분(redistribute) 

ip access-list standard METRIC<br/>
 permit 100.1.0.0 0.0.0.255<br/>

ip access-list standard FILTER<br/>
 deny   100.1.3.0 0.0.0.255<br/>
 permit any<br/>

route-map POLICY permit 10<br/>
 match ip address METRIC<br/>
 set metric 10<br/>

route-map POLICY permit 20<br/>
 match ip address FILTER<br/>

router rip<br/>
 redistribute connected metric 5<br/>
 redistribute static route-map POLICY<br/>
 default-metric 3<br/>

#### 7. PBR Policy-Based Routing 설정


R5><br/>
ip access-list extended R5TOR3<br/>
 permit ip 172.16.20.0 0.0.0.255 1.1.30.0 0.0.0.255<br/>

route-map PBR<br/>
match ip address R5TOR3<br/>
set ip next-hop 1.1.35.1<br/>

interface GigabitEthernet0/0<br/>
 ip policy route-map PBR<br/>

R3><br/>
ip access-list extended R3TOR5<br/>
 permit ip 1.1.30.0 0.0.0.255 172.16.20.0 0.0.0.255 <br/>

no route-map PBR<br/>

route-map PBR permit 10   ← 기존 R4방향 설정에 해당<br/>
match ip address R3TOR4<br/>
set ip next-hop 1.1.34.4<br/>
<br/>
route-map PBR permit 20<br/>
match ip address R3TOR5<br/>
set ip next-hop 1.1.35.5<br/>
<br/>
interface GigabitEthernet0/0<br/>
ip policy route-map PBR<br/>






































































































































