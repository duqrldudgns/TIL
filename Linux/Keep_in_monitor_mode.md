# Monitor Mode 유지하기
Monitor Mode를 사용하다 보면 중간에 Monitor Mode는 유지가 되지만 패킷이 잡히지 않을 때가 있다. 그 해결방안으로는 두가지 방법이 있다.

## Network Manager의 처리


### 1.

Linux에서 Network Manager라는 프로세스가 있고 모든 network device를 manage하게 되는데,   

monitor mode로 전환된 interface를 access하여 오동작을 일으켜 패킷을 잡는 도중에 오류가 날 수 있다.     

이를 위해서 monitor mode로 사용되어지는 interface를 unmanage하도록 해야 한다. 
  
 
```
라즈베리파이
sudo apt-get install network-manager
```

```
/etc/NetworkManager/NetworkManager.conf
```  

파일 마지막에 다음과 같은 항목을 추가하여 Network-Manager로 하여금 특정 interface(이름이나 mac으로)에 대해서 unmanage 설정한다. 

```
[keyfile]
unmanaged-devices=interface-name:mon*;interface-name:wlan1;mac:00:11:22:33:44:55
```

설정이 바뀐 이후에는 네트워크 서비스를 재시작을 한다.

```
systemctl restart NetworkManager
```


----------------
### 2.
monitor mode 작동을 방해하는 프로세스가 떠 있을 경우 제대로 된 작동을 하지 못할 수 있다. 

이 경우 다음과 같은 명령어를 통하여 해당 프로세스들을 죽이도록 한다.

```
airmon-ng check kill
```

#### ※ NetworkManager와 같은 프로세스의 중지로 인해 정상적인 인터넷 사용이 되지 않을 수 있음

---------

출처 : [gilgil](https://gitlab.com/gilgil/sns/-/wikis/monitor-mode/monitor-mode)
