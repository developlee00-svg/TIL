# AWS SAA 필수 개념 정리

---

## 1. 컴퓨팅

* **EC2 구매 옵션** — On-Demand(단기/예측불가), Reserved(1~3년 약정, 최대 72% 절감), Savings Plans(유연한 약정), Spot(최대 90% 절감, 중단 허용 워크로드), Dedicated Host(라이선스/규정 준수)
* **Auto Scaling** — Target Tracking(지표 목표값 유지), Step Scaling(단계별), Scheduled(예측 가능한 패턴), Predictive(ML 기반 예측)
* **Placement Group** — Cluster(저지연/HPC), Spread(장애 격리, AZ당 7개), Partition(대규모 분산, HDFS/Kafka)
* **Lambda** — 최대 15분, 10GB 메모리, 서버리스 이벤트 기반. VPC 접근 시 ENI 필요
* **ECS vs EKS vs Fargate** — ECS(AWS 네이티브 컨테이너), EKS(관리형 쿠버네티스), Fargate(서버리스 컨테이너, EC2 관리 불필요)

---

## 2. 스토리지

* **S3 스토리지 클래스** — Standard → Standard-IA → One Zone-IA → Glacier Instant → Glacier Flexible → Glacier Deep Archive. Intelligent-Tiering은 액세스 패턴 불명확 시
* **S3 핵심 기능** — Versioning(실수 삭제 방지), Lifecycle(자동 티어 전환), Replication(CRR/SRR), Transfer Acceleration(글로벌 업로드 가속), Multipart Upload(100MB 이상 권장)
* **S3 보안** — 버킷 정책, SSE-S3/SSE-KMS/SSE-C 암호화, Presigned URL(임시 접근), Object Lock(WORM)
* **EBS** — gp3(범용, 기본), io2(고IOPS/멀티어태치), st1(처리량 최적화 HDD), sc1(콜드 HDD). AZ 종속, 스냅샷으로 AZ 간 이동
* **EFS** — NFS, 다중 AZ, Linux 전용, 동시 다중 인스턴스 마운트
* **FSx** — Windows File Server(SMB/AD), Lustre(HPC/ML, S3 연동), NetApp ONTAP, OpenZFS
* **Storage Gateway** — File(NFS/SMB→S3), Volume(iSCSI), Tape(백업). 온프레미스↔AWS 하이브리드

---

## 3. 데이터베이스

* **RDS** — Multi-AZ(고가용성, 동기 복제, 자동 페일오버), Read Replica(읽기 확장, 비동기, 최대 15개, 리전 간 가능)
* **Aurora** — MySQL/PostgreSQL 호환, 6개 복제본(3AZ), 스토리지 자동 확장(128TB), Aurora Serverless(가변 워크로드), Global Database(리전 간 1초 미만 복제)
* **DynamoDB** — 완전관리 NoSQL, 밀리초 지연. DAX(마이크로초 캐시), Global Tables(다중 리전), Streams(변경 캡처), TTL, On-Demand vs Provisioned
* **ElastiCache** — Redis(복제/백업/정렬셋), Memcached(단순 캐시, 멀티스레드)
* **용도별 선택** — Redshift(DW/OLAP), Neptune(그래프), DocumentDB(MongoDB), Keyspaces(Cassandra), Timestream(시계열), QLDB(원장)

---

## 4. 네트워킹

* **VPC 기본** — 서브넷(AZ 종속), IGW(인터넷), NAT Gateway(프라이빗→아웃바운드, AZ별 배치 권장), Route Table
* **보안** — Security Group(스테이트풀, 허용만), NACL(스테이트리스, 허용/거부, 서브넷 레벨)
* **VPC 연결** — Peering(1:1, 전이 불가), Transit Gateway(허브앤스포크, 다중 VPC/VPN), VPC Endpoint(Gateway: S3/DynamoDB 무료, Interface: PrivateLink)
* **온프레미스 연결** — Site-to-Site VPN(인터넷 경유, 빠른 구축), Direct Connect(전용선, 안정적 대역폭, 구축 수주 소요)
* **ELB** — ALB(L7, 경로/호스트 라우팅), NLB(L4, 초저지연, 고정 IP), GWLB(방화벽/어플라이언스)
* **Route 53 라우팅** — Simple, Weighted(가중치), Latency(지연), Failover(장애조치), Geolocation(위치), Multivalue
* **CloudFront** — CDN, 엣지 캐싱, OAC로 S3 보호, 커스텀 오리진 가능
* **Global Accelerator** — Anycast IP, TCP/UDP 가속, 정적 IP 2개 제공

---

## 5. 보안 및 자격 증명

* **IAM** — 정책은 최소 권한 원칙, Role(임시 자격 증명, EC2/Lambda에 권장), 크로스 계정 접근은 Role Assume
* **KMS** — 관리형 키 암호화, 자동 로테이션, 리전 종속(멀티리전 키 별도)
* **Secrets Manager vs Parameter Store** — Secrets Manager(자동 로테이션, RDS 통합, 유료), Parameter Store(무료 티어, 로테이션 수동)
* **WAF** — L7 웹 공격 방어(SQL Injection, XSS), ALB/CloudFront/API Gateway에 연결
* **Shield** — DDoS 방어. Standard(무료), Advanced(전담 대응팀)
* **GuardDuty** — 위협 탐지(ML 기반), Inspector(EC2/ECR 취약점 스캔), Macie(S3 민감정보 탐지)
* **Cognito** — User Pool(인증/로그인), Identity Pool(임시 AWS 자격 증명)

---

## 6. 애플리케이션 통합

* **SQS** — Standard(무제한 처리량, 최소 1회 전달), FIFO(순서 보장, 300TPS), DLQ(실패 메시지), Visibility Timeout, Long Polling
* **SNS** — Pub/Sub, 팬아웃 패턴(SNS→다중 SQS), 이메일/SMS/Lambda 구독
* **EventBridge** — 이벤트 버스, 스케줄링, SaaS 통합, 서비스 간 이벤트 라우팅
* **Kinesis** — Data Streams(실시간, 샤드 기반, 리플레이 가능), Firehose(준실시간 적재, S3/Redshift/OpenSearch), Managed Flink(스트림 분석)
* **Step Functions** — 서버리스 워크플로 오케스트레이션, 상태 머신
* **API Gateway** — REST/HTTP/WebSocket, 스로틀링, 캐싱, Lambda 통합

---

## 7. 분석 및 데이터

* **Athena** — S3 데이터 서버리스 SQL 쿼리, 스캔량 과금(파티셔닝/Parquet로 절감)
* **Glue** — 서버리스 ETL, Data Catalog, Crawler
* **Redshift** — 컬럼 기반 DW, Spectrum(S3 직접 쿼리)
* **EMR** — 관리형 Hadoop/Spark 클러스터
* **QuickSight** — BI 대시보드

---

## 8. 모니터링 및 관리

* **CloudWatch** — 지표/로그/알람, 커스텀 지표(메모리 등은 에이전트 필요), Logs Insights
* **CloudTrail** — API 호출 감사 로그(누가 무엇을 했는가)
* **Config** — 리소스 구성 변경 추적, 규정 준수 평가
* **Trusted Advisor** — 비용/성능/보안/한도 권고
* **Systems Manager** — Session Manager(SSH 없이 접속), Patch Manager, Run Command
* **Organizations** — 다중 계정 관리, SCP(권한 상한), Consolidated Billing

---

## 9. 재해 복구 (DR)

* **전략 4단계 (RTO/RPO 순)** — Backup & Restore(저비용/느림) → Pilot Light(핵심만 대기) → Warm Standby(축소 버전 상시 가동) → Multi-Site Active-Active(고비용/즉시)
* **RTO** — 복구 목표 시간 / **RPO** — 허용 데이터 손실 시간

---

## 요약

| 도메인 | 핵심 포인트 |
|---|---|
| **컴퓨팅** | EC2 구매옵션, Auto Scaling 정책, Lambda 제한 |
| **스토리지** | S3 클래스/수명주기, EBS vs EFS vs FSx 선택 |
| **DB** | Multi-AZ vs Read Replica, Aurora, DynamoDB DAX |
| **네트워킹** | SG vs NACL, VPC 연결 옵션, ELB 유형, Route 53 라우팅 |
| **보안** | IAM Role 우선, KMS, WAF/Shield/GuardDuty 구분 |
| **통합** | SQS vs SNS vs Kinesis 선택 기준 |
| **DR** | RTO/RPO에 따른 4가지 전략 |

> SAA 문제의 핵심은 "요구사항(비용/성능/가용성/보안)에 가장 적합한 서비스 선택"이다. 각 서비스의 차별점과 선택 기준을 비교 관점으로 암기하는 것이 효율적이다.