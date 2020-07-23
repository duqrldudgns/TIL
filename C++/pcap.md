# pcap programming
 * pcap : packet capture
 * 참조 : (http://www.tcpdump.org/pcap.html)

Description | Functions
-|-
pcap 핸들 열기 | pcap_open_live, pcap_open_offline
pcap 핸들 닫기 | pcap_close
packet 수신 | pcap_next_ex
packet 송신 | pcap_sendpacket

## Linux
* pcap을 사용하기 위해 라이브러리 설치 필요
```
$ sudo apt install libpcap-dev
```

## code
* pcap.h 파일을 include하고 링크 과정에서 '-lpcap'옵션 추가 필요
```
// 수신 
#include <pcap.h>
#include <stdio.h>

void usage() {
    printf("syntax: pcap-test <interface>\n");
    printf("sample: pcap-test wlan0\n");
}

int main(int argc, char* argv[]) {
    if (argc != 2) {
        usage();
        return -1;
    }

    char* dev = argv[1];
    char errbuf[PCAP_ERRBUF_SIZE];
    pcap_t* handle = pcap_open_live(dev, BUFSIZ, 1, 1000, errbuf);
    if (handle == nullptr) {
        fprintf(stderr, "pcap_open_live(%s) return nullptr - %s\n", dev, errbuf);
        return -1;
    }

    while (true) {
        struct pcap_pkthdr* header;
        const u_char* packet;
        int res = pcap_next_ex(handle, &header, &packet);
        if (res == 0) continue;
        if (res == -1 || res == -2) {
            printf("pcap_next_ex return %d(%s)\n", res, pcap_geterr(handle));
            break;
        }
        printf("%u bytes captured\n", header->caplen);
    }

    pcap_close(handle);
}
```

## 참조

### <libnet-headers.h>
* 각각의 Header 정보들이 structure로 잘 선언된 헤더파일
> (http://packetfactory.openwall.net/projects/libnet) 
> > Latest Stable Version 다운로드(libnet.tar.gz) 
> > > include/libnet/libnet-headers.h 참조

* 자주 쓰는 구조체
```
struct libnet_ethernet_hdr  
struct libnet_ipv4_hdr  
struct libnet_tcp_hdr   
```

### <netinet/~.h>
* <netinet/ether.h>
* <netinet/ip.h>
* <netinet/tcp.h>
