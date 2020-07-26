# AWS setting

# VPC 생성

1. Service → VPC 이동
2. Your VPCs 선택
3. Create VPC 클릭
4. Name tag, IPv4 CIDR block 입력
5. VPC Create 클릭

# Subnet 생성

1. Service → VPC 이동
2. Subnet 선택
3. Create Subnet 클릭
4. Name tag 입력
5. VPC 선택, AZ 선택
6. IPv4 CIDR block 입력
7. Create 클릭

# EC2 Instance 생성

# Internet Gateway생성 & 연결

1. Service → VPC 이동
2. Internet Gateway 선택
3. Create internet gateway 클릭
4. Name tag 입력 (Ex. BOB_VPC0_IGW)
5. Create intent gateway 클릭
6. 생성한 gateway 선택
7. Actions 클릭
8. Attach to VPC 클릭
9. VPC 선택
10. Attach 클릭

# Router 연결

1. Service → VPC 이동
2. Route Table 선택
3. VPC 선택
4. Actions 클릭
5. Edits Routes 클릭
6. Add Routes 클릭
7. Destination  에 0.0.0.0/0 입력
8. Target을 본인의 Internet gateway로 설정
9. Save routes 클릭

# 접속 테스트

- EC2 인스턴스에 연결

# Nginx 설정

- 서버 세팅 및 방화벽 수정

# 프라이빗 서브넷용 EC2 인스턴스 생성

    - Auto-assign Public IP를 Disable로 변경
    - 원래는 SG를 따로 만들어야 하지만 실습이므로 동일하게 사용
    - 키페어는 새로 만들볼 것 (userXX_이름_key2)
    - 인스턴스 이름은 userXX_이름_db1이라고 설정

# NAT GW 생성

1. Service → VPC 이동
2. NAT Gateway 선택
3. Create NAT Gateway 클릭
4. Subnet 및 Elastic IP 선택
5. Create a NAT Gateway 클릭
6. Route Tables로 이동하여 라우터 생성 및 서브넷 연결

# 프라이빗 EC2 접속

- 두 가지 방법
    - 내부에서 외부로 나갈 때
        - IGW ← Public Router ← NACL ← NAT GW ← Private Router ← SG ← DB
    - 배스쳔 호스트를 통해 외부에서 내부로 접속할 때
        - IGW → Public Router → NACL → Private Router → SG → DB EC2
- 퍼블릭 서버에서 접속해야 함
- Agent Forwarding을 활용하여 키를 사용할 수 있도록 함

# Q&A

- 사실 이건 개발자들이 하는 작업이 아닌 인프라팀과 보안팀이 논의를 하려 인프라팀이 작업하는 내용임
- NAT 게이트웨이는 public에 만들고 db EC2는 기존 라우팅 테이블에 연결되지 않은 프라이빗 서브넷에 만드는 것이 맞지 않은가
    - 우리가 작업한 것이 이것임
        - nat-gw를 sub1-2에 넣음
        - public 서브넷에 sub1-1, sub1-2가 포함되어 있음
        - private 서브넷에 sub1-2, sub1-4를 넣었고, 이 둘은 게이트웨이가 nat-gw로 설정되어 있음
        - public에 EC2 하나 만들어 web1
        - private에 EC2 하나 만들어 db1
- 베스천 호스트 역할을 하는 것이 어떤 노드인가
    - private 망에 연결하기 위해 인터넷 망의 최상단 호스트를 거쳐야 함
    - nat-gw는 라우터임
    - 우리가 bastion host를 만들었고, 이를 nat-gw로 함께 쓰는 것은 가능함 (그러나, 우리가 한 방식은 아님)
- 탄력적 IP? (= 유동 IP)
    - EC2에도 공인IP가 다이나믹하게 할당됨 → 한번 할당받으면 고정IP임
        - 한시적이지만 고정IP로 사용할 수 있음

# 위 내용의 대략적 그림

<img src="/Cloud/img/learn.png" width="450px" height="300px" title="px(픽셀) 크기 설정" ></img><br/>
