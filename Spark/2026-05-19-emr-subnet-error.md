# EMR 클러스터가 VPC Subnet 없으면 즉시 종료됨

> 2026-05-19 · Spark · EMR

## 증상

```
Subnet is required: The specified instance type m5.xlarge can only be used in a VPC.
```

EMR 클러스터가 생성 2초만에 `VALIDATION_ERROR`로 종료됨.

## 원인

- `m5.xlarge`는 EC2-Classic이 아닌 **VPC 전용** 인스턴스 타입
- 계정에 default VPC가 없으면 EMR이 자동으로 못 찾음
- SK Planet 같은 조직 계정은 default VPC 삭제돼 있는 경우 많음

## 해결

`JOB_FLOW_OVERRIDES`의 `Instances` 블록에 `Ec2SubnetId` 추가:

```python
"Instances": {
    "Ec2SubnetId": "subnet-xxxxxxxx",  # 퍼블릭 서브넷
    "InstanceGroups": [...],
    ...
}
```

## 서브넷 선택 기준

- 퍼블릭 서브넷 (라우팅 테이블의 `0.0.0.0/0` 대상이 `igw-`로 가는 것)
- 가용 영역은 아무거나
- VPC 콘솔 → 서브넷 → 필터로 찾기

---

🔗 블로그 정리: [데이터 처리 6 - EMR + Spark](https://dev-lee.tistory.com/95)