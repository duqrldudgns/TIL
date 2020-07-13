# Endian
* 컴퓨터의 메모리와 같은 1차원의 공간에 여러 개의 연속된 대상을 배열하는 방법

## Byte order
* NBO(network byte order) : 네트워크 레벨에서 숫자를 표현하는 순서
* HBO(host byte order) : 호스트에 레벨에서 숫자를 표현하는 순서


## Endian
* LE(little-endian) : HBO와 NBO가 다름
* BE(big-endian) : HBO와 NBO가 같음


## Conclusion

* Intel CPU의 경우는 대부분 LE 계열이고, ARM CPU의 경우에는 종류에 따라 BE, LE 계열로 구분되어 지기도 한다. 올바른 네트워크 프로그래밍을 하기 위해서는 LE 및 BE의 차이 및 NBO와 HBO간 변환를 시켜 주는 작동 원리를 이해하고 있어야 한다.

* **#include <netinet/in.h>** 하면 아래와 같은 함수들을 사용할 수 있다. 아래 함수들은 자신의 CPU가 LE이던지 BE이던지 상관없이 작동할 수 있도록 컴파일러가 자신의 byte order를 알고 처리해 준다.


function | size | conversion
--|--|--
uint16_t **ntohs**(uint16_t netshort); | 2 byte | NBO to HBO
uint16_t **htons**(uint16_t hostshort); | 2 byte | HBO to NBO
uint32_t **ntohl**(uint32_t netlong); | 4 byte | NBO to HBO
uint32_t **htonl**(uint32_t hostlong); | 4 byte | NBO to HBO

***
출처 : [gilgil님](https://gitlab.com/gilgil/sns/-/wikis/byte-order/byte-order)
