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

## kibana (management)
```
elk 에서 검색 가능한 구조로 만들기
 -> index patterns -> create index
shakespeare -> next -> next

왼쪽 메뉴discover
change -> shakespeare -> search ( text_entry :"ACT I" )
```

## kibana (visualizations)
```
Vertical Bar 생성
Buckets add
X-axis
Terms
playname
size10
play
save(BoB-shake-bar-playname)

tag cloud
Buckets add
Aggregation
Terms
text_entry.keyword
size 50
save  BoB-shakes-tagcloud-textentry

대시보드에서 위 항목 add
BoB-shakes-tagcloud-textentry
BoB-shake-bar-playname
```

## beats 설치
```
sudo apt install nginx -y
/var/log/nginx/access.log 로그 쌓이는 곳, 이걸 elk 에 밀어 넣을 예정

wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.5.2-amd64.deb
sudo dpkg -i filebeat-7.5.2-amd64.deb
sudo vi /etc/filebeat/filebeat.yml 설정파일 위치 

원래 일반적인 구조
beats -> logstash -> elasticsearch -> kibana
지금 하는 설정
beats -> elasticsearch -> kibana

수정
<<INPUT>>
-type : log
 enabled : true
 paths : /var/log/nginx/*.log
<<OUPUT>>
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["localhost:9200"]
  # Optional protocol and basic auth credentials.
  protocol: "http"
  username: "elastic"
  password: "비번"
  
( sudo filebeat setup ) - 이걸 통해서 각종 설정들을 알아서 kibana에다가 만들어줌 근데 지금은 안함 -> Management -> Index patterns 자동 생성
sudo systemctl start filebeat 파일비트 서비스 구동
sudo filebeat modules list 파일비트가 지원하는 다양한 로그 규격들 목록 확인
( sudo filebeat modules enable nginx )   nginx활성화하는건데 해도 되고 안해도됨 . 위에서 수동으로 INPUT부분에서 nginx로그를 넣었음. 이거는 자동화해주는 느낌

kibana dev tools 에서 GET _cat/indices?v 로 있나 확인
tail -f /var/log/nginx/access.log
```

## kibana Management -> Index patterns
```
filebeat-* -> @timestamp 설정
```
## kibana discover
```
filebeat change

파일비트 : 로그파일보내주는걸 잘하는 애

로그스태시 : 전처리가 가능한 것

일레스틱서치 : 들어간 데이터를 잘, 빠르게 검색해줌

현재 로그 포맷(키/밸류 확인)
@timestamp:Aug 9, 2020 @ 18:48:19.892 message:14.42.3.83 - - [09/Aug/2020:18:32:59 +0900] "GET / HTTP/1.1" 200 396 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.105 Safari/537.36" input.type:log host.os.version:16.04.6 LTS (Xenial Xerus) host.os.family:debian host.os.name:Ubuntu host.os.kernel:4.15.0-1080-gcp host.os.codename:xenial host.os.platform:ubuntu host.id:8d96388dc6509d0d14131a56805959b8 host.name:elk1 host.containerized:false host.hostname:elk1

메세지필드가 가장 중요한 nginx이 로그인데, 파싱을 전혀 못했음
그래서 결론적으로 로그파싱을 못하니까 logstash를 거쳐야함

elastic으로 바로 넣어줬는데 파싱이 제대로 안짤려넣어서 logstash통해서 짤라서 파싱해주는것
```

## logstash 실행
```
sudo systemctl start logstash
설정파일
sudo vi /etc/logstash/logstash.yml

grok : 정규표현식같은것
dev tools grok debugger로
샘플데이터에
218.237.230.97 - - [09/Aug/2020:18:35:00 +0900] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.105 Safari/537.36"
그록패턴에
%{IPORHOST:[nginx][access][remote_ip]} - %{DATA:[nginx][access][user_name]} \[%{HTTPDATE:[nginx][access][time]}\] \"%{WORD:[nginx][access][method]} %{DATA:[nginx][access][url]} HTTP/%{NUMBER:[nginx][access][http_version]}\" %{NUMBER:[nginx][access][response_code]} %{NUMBER:[nginx][access][body_sent][bytes]} \"%{DATA:[nginx][access][referrer]}\" \"%{DATA:[nginx][access][agent]}\"


sudo vi /etc/logstash/conf.d/nginx.conf

input {
  beats {
    port => 5044
  }
}
filter {
    grok {
      match => { "message" => ["%{IPORHOST:[nginx][access][remote_ip]} - %{DATA:[nginx][access][user_name]} \[%{HTT
PDATE:[nginx][access][time]}\] \"%{WORD:[nginx][access][method]} %{DATA:[nginx][access][url]} HTTP/%{NUMBER:[nginx]
[access][http_version]}\" %{NUMBER:[nginx][access][response_code]} %{NUMBER:[nginx][access][body_sent][bytes]} \"%{
DATA:[nginx][access][referrer]}\" \"%{DATA:[nginx][access][agent]}\"] }
       # remove_field => "message"
    }
}
output {
  elasticsearch {
    hosts => "localhost:9200"
    index => "logstash-20200809"
    user => "elastic"
    password => "비번"
  }
} 

sudo systemctl restart logstash.service


sudo vi /etc/filebeat/filebeat.yml      아웃풋을 일레스틱서치에서 로그스태쉬로 변경

#output.elasticsearch:
  # Array of hosts to connect to.
  #hosts: ["localhost:9200"]
  # Optional protocol and basic auth credentials.
  #protocol: "http"
  #username: "elastic"
  #password: "비번"
#----------------------------- Logstash output --------------------------------
output.logstash:
  # The Logstash hosts
  hosts: ["localhost:5044"]

sudo systemctl restart filebeat.service
  
 홈페이지 왔다갔다 하기
  GET _cat/indices?v 로 확인 logstash있는지

  키바나 인덱스패턴에 logstash- 추가
```

## 동작순서
1. 웹페이지에 접속한다
2. nginx가 access.log를 만든다
3. filebeat가 access.log 파일에 변화를 감지한다
4. filebeat가 access.log 파일을 읽어서 그 변화된 로그 양을 logstash로 보낸다
5. logstash가 그 내용을 (socket 5044)읽어서, 파싱을한다.
6. logstash가 그 내용을 다시 output으로 보낸다 (근데 그 output이 elasticsearch임)
7. elasticsearch가 그 내용을 받아서, 인덱싱 한다.
8. 그 내용을 elasticsearch를 통해서 조회를 한다.
