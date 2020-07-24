# 더미 인터페이스를 통한 패킷 스니핑
> 가상의 인터페이스를 생성   
> 와이어샤크로 필요한 패킷만 저장  
> tcpreplay 명령어를 통한 패킷 스니핑

* 실시간 패킷을 스니핑 하는 일은 매번 다른 패킷이 수신되기 때문에 디버깅하기 힘듦.

* 똑같은 패턴의 패킷을 수신하도록 하여 작업의 능률 향상.




## wireshark로 필요한 패킷만 저장하기
* Follow - * stream 기능을 통해 필요한 패킷만 화면에 띄움. (이 작업 안해도 됨)

* File - Export Specified Packets 기능을 통해 필요한 패킷만 pcap file로 저장.




## 가상 인터페이스 생성

* 생성
```
$ sudo ip link add dum0 type dummy
$ sudo ifconfig dum0 up
```

* 삭제
```
$ sudo ip link del dum0
```




## tcpreplay로 패킷 전송
```
$ sudo tcpreplay -i dum0 .pcap
```
