# tcpdump 명령어

### 무선랜카드 이용하여 패킷 수집할 때 사용

```
sudo tcpdump -i wlan1 "link[0]==0x40" -G 3600 -w /home/pi/bob-pr/pcap/pr-%Y%m%d-%H%M.pcap
```

- -i wlan1: 인터페이스명
- "link[0]==0x40" : Type이 Probe Request(0x0004의 네트워크 바이트 오더)
- -G 3600: 패킷 정보 저장 파일을 3600초(1시간)마다 갱신한다(한시간마다 년월일시분.pcap 파일 새로 생성)
- -w : 패킷정보 저장할 파일명
- 주의사항: tcpdump: Couldn't change ownership of savefile 오류가 나는 경우 파일 경로를 루트 디렉토리로
- 파일 하나 저장 잘 되고 두번째 파일 생성시 오류: 명령어 끝에 -Z root 옵션
