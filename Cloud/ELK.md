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

---
> 이어서

## 비밀번호 찾기
```
비밀번호 파일
/etc/kibana/kibana.yml

비밀번호 재설정
/usr/share/elasticsearch/bin/elasticsearch-setup-passwords interactive
```
## 재시작
```
systemctl status elasticsearch
systemctl status kibana
sudo systemctl start elasticsearch
sudo systemctl start kibana

내부에서 curl로 서비스 먼저 확인
curl -XGET -uelastic:'일라스틱 비번' localhost:9200
curl -XGET -uelastic:'일라스틱 비번' localhost:9200/_cat/
curl -XGET -uelastic:'일라스틱 비번' localhost:9200/_cat/health
curl -XGET -uelastic:'일라스틱 비번' localhost:9200/_cat/nodes
curl -XGET -uelastic:'일라스틱 비번' localhost:9200/_cat/indices?v

```

## kibana (dev tools)
```
키바나 창을 띄워서, 콘솔에서 치고 있지만 실제로 명령어가 구동되는 곳은 elasticsearch의 결과가 보여지는 중
지금 하고 있는 것은 커맨드라인에서 curl로 하는 것과 동일
좌측 dev Tools
문장이 포함된 부분에서 실행을 해야함
실행단축키 : ctrl + Enter

PUT은 밀어넣는 것
PUT /customer 실행  -  새로운 index를 만듦

GET은 가져오는 것
GET _cat/indices?v 실행
green은 안전하게 데이터 생성
yellow는 안전하지 않음(노드가 1개뿐이라서, 이 노드가 죽으면 데이터가 유실됨)

생성
PUT /customer/type1/1
{
  "name": "abcd"
}

조회
GET customer/type1/1  

삭제 (후 다시 원상복귀)
DELETE customer

업데이트 (기존 데이터 유지, 새로운 필드 추가)
POST customer/type1/1/_update
{
  "doc": {
    "age": 99
  }
}

업데이트 (기존 데이터(name과 age)삭제 후 처음부터 새롭게 추가)
POST customer/type1/1
{
  "name": "zzzz"
}

```

## 연습문제
```
PUT /customer/type1/1
{
  "name": "John",
  "age": 23,
  "bday": "2020-01-01",
  "phone": "1111"
}
PUT /customer/type1/2
{
  "name": "Sam",
  "age": 25,
  "bday": "1998-02-10",
  "phone": "2222"
}
PUT /customer/type1/3
{
  "name": "Tom",
  "age": 19,
  "bday": "2004-03-20",
  "phone": "3333"
}
PUT /customer/type1/4
{
  "name": "Julie",
  "age": 22,
  "bday": "2001-04-15",
  "phone": "4444"
}
PUT /customer/type1/5
{
  "name": "Jenny",
  "age": 17,
  "bday": "2006-05-30",
  "phone": "5555"
}

GET customer/type1/1
GET customer/type1/2
GET customer/type1/3
GET customer/type1/4
GET customer/type1/5

GET customer/_mapping

GET /customer/_search?q=*

GET /customer/_search?q=*&sort=age:asc - 나이순정렬

# Query DSL
POST /customer/_search
{
  "query": {"match_all": {}}
}

POST /customer/_search                 - 나이순정렬
{
  "query": {"match_all": {}},
  "sort": [
    {"age": "asc"}
  ]
}

POST /customer/_search
{
  "query": {"match_all": {}},
  "from": 2,
  "size": 1
}

# null
POST /customer/_search
{
  "query": {"match": {
    "age": 20
  }}
}

#not null
POST /customer/_search
{
  "query": {"match": {
    "age": 22
  }}
}

#must = and
POST /customer/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": {"name": "Julie"}},
        { "match": {"age": 22}}
      ]
    }
  }
}

```

## RDBMS와의 차이
```
RDBMS
스키마를 필수로 설정해야함
키 이름과 필드 속성(INT, CHAR 등) 사전 정의 필요

ELK
그런거 필요 없음
```

## dev tools
```
벌크로 데이터 밀어넣기
POST /product/type1/_bulk
{"index":{"_id":1}}
{"name:":"John"}
{"index":{"_id":2}}
{"name:":"Sam"}
{"index":{"_id":3}}
{"name:":"Tom"}


GET /product/_search?q=*

POST /product/type1/_bulk
{"index":{"_id":1}}
{"name":"John", "age":20}
{"index":{"_id":2}}
{"name":"Sam", "age":30}
{"index":{"_id":3}}
{"name":"Tom", "age":40}

POST /product/type1/1/_update
{
  "script": {
    "inline": "ctx._source.age++"
  }
}

GET /product/_search?q=*



반복해보기
POST /product/type1/4/_update
{
  "script": {
    "inline": "ctx._source.age++"
  }
}

POST /product/type1/4/_update
{
  "script": {
    "inline": "if(ctx._source.age >= 100) {ctx.op = 'delete' } else {ctx.op = 'none'}"
  }
}

GET /product/type1/4


```

## 두번째 샘플 데이터 추가

```
GET /_cat/indices   #샘플데이터 추가된 것 확인

GET kibana_sample_data_flights/_search?q=*  #기본 사이즈 10개
GET kibana_sample_data_flights/_search?q=*&size=20&from=20 #20개건너뛰고 20개 출력
GET kibana_sample_data_flights/_search?q=*&sort=AvgTicketPrice

POST kibana_sample_data_flights/_search
{
  "query": {"match_all": {}},
  "sort": {"AvgTicketPrice": "desc"}
}

POST kibana_sample_data_flights/_search
{
  "query": {"match":{"DestCountry": "AU"}},
  "sort": {"AvgTicketPrice": "asc"},
  "_source": ["AvgTicketPrice", "FlightNum", "OriginCountry", "DestCountry"]
}
```

## 데이터 임포트

```
curl -O https://download.elastic.co/demos/kibana/gettingstarted/7.x/shakespeare.json  -  샘플데이터 가져오기
wc shakespeare.json  - 데이터 내용 확인하기
head shakespeare.json
vi shakespeare.json
curl -uelastic:<pwd> -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/shakespeare/_bulk?pretty' --data-binary @shakespeare.json


dev tools 
PUT /shakespeare
{
  "mappings": {
    "properties": {
    "speaker": {"type": "keyword"},
    "play_name": {"type": "keyword"},
    "line_id": {"type": "integer"},
    "speech_number": {"type": "integer"}
    }
  }
}
```

## kibana
```
elk 에서 검색 가능한 구조로 만들기
management -> index patterns -> create index
shakespeare -> next -> next

왼쪽 메뉴discover
change -> shakespeare -> search ( text_entry :"ACT I" )
```
