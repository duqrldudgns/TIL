# ELK 실습

## vm 인스턴스 생성
```
이름 elk1

n1-standard-1

우분투 16.04 LTS

http 트래픽허용
```

## ssh연결

### 날짜 맞추기
```
sudo rm -rf /etc/localtime  
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime    
date
```
### 자바 설치
```
sudo apt install default-jdk
```
### ELK Stack 설치
```
mkdir downloads   
cd downloads/   
sudo apt update   
sudo apt upgrade    
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.5.2.deb   
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.1-amd64.deb   
wget https://artifacts.elastic.co/downloads/kibana/kibana-7.5.2-amd64.deb   
sudo dpkg -i elasticsearch-7.5.1-amd64.deb    
sudo dpkg -i kibana/kibana-7.5.2-amd64.deb    
sudo dpkg -i logstash-7.5.2.deb   
```
### 설치는 됐는데 돌아가지 않고있다는걸 확인
```
systemctl status elasticsearch    
systemctl status kibana
```
### 시작
```
sudo systemctl start elasticsearch
sudo netstat -antp | grep 9200
curl localhost:9200

sudo systemctl start kibana
sudo netstat -antp | grep 5601
curl -L http://localhost:5601
```

## vm 인스턴스
```
네트워크 태그 elastic-server 추가
```

## vpc 네트워크 -방화벽
```
이름 elastic-server

지정된 대상 태그

범위 0.0.0.0/0 (모두 허용)

tcp 9200, 5601

접속시도
공인ip:9200
공인아이피로 접속해도안되는게 일단 정상. 보안설정을 풀어야함
```

## ssh 
 
### 패킷 오고가는지 확인
```
sudo tcpdump -i ens4 -nne port 9200
```
### elasticsearch보안설정 풀기
```
sudo vi /etc/elasticsearch/elasticsearch.yml
node.name: node-1
network.host: 0.0.0.0 추가
discovery.seed_hosts: ["node-1"]
cluster.initial_master_nodes: ["node-1"]

sudo systemctl restart elasticsearch
공인ip:9200 접속하면 잘됨
```

## vpc 네트워크 -방화벽
```
범위 14.42.3.83(지금 내 로컬 ?공인아이피)/32로 바꿈 
```
## ssh
### 키바나 보안설정
```
 sudo vi /etc/kibana/kibana.yml 
 server.host: "0.0.0.0"
 sudo systemctl restart kibana
 공인ip:5601 접속하면 잘됨
```

### 인가된 사용자 인증 설정

일레스틱과 키바나의 통신을 어떻게 할것인가
```
sudo vi /etc/elasticsearch/elasticsearch.yml
맨아래 
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true

sudo systemctl restart elasticsearch

sudo /usr/share/elasticsearch/bin/elasticsearch-setup-passwords interactive
비밀번호 입력한거 기억하기

공인ip:5601 접속하면 안됨
 ```
 
### 주석풀기
```
sudo vi /etc/kibana/kibana.yml 
elasticsearch.username: "kibana"
elasticsearch.password: "키바나 비번"
sudo systemctl restart kibana
공인ip:5601 하면 비번요구
id : elastic
비번 : 일라스틱 비번


curl -XGET -uelastic:'일라스틱 비번' localhost:9200
```
## 공인ip:5601 접속
```
웹로그샘플데이터 추가
visualize에서 만든데이터를
dashboard에서 add 해서 볼 수 있음
```
