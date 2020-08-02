# ARP Spoofing
* LAN에서 ARP를 이용하여 상대방의 데이터 패킷을 중간에서 가로채는 기법
* A와 B가 통신을 할 때 자신의 MAC 주소를 B의 MAC주소라 속이고 B가 받을 패킷을 자신이 먼저 받고 B 에게 그 패킷을 보내주는 방식
* 특별한 이상 증상이 없기 때문에 공격당하는 것을 확인하기가 힘듦

## Spoofing
* 사전적인 의미로 '속이다'

## ARP Protocol
* Address Resolution Protocol - 주소 결정 프로토콜
* TCP/IP 3계층(네트워크 계층)의 IP Address를 2계층(데이터 링크 계층)의 MAC address로 대응 시킬때 사용하는 프로토콜

## 과정
- 공격자가 A에게 자신의 MAC주소를 B의 MAC 주소라고 속인다.
- 공격자가 B에게 자신의 MAC 주소를 A의 MAC 주소라고 속인다.
- A는 B의 MAC 주소가 공격자라 알고 있고, B도 A의 MAC 주소가 CC(공격자)라 알고 있다.
- 공격자는 A에게 받은 메시지를 B에게 보내고, B에게 받은 메시지를 A에게 정상적으로 보내준다.

## sender 감염 시키기 - 기초
> sender : 공격 당하는 대상    
> target : sender와 데이터를 주고받는 대상 (주로 gateway)    
> attacker : 공격자

* 우선, 공격하고 싶은 sender의 IP주소를 알고 있어야 함
* sender의 mac address를 모르기 때문에 ARP Request를 통해 sender의 IP를 입력하여 송신
  * eth_dmac = broadcast(ff:----), arp_tmac = empty(00:----)
* ARP Reply로 sender로부터 mac address를 수신 받음
* ARP Reply로 sender 에게 target의 ip는 제대로 알려주고 mac address는 attacker의 것으로 설정하여 송신
  * arp_smac = attacker's mac addr , arp_sip = target's ip
* sender에서 target의 mac address가 attacker의 것으로 변경 됨(arp -a)

[ARP 실습](https://github.com/duqrldudgns/send_arp/tree/master/new)

---

## RARP
* Reverse Address Resolution Protocol
* ARP의 반대 역할
  * MAC 주소 값으로 IP 주소 값을 알아내는 프로토콜
  
