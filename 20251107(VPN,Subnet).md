## ACL


#### R1 > 기존 ACL 설정 삭제 NAT설정: C,D 영역을 NAT 적용 범위로 지정

IOSvR1(config)#ip access-list standard PERMIT_NAT <br/>
IOSvR1(config-std-nacl)#permit 10.1.1.192 0.0.0.31           <br/>     
IOSvR1(config-std-nacl)#permit 10.1.1.224 0.0.0.15<br/>
IOSvR1(config-std-nacl)#end<br/>



#### R1> telnet, ssh,http server로 동작 시킨 후 D영역에서 들어오는 접근을 차단, 나머지는 허용

ip access-list extended PERMIT
deny   tcp 10.1.1.224 0.0.0.15 host 10.1.1.241 eq www
deny udp host 10.1.1.237 host 8.8.8.8 eq domain
deny   tcp any any eq telnet
deny   tcp any any eq 22
permit ip any any

IOSvR1(config-ext-nacl)#no ip access-list extended PERMIT


## VPN

### IPSec VPN

<img width="405" height="75" alt="image" src="https://github.com/user-attachments/assets/3ed4cc88-f692-4a2c-ae4e-ad7e1d1471f7" />

<img width="619" height="503" alt="image" src="https://github.com/user-attachments/assets/a936a38c-cb70-4185-ab5b-1c39b9fd0b01" />

<img width="945" height="427" alt="image" src="https://github.com/user-attachments/assets/d41045db-52c3-4f02-8c62-e3a6da7423d6" />

<img width="970" height="452" alt="image" src="https://github.com/user-attachments/assets/0eb8b2eb-4b0c-4b03-8cd4-33f01fec06f3" />

<img width="972" height="384" alt="image" src="https://github.com/user-attachments/assets/1fadb01d-39b5-4f46-866d-4c6f811076b5" />



### 서브넷 계산


<img width="658" height="345" alt="image" src="https://github.com/user-attachments/assets/6fd43fa9-9f83-4a61-970e-6045b7a31986" />






















