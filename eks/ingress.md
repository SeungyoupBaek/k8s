## Ingress

- 주요기능
  - 경로 기반 라우팅
  - 가상 호스트 기반 라우팅
  - SSL 인증서 관리
  - 로드 밸런싱

---

### AWS Load Balancer Controller
- kubernetes 환경에서 외부 CSP(Cloud Service Provider)의 로드 밸런서 서비스를 관리하기 위한 오픈 소스 프로젝트
- kubernetes application을 위한 로드 밸런서를 생성, 구성 및 관리하는 역할 수행
- 주요기능
  - kubernetes ingress 리소스를 기반으로 ALB or NLB를 생성하고 관리
  - AWS Auto Scaling Group과 통합

### External DNS Controller
- ingress 리소스에 ingress에 사용할 DNS를 등록하면 이를 기반으로 DNS Controller가 AWS Route53 DNS 레코드를 자동으로 관리하는 오픈 소스 컨트롤러
- 실행 예시
  - ingress 리소스에서 www.example.com 도메인에 대한 서비스 생성
  - AWS External DNS Controller가 이를 자동으로 감지하고 AWS Rout53에서 www.example.com에 대한 새 DNS 레코드를 생성
  - 사용자가 www.example.com에 접근하면 자동으로 쿠버네티스 클러스터의 해당 서비스로 라우팅

### IRSA 설정 단계
1. kubernetes에서 Service Account 생성
2. AWS IAM에서 Service Account를 위한 IAM 역할(Role)과 정책(Policy)를 생성
3. kubernetes cluster에서 IRSA 리소스를 생성하고 Service Account와 IAM 역할을 연결
4. Pod 매니페스트에서 Service Account를 사용하도록 지정

### Install
https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.6/deploy/installation/
