

## NAPALM

sudo apt update<br/>
sudo apt install python3-venv -y<br/>
buntu@ubuntu-server:~/PythonHome$ mkdir NetAuto<br/>
ubuntu@ubuntu-server:~/PythonHome$ cd NetAuto/<br/>
ubuntu@ubuntu-server:~/PythonHome/NetAuto$ python3 -m venv napalm_venv<br/>
ubuntu@ubuntu-server:~/PythonHome/NetAuto$ ls<br/>
napalm_venv<br/>
ubuntu@ubuntu-server:~/PythonHome/NetAuto$ ll<br/>
합계 12<br/>
drwxrwxr-x 3 ubuntu ubuntu 4096  1월  6 12:15 ./<br/>
drwxrwxr-x 3 ubuntu ubuntu 4096  1월  6 12:14 ../<br/>
drwxrwxr-x 5 ubuntu ubuntu 4096  1월  6 12:15 napalm_venv/<br/>
ubuntu@ubuntu-server:~/PythonHome/NetAuto$ ls napalm_venv/<br/>
bin  include  lib  lib64  pyvenv.cfg<br/>
ubuntu@ubuntu-server:~/PythonHome/NetAuto$ source napalm_venv/bin/activate<br/>
(napalm_venv) ubuntu@ubuntu-server:~/PythonHome/NetAuto$ deactivate<br/>
ubuntu@ubuntu-server:~/PythonHome/NetAuto$ source napalm_venv/bin/activate<br/>
(napalm_venv) ubuntu@ubuntu-server:~/PythonHome/NetAuto$ pip install -U napalm<br/>
(napalm_venv) ubuntu@ubuntu-server:~/PythonHome/NetAuto$ pip show napalm<br/>
Name: napalm<br/>
Version: 5.1.0<br/>
Summary: Network Automation and Programmability Abstraction Layer with Multivendor support<br/>

로그 메시지 없이 빠른 접속환경에서의 실습을 위해 각 장비에서 배너 관련 설정 취소<br/>
conf t<br/>
no banner login <br/>
no banner exec  <br/>
no banner motd <br/>
end<br/>
wr<br/>

na1.py<br/>
import napalm<br/>
driver = napalm.get_network_driver('ios')  <br/>
device = driver(hostname='10.1.1.11', username='ccnp', password='cisco') <br/>
device.open()  <br/>

output = device.get_facts()  <br/>
print(output)  <br/>

device.close() <br/>
---<br/>

from napalm import get_network_driver<br/>
driver = get_network_driver('ios')  <br/>
device = driver(hostname='10.1.1.12', username='ccnp', password='cisco') <br/>
device.open()  <br/>

output = device.get_facts()  <br/>
print(output)  <br/>

device.close() <br/>



## EIGRP

#### EIGRP는 시스코(Cisco)에서 개발한 거리 벡터(Distance Vector) 기반의 라우팅 프로토콜이지만, 링크 상태(Link State) 프로토콜의 장점까지 흡수한 '하이브리드' 성격의 강력한 프로토콜입니다.

- 빠른 수렴(Fast Convergence): DUAL(Diffusing Update Algorithm) 알고리즘을 사용하여 네트워크 변화를 즉시 감지하고 대체 경로로 바로 전환합니다.
- 부하 분산(Load Balancing): 기본적으로 경로 비용이 같은 경우(Equal Cost)는 물론, 설정에 따라 비용이 다른 경로(Unequal Cost) 간에도 트래픽을 분산할 수 있는 유일한 프로토콜입니다.
- 효율적인 업데이트: 전체 라우팅 테이블을 주기적으로 보내지 않고, 변화가 생겼을 때만(Incremental) 관련 라우터들에게 업데이트를 보냅니다.
- AS(Autonomous System) 번호 사용: 동일한 AS 번호를 가진 라우터들끼리만 정보를 공유합니다.




























































































































































































































































