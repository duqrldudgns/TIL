# Firmware extract

## 펌웨어 얻기 전 과정
 * Flash Memory에 적혀있는데 모델명 확인
 * 라즈베리파이 세팅
 * 그에 맞게 라즈베리파이와 선 연결

## 펌웨어 추출 과정
 ### Flashrom을 이용한 펌웨어 추출
 ```
 $ sudo flashrom -p linux_spi:dev=/dev/spidev0.0 -r firmware.bin
 ```
 모델명 요구함
 ```
 $ sudo flashrom -p linux_spi:dev=/dev/spidev0.0 -r firmware.bin -c "모델명"
 ```
 
 ### Binwalk 설치
 ```
 $ git clone https://github.com/ReFirmLabs/binwalk.git
 $ ./deps.sh
 $ ./setup.py build
 $ ./setup.py install
 ```
 ### file system의 Offset 값 확인
 * Mre: 재귀하게 파일시스템을 추출까지 해줌   
 * E : 암호화 여부 판단   
 * A : 펌웨어의 아키텍쳐 확인 가능   
 ```
 $ binwalk -Mre firmware.bin
 ```
 결과로 offset 이 나열 됨
 
 ### dd 를 이용해 file system 분리
 ```
 $ dd if=./firmware.bin of=./"squashfs" skip="시작주소값" bs=1 count="길이"
 ```
 
 ### unsqaushfs로 압축 풀기(?)
 ```
 $ sudo apt install squashfs-tools
 $ unsqaushfs "squashfs"
 ```
 squashfs-root가 만들어짐
 
 ### gcc-5-mips-linux-gnu로 Cross Compile 하기
 
 내 이름 출력하는 간단한 test.c 파일 만든 후  - **나중에는 이 부분에서 내가 원하는 것을 설정 할 수 있음**
 ```
 $ apt-get install gcc-5-mips-linux-gnu
 $ mips-linux-gnu-gcc-5 -o test test.c -static
 ```
 
 test를 squashfs-root 디렉토리 안에 넣음
 
 ### sqaushfs로 만들기
 ```
 $ mksquashfs squashfs-root "생성할 파일시스템 이름" -comp xz
 ```
 
 ### 수정된 파일시스템을 원본 파일에 patch
 
 (https://github.com/k1rh4/hwHacking/blob/master/firm_re_assembler.py) 를 통해 python 파일 만듦
 ```
 $ python firm_re_assembler.py -s "시작주소" -e "끝주소" -i "원본 펌웨어" -o "만들 펌웨어 이름.bin" -m "수정한 파일시스템"
 ```
 
 ## 끝
 
