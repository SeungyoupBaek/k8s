### IRSA(IAM Role for Service Account)
- OpenID Connector(OIDC) Identity Provider와 STS를 활용하여 쿠버네티스의 Service Account에 IAM Role을 할당할 수 있게 하는방식

<img src="../images/irsa.png" alt="IRSA image">

#### Practice

```shell
# Create Cluster
$ eksctl create cluster my-cluster
# Create IAM Identity provider
$ eksctl utils associate-iam-oidc-provider --region=ap-northeast-2 --cluster=my-cluster --approve
# Create IAM Role and ServiceAccount
$ eksctl create iamserviceaccount --cluster=my-cluster --name=test-role --attach-policy-arn=arn:aws:iam:aws:policy//AmazonS3FullAccess --approve
```

- Deploy Pod

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pod-a
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pod-a
  template:
    metadata:
      labels:
        app: pod-a
    spec:
      containers:
        - name: ubuntu
          image: ubuntu:latest
          imagePullPolicy: Always
          command: [ "/bin/bash", "-c", "--" ]
          args: [ "while true; do sleep 30; done;" ]
      serviceAccountName: test-role
      nodeSelector:
        kubernetes.io/hostname: ip-192-168-59-173.ap-northeast-2.compute.internal
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pod-b
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pod-b
  template:
    metadata:
      labels:
        app: pod-b
    spec:
      containers:
        - name: ubuntu
          image: ubuntu:latest
          imagePullPolicy: Always
          command: [ "/bin/bash", "-c", "--" ]
          args: [ "while true; do sleep 30; done;" ]
      nodeSelector:
        kubernetes.io/hostname: ip-192-168-59-173.ap-northeast-2.compute.internal
```

- Check role
```shell
$ kebectl get po -o wide
$ aws s3 ls | wc -l 12
```

### Dataplane
- Self-managed nodes
  - ASG 기반 EC2 형태로 Worker Node가 동작
  - EKS의 Worker로써 동작하기 위한 설정 구성 필요
  - 비용은 EC2 사용 기준과 동일
- Managed Node Group
  - ASG 기반 EC2 형태로 Worker Node가 동작
  - AWS 제공 기능으로 노드의 프로비저닝과 수명 주기 관리를 자동화
  - 원하다면 EC2의 세부적인 구성도 가능
- Fargate
  - 서버리스 형태로 필요한 컴퓨팅 용량만 할당 받아 동작
  - 서버리스임으로 VM을 프로비저닝하고 관리할 필요 없음
  - 서버 유형 및 노드 최적화 불필요

### WorkNode
- 모니터링
  - Tool : Prometheus, EFK, Datadog 등
  - Observability : 시스템의 상태뿐만 아니라 동작 등으로 모든 숨겨진 이슈 파악
