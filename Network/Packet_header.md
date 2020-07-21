# Network packet header 중요한 부분 정리

## Ethernet frame
![Alt text](/Network/img/Ethernet_frame.jpg)

## Ethernet header
 * header length : 14byte
 * dest 가 먼저 나옴 ( IP, TCP 와는 다르게 )
 * Type(2byte) : 이더넷 다음에 어떤 프로토콜이 올지 표시. ex) ipv4 : 0x0800

## IP header
![Alt text](/Network/img/IP_header.png)
 * header length : 20byte
 * version(4bit) : IPv4는 4의 값 ( 0100 )
 * IHL(4bit) : IP 헤더의 크기 / 4 = 5의 값 ( 0101 )
      * version + IHL = 1byte , 0x45(0100/0101)
 * Protocol(1byte) : 다음에 올 프로토콜 표시. ex) tcp : 0x06
 
## TCP header
![Alt text](/Network/img/TCP_header.png)
 * header length : 20byte
 * Source port(2byte) : 보통 1024 이상의 값이 들어감.
 * Destination port(2byte) : 정해진 값.  ex) http : 80
    * port 번호로 인하여 하나의 시스템에서 다양한 서비스를 할 수 있음.
 * offset(4bit) : TCP 헤더의 크기 / 4 = 5의 값 ( 0101 )
