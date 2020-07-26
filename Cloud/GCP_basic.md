# GCP 실습
> CE, VM인스턴스, disk  
> Storage, Bucket   
> SQL

# Concept

- GCP 배워보기 + CloudShell을 통해 개발자스럽게 개발하기
- 코딩: 재사용성

### Compute Engine

나만의 서버 만들기

### Storage(Bucket)

나만의 공간(저장소) 만들기

### DB만들기(SQL Server)

나만의 데이터베이스 만들기

---

## 컴퓨트 엔진 생성

* 컴퓨트 엔진 = CE(Compute Engine)
* AWS EC2와 동일

1. 좌측상단 메뉴 → 컴퓨팅 → VM 인스턴스

    처음 들어오는 서비스는 "활성화"해 주어야 함 (1~10분 정도 걸림)

2.  중앙 상단의 "인스턴스 생성"
3.  설정
    - 이름: bob-1
    - f1-micro 선택 (1core 613MB 메모리)
    - Ubuntu 16.04 LTS 선택
    → Create 클릭 

4. Internal IP, External IP 확인
5. SSH를 브라우저에서 연결 해 봄
    - 편함
6. 기본 계정은 gmail account로 만들어짐 → 보안 상 좋음, 키 관리 필요 X

## 컴퓨트 엔진 사용

1. nginx 설치

    ```bash
    sudo apt install nginx -y
    curl localhost
    ```

2. 인스턴스 이름을 클릭하여 설정 진입 → 우측상단의 Edit 클릭
    - Firewalls에서 Allow **HTTP traffic 선택**

      - 자동으로 Network Tags에 http-server가 붙게 됨

## CloudShell 사용

1. 우측 상단의 CloudShell 아이콘 클릭
    - CloudShell은 개발을 할 수 있는 임시 인스턴스 → VM과 전혀 무관
    - 클릭하는 순간 생성됨 → 다소 시간이 걸림
    - 데이터는 인스턴스가 지워지더라도 유지됨
    - gcloud의 커맨드를 활용하는 공간
        - gcloud → GCP의 모든 것을 CLI로 할 수 있음
2. gcloud compute zones list | grep us-central1
3. gcloud config set compute/zone us-central1-a
    - 기본 존을 세팅하지 않으면 gcloud XXXX —zone XXX을 쳐야 함
    - gcloud config get-value compute/zone 을 이용하여 현재 기본 존 확인 가능
    - gcloud config list를 이용하여 설정들을 확인할 수 있음
4. gcloud compute instances create "bob-2" --machine-type "g1-small" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default"
    - 인스턴스를 생성
5. gcloud compute machine-types list
    - 인스턴스 타입 리스트 출력
6. gcloud compute images list | grep debian
    - 이미지 리스트 출력 (데비안)
7. gcloud compute instances list
8. 인스턴스 관리
    - gcloud compute instances describe "bob-2"
        - 인스턴스 정보 출력
    - gcloud compute instances stop "bob-2"
        - 인스턴스 종료
    - gcloud compute instances start"bob-2"
        - 인스턴스 시작
    - gcloud compute instances delete"bob-2"
        - 인스턴스 삭제
9. gcloud compute ssh bob-2
    - bob-2 인스턴스에 접속할 수 있음
    - 내부에서 아래의 명령 수행
        1. sudo apt update
        2. sudo apt install apache2 php php-mysql -y
    - 이후 이전과 동일하게 firewalls 설정


## 키 생성

 - C:/Users/<id>/.ssh/id_rsa     (개인키)
 - C:/Users/<id>/.ssh/id_rsa.pub    (공개키)
                            
- puttygen 을 이용하여 퍼블릭키를 ssh에 입력해줘야함
  - 올리는곳??    
     메뉴 > 컴퓨트엔진(compute-engine) > 좌측메뉴에서    
     메타데이터(키-벨류 를 넣는곳)    
     중앙 상단의 "메타데이터/SSH키" > SSH 선택   
     수정 > 항목추가 > 공개키 삽입   
  - puttygen 으로 만들었을때, 주석(comment) 에 쓴 것 이 이름이 됨 (= 메타데이터에 들어간 이름)    
    사용자이름(shpark-bob-test1)     
---

## Bucket 

* 스토리지 공간임 (AWS의 S3 저장소와 유사)

---
- 커맨드 종류
    - gcloud (compute외 다양한 것들, k8s 등)
    - gsutil이라는 커맨드를 사용하면 bucket 생성 및 관리가 가능
- Bucket의 위치
    - 개별리전
    - 멀티리전
    - Etc.
1. export LOCATION=ASIA 
    - 임의로 리눅스 환경 변수를 설정
2. gsutil -l $LOCATION gs://$DEVSHELL_PROJECT_ID
    - 프로젝트 이름으로 버킷 만들기
3. 버킷 관련 명령어
    - gsutil ls
    - gsutil cl
    - gsutil mv
    - gsutil rm
    - gsutil rb
        - 버킷 완전 삭제
        
4-1. gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png       
  - 퍼블릭한 공간 (보안상 all 허용은 아님)
  - cloud-training 이라는 버킷이 있음
  - 여기서 먼저 사진을 가져옴.

4-2. gsutil cp gs://$DEVSHELL_PROJECT_ID/gcpfci/my-excellent-blog.png my-excellent-blog.png
    - 위와 같은 방법으로 저장공간을 사용할 수 있음
    
5. 이미지를 이동
    - gcloud compute scp my-excellent-blog.png bob-1:~/
    - gcloud compute scp my-excellent-blog.png bob-2:~/

6. html 파일 생성(우측 상단에 있는 연필 아이콘으로 "편집기 열기")

    ```html
    <img src="https://storage.googleapis.com/{$bucket_경로}/my-excellent-blog.png">
    ```

7. gcloud compute scp index.html bob-1:

8. gcloud compute ssh bob-1   (또는 웹에서 CE > SSH)

9. sudo mv index.html /var/www/html/
 * 이미지 파일이 왜 안불려올까? 왜 X박스로 이미지가 꺠져서 나올까??
 * 퍼블릭 유저가 접근 할 수 있는 권한이 없음...

10. 권한 추가
    - gsutil acl get gs://$DEVSHELL_PROJECT_ID/myexcellent-blog.png
        - 권한 확인
    - gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
        - 권한 추가

---

## DB 만들기

이렇게 DB를 만들어 주는 게 SaaS(Software as a Service)

- Admin 관점에서 SaaS를 사용하면 서비스만 관리하면 됨
1. SQL → MySQL
2. 정보 입력
    - ID: bob-db
    - PW: 알아서
3. 생성될 때 까지 기다림...
    - DB에 연결하기 위한 경로
        - <project-id>:<zone>:<db-name>
4. 사용자 생성
    1. Users 진입
    2. Create user account 클릭
    3. 정보 입력
        - Username: dbuser
        - Password: 알아서
        - Host name: Allow any host (%)
    4. Create 클릭
5. 네트워크 추가
    1. Connections 진입
    2. Add network 클릭
    3. 정보 입력
        - Name: web front
        - Network: 웹 서버의 퍼블릭 ip
    4. Save 클릭
6. DB의 공개 IP주소 복사 해 두기

---

# DB 연동하기

1. bob-1 서버로 이동
2. 테스트 페이지 작성
    - sudo vi /var/www/html/index.php

        ```html
        <html><head><title>Hello BOB DB</title></head>
        <body>
        <h1>Hello, BOB!!</h1>

        <?php
        $dbserver="XXX.XXX.XXX.XXX";
        $dbuser="dbuser";
        $dbpassword="databaseuser";
        // 나중에 실제 코딩에서는 암호를 이렇게 소스에 하드코딩 하면 안됨!!

        $conn = new mysqli($dbserver, $dbuser, $dbpassword);

        if (mysqli_connect_error()) {
                echo ("DB Conn Failed: " .mysqli_connect_error());
        } else {
                echo ("DB Conn succeeded.");
        }
        ?>

        <p>
        <img src="https://storage.googleapis.com/fastcampus-222222/my-excellent-blog.png">
        </body>
        </html>
        ```

3. php 설치
    - sudo apt update
    - sudo apt install php7.0-fpm
    - sudo apt install php-mysqli
4. nginx php-fpm 연결
    - sudo vi /etc/nginx/sites-available/default
    - index.php 추가
    - .php 처리 해 주는 곳 주석 해제
    - sudo vi /etc/nginx/sites-available/default      
 에서 index index.html 있는곳 찾아서 index.php 추가     
39번째줄 추가      

그 아래 각종 확장자 중 .php 처리 해 주는곳에서     
주석 해지     
```
location ~ \.php$ {
  include snippets/fastcgi-php.conf;
  fastcgi_pass unix:/run/php/php7.0-fpm.sock;
}
```

---

# 인스턴스 용량 늘리기

1. 인스턴스 이름 클릭
2. edit 클릭
3. Add new disk
4. 정보 입력
    - Name: 알아서
    - Type: SSD persistent disk
    - Etc.
5. Done 클릭
6. Save 클릭

## 늘려진 용량 확인

1. 해당 인스턴스 접속
2. dmesg -T 입력
    - hdd 추가 된 것을 확인할 수 있음
3. sudo fdisk -l
    - 하드디스크가 어디에 붙어있는 지 확인 가능
4. sudo mkfs -t ext4 {$하드디스크_위치}
    - Ex. sudo mkfs -t ext4/dev/sdb
5. 마운트 할 폴더 생성
    - Ex. sudo mkdir /data
6. mount {$마운트_위치} {$하드디스크_위치}
    - Ex. mount /dev/sdb /data
7. 알아서 권한 설정

재부팅시에 알아서 마운트 되도록 하기 위해서 /etc/fstab 수정

---

# 타임존 설정

```bash
sudo rm -rf /etc/localtime
sudo ln -s /user/share/zoneinfo/Asia/Seoul /etc/localtime
```
