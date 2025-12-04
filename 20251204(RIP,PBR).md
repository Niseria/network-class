
## RIP


<img width="1158" height="571" alt="image" src="https://github.com/user-attachments/assets/64c981c4-5491-4afc-ac92-d2bd87334cd8" />


distribute-list : 내가 상대방(rip 이웃)에게 전달할 네트워크정보를 선택적으로 관리하겠다.

ACL, prefix 차이점 : 네트워크 범위를 지정할때 보다 편하고 정밀하게 제어가 가능하다.


ip prefix-list PREFIX description RIP_PREFIX

R1(config-router)#do sh run | s rip<br/>
router rip<br/>
 version 2<br/>
 network 1.0.0.0<br/>
 network 10.0.0.0<br/>
 network 172.16.0.0<br/>
 network 192.168.10.0<br/>
 distribute-list prefix PREFIX out GigabitEthernet0/0<br/>
 distribute-list prefix PREFIX in GigabitEthernet0/0<br/>
 no auto-summary<br/>
<br/>
상대방(RIP 이웃)에게 보내고, 받는 모든 정보가 보이지 않게 된다.<br/>
<br/>
R1(config-router)#no distribute-list prefix PREFIX out GigabitEthernet0/0<br/>
R1(config-router)#no distribute-list prefix PREFIX in GigabitEthernet0/0<br/>
R1(config-router)#exit<br/>
R1(config)#no ip prefix-list PREFIX<br/>










###  R6에서 distribute-list, prefix-list(ACL)를 활용해서 나가는 모든 RIP 네트워크 정보를 차단



-------------------
R5에서 조회,확인


R6(config)#ip access-list standard ACL<br/>
R6(config-std-nacl)#deny any            <br/>
R6(config-std-nacl)#router rip<br/>
R6(config-router)#distribute-list ACL out g0/0<br/>
R6(config-router)#distribute-list ACL out g1/0<br/>


R6(config)#ip prefix-list PREFIX deny 0.0.0.0/0 le 32<br/>
R6(config)#router rip<br/>
R6(config-router)#distribute-list prefix PREFIX out g1/0<br/>
R6(config-router)#distribute-list prefix PREFIX out g0/0<br/>

### R6에서 distribute-list, prefix-list(ACL)를 활용해서 나가는 RIP 네트워크 정보에서  L0(7.7.7.0/24)만 차단, L1은 허용(혹은 나머지 모두 허용)

R6(config)#ip prefix-list PREFIX deny 7.7.7.0/24   <br/>
R6(config)#ip prefix-list PREFIX permit 0.0.0.0/0 le 32<br/>

R6(config)#router rip<br/>
R6(config-router)#distribute-list prefix PREFIX out g1/0<br/>
R6(config-router)#distribute-list prefix PREFIX out g0/0<br/>

prefix-list 에서 부분 삭제, 추가 등은 다음과 같이<br/>
R6(config)#no ip prefix-list PREFIX seq 5 deny 7.7.7.0/24<br/>
R6(config)#do sh ip prefix
ip prefix-list PREFIX: 1 entries
   seq 10 permit 0.0.0.0/0 le 32
R6(config)#ip prefix-list PREFIX seq 4 deny 7.7.7.0/24
R6(config)#do sh ip prefix                            
ip prefix-list PREFIX: 2 entries
   seq 4 deny 7.7.7.0/24
   seq 10 permit 0.0.0.0/0 le 32


### R5에서 distribute-list, prefix-list(ACL)를 활용해서 나가는 RIP 네트워크 정보에서 L1(6.6.5.0/24)만 허용, L1은 차단(혹은 나머지 모두 차단)



R5(config)#ip prefix-list PREFIX permit 6.6.5.0/24 <br/>
R5(config)#router rip<br/>
R5(config-router)#destribute-list prefix PREFIX out g0/0<br/>
R5(config-router)#destribute-list prefix PREFIX out g1/0<br/>

## offset-list

특정경로의 메트릭을 증가시 경로의 우선순위 조정

ip access-list standard ACL<br/>
permit 7.7.7.0 0.0.0.255<br/>
permit 7.7.6.0 0.0.0.255<br/>

router rip<br/>
offset-list ACL in 10 g1/0<br/>

offset-list 0 in g1/0 <- 한번에 모든 offset-list를 설정할수 있다<br/>

R6(config-router)#do sh ip route rip<br/>
     6.0.0.0/24 is subnetted, 2 subnets<br/>
R       6.6.5.0 [120/1] via 1.1.67.6, 00:00:11, GigabitEthernet0/0<br/>
R       6.6.6.0 [120/1] via 1.1.67.6, 00:00:11, GigabitEthernet0/0<br/>

#### 삭제

no offset-list ACL in 10 GigabitEthernet1/0

### PBR

1번째 - 정책라우팅 PBR<br/>
2번째 - 정적라우팅 route<br/>
3번째 - 동적라우팅 RIP - 경로조정<br/>
<br/>



<img width="1242" height="432" alt="image" src="https://github.com/user-attachments/assets/8b1b485f-3ffc-4ebd-9c06-4a5d6d9dc389" />



#### PBR <- ACL - Routemap - 인터페이스 적용<br/>


*접속 타임아웃 설정
exec-timeout 1 0 <- 자리 비움으로 인한 접근을 방지하기 위함<br/>
Timestamp 로그 설정 <- 네트워크 장비에서 로그가 발생했을 때 타임 정보가 없다면 발생 시기 등과 같은 정보를 자세히 알 수 없으니 로그 설정
<br/>


#### R5

conf t<br/>
access-list 101 permit ip host 192.168.1.1 host 192.168.2.1<br/>
access-list 102 permit ip host 192.168.1.2 host 192.168.2.2<br/>

route-map PBR permit 10(permit 10 안적어도 10으로 적용됨)<br/>
match ip add 101<br/>
set ip next-hop 1.1.76.7<br/>

route-map PBR permit 20<br/>
match ip address 102<br/>
set ip next-hop 1.1.76.7<br/>

int f0/1<br/>
ip policy route-map PBR<br/>
sh run | s access<br/>

#### R6

R6(config)#access-list 101 permit ip host 192.168.2.1 host 192.168.1.1<br/>
R6(config)#access-list 102 permit ip host 192.168.2.2 host 192.168.1.2<br/>

R6(config)#route-map PBR permit 10<br/>
R6(config-route-map)#match ip add 101<br/>
R6(config-route-map)#set ip next-hop 1.1.67.6<br/>
R6(config-route-map)#route-map PBR permit 20<br/>
R6(config-route-map)#match ip add 102<br/>
R6(config-route-map)#set ip next-hop 1.1.76.6<br/>

R6(config-route-map)#int f0/1<br/>
R6(config-if)#ip policy route-map PBR<br/>


































