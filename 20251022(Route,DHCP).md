## 라우터 경로지정

<img width="1417" height="539" alt="Image" src="https://github.com/user-attachments/assets/e9363569-dd7d-42bd-a8db-5c84938b7284" />


### PC1 -> R1 -> R2 -> R4 -> PC2


#### R1
ip route 192.168.24.0 255.255.255.0 192.168.12.2
ip route 192.168.4.0 255.255.255.0 192.168.12.2

#### R2
ip route 192.168.1.0 255.255.255.0 192.168.12.1
ip route 192.168.4.0 255.255.255.0 192.168.24.4

#### R4
ip route 192.168.1.0 255.255.255.0 192.168.24.2
ip route 192.168.12.0 255.255.255.0 192.168.24.2

pc1 - trace 192.168.4.10


### PC2 -> R4 -> R3 -> R1 -> PC1

#### R1
ip route 192.168.4.0 255.255.255.0 192.168.13.3 10 <- 주 경로
ip route 192.168.4.0 255.255.255.0 192.168.12.2 20 <- 백업경로

#### R3
ip route 192.168.4.0 255.255.255.0 192.168.34.4 
ip route 192.168.1.0 255.255.255.0 192.168.13.1 

#### R4
ip route 192.168.1.0 255.255.255.0 192.168.34.3 10 <- 주경로
ip route 192.168.1.0 255.255.255.0 192.168.24.2 20 <- 백업경로

## 복습

### DHCP


<img width="1847" height="637" alt="Image" src="https://github.com/user-attachments/assets/1fe2d24e-80c0-4486-9a27-9537dc76f3dc" />

<img width="933" height="730" alt="Image" src="https://github.com/user-attachments/assets/9334269f-d928-435e-8eb7-7a42c4d94717" />

<img width="848" height="508" alt="Image" src="https://github.com/user-attachments/assets/7bd93ea1-58fe-4304-ac79-3e5b53fcef53" />

<img width="897" height="617" alt="Image" src="https://github.com/user-attachments/assets/2f74c286-16da-4395-b9ca-c4547acf6239" />



### DNS

<img width="919" height="741" alt="Image" src="https://github.com/user-attachments/assets/68143aed-b7db-4276-a64b-19882b94210d" />

당연히 8.8.8.9~10과의 ping 연결이 가능해야함



















