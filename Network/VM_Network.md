# VM Network 종류

## NAT(Network Address Translation)
- 가상머신 내부 네트워크 -> Host PC 외부 네트워크 단방향 통신 가능
- Host PC 내의 가상 머신 간의 통신 **불가능**
- 포트포워딩을 통해 외부 네트워크와 양방향 통신 가능

<img src="/Network/VM_Network_png/NAT.png" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="NAT"></img><br/>

## NAT 네트워크(NAT Network)
- 가상머신 내부 네트워크 -> Host PC 외부 네트워크 단방향 통신 가능
- Host PC 내의 가상 머신 간의 통신 **가능**

<img src="/Network/VM_Network_png/NAT_Network.png" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="NAT"></img><br/>

## 브리지 어댑터(Bridged Adapter)
- Host PC와 동등한 수준의 네트워크 구성
- DHCP에서의 추가의 IP 할당이 필요함

<img src="/Network/VM_Network_png/Bridged_Adapter.png" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="NAT"></img><br/>

## 내부 네트워크(Internal Network)
- Host PC와 **독립적인** 내부 네트워크
- 같은 Host PC 내의 가상 머신 간 연결이 가능

<img src="/Network/VM_Network_png/Internal%20Network.png" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="NAT"></img><br/>

## 호스트 전용 어댑터(Host-only Adapter)
- Host PC를 **포함한** 내부 네트워크 (외부 네트워크와는 단절)
- 같은 Host PC 내의 가상 머신 간 연결이 가능

<img src="/Network/VM_Network_png/Host-only_Adapter.png" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="NAT"></img><br/>

***
출처: [TechNote.kr](https://technote.kr/213) 
