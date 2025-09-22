## GNS3 개인적인 메모

### Pool

<br/>

#### pool 대역 : 10.1.1.101 부터 할당 (10.1.1.0/24)

ip dhcp pool DHCP(이름) <br/>
<br/>
network 10.1.1.0 /24 <br/>
default-router 10.1.1.254 (게이트웨이 지정)<br/>
dns-server 8.8.8.8<br/>
<br/>

#### 만약 예외로 처리하고싶다

conf t 모드로 <br/>
ip dhcp excluded-address 10.1.1.0 10.1.1.100<br/>
<br/>

<img width="922" height="532" alt="Image" src="https://github.com/user-attachments/assets/4e248f0a-2c0d-48cd-8bf3-0d99b33c4945" />

#### Switch

int vlan 1<br/>
no sh<br/>
ip add dhcp<br/>
sh ip int br<br/>
ping 10.1.1.254  ← 라우터와 연결 확인<br/>

### NAT 설정

R1(config)#int g0/0<br/>

#### R1(config-if)#ip add dhcp  ← 인터페이스에 IP 할당

R1(config-if)#no sh<br/>
<br/>
R1(config-if)#int g0/0<br/>

#### R1(config-if)#ip nat outside ← 역할 지정

R1(config-if)#int g0/1<br/>

#### R1(config-if)#ip nat inside  ← 역할 지정

#### 2(config)#ip nat pool NAT 10.1.1.201 10.1.1.220 prefix-length 24 (netmask 255.255.255.0)

풀을 적용한다.<br/>

#### R2(config)#ip nat inside source list 1 pool NAT

INSIDE 범위를 지정한다.<br/>
R2(config)#access-list 1 permit any<br/>


## 스위치에서 ip를 입력하는 방법

### int vlan 1에서 ip 할당 (가상 인터페이스 SVI)

int vlan 1<br/>
ip add 10.1.1.10 255.255.255.0<br/>
or<br/>
ip add dhcp<br/>
<br/>

### switch의 특정 포트를 routed port로 변환 (물리 인터페이스)

#### 포트에 ip 주소를 할당 가능하게 된다.

int g0/1<br/>
no switchport<br/> 
<br/>
ip add 10.1.1.10 255.255.255.0<br/>
or<br/>
ip add dhcp<br/>

#### 다시 스위치 포트를 받고싶다


int g0/1<br/>
switchport<br/>


## 자동으로 DHCP로 IP 할당

<img width="342" height="546" alt="Image" src="https://github.com/user-attachments/assets/4608c74e-c036-4a05-bbe6-86190b459c85" />

<img width="778" height="707" alt="Image" src="https://github.com/user-attachments/assets/f8b3ff64-3809-42e5-a4c7-fca26cac5676" />

아래 3줄 #을 삭제<br/>

<img width="597" height="436" alt="Image" src="https://github.com/user-attachments/assets/80617ed4-c8ba-4cdf-b70f-7456f08e8bf6" />

#### 게이트웨이 확인 route

<img width="664" height="74" alt="Image" src="https://github.com/user-attachments/assets/9f32c08e-7c8b-4cdf-be48-839d6f9f8cc5" />

#### dns 주소 확인 cat /etc/resolv.conf

<img width="424" height="32" alt="Image" src="https://github.com/user-attachments/assets/bdb85976-cc11-44e6-9410-501a7f83d3ab" />


## 수동으로 IP 할당


#### 전원 종료

<img width="287" height="120" alt="Image" src="https://github.com/user-attachments/assets/54fc699a-7cee-4094-98c4-3c098cd2c8e3" />


<img width="337" height="523" alt="Image" src="https://github.com/user-attachments/assets/f58a09cc-0af1-4049-b6ae-a521d2eabdaf" />

#### 원하는 IP와 게이트웨이 수정

*아래 DHCP #은 다시 넣어주기<br/>

<img width="774" height="719" alt="Image" src="https://github.com/user-attachments/assets/627f00d9-322d-41db-967e-3a0d8575d59a" />


## 라우터 혹은 스위치 telnet 접속 구축

conf t 모드<br/>
line vty 0 4<br/>
password cisco (비밀번호 설정)<br/>
login<br/>
privilege level 15 (바로 관리자로 접속)<br/>

## ubuntu 

#### ubuntu 버전 확인

uname -a<br/>

#### python3 버전확인

python3 --version<br/>

#### python3 업데이트

apt install python3-pip -y<br/>

#### python3 실행

python3<br/>





























