## Architecture
<img src="images/architecture.png" alt="Kubernetes architecture image">

### etcd
- 쿠버네티스에서 필요한 모든 데이터를 키-값 형태로 저장하는 데이터베이스
- etcd가 다운되면 모든 컴포넌트가 미아가 되기 때문에 가용성이 매우 중요
- 클러스터링하여 분산 실행하는 RSM(Replicated State Machine) 구조

```shell
# etcd pod 상세 확인
$ k describe pod etcd-minikube -n kube-system
```

### kube-apiserver
- 쿠버네티스 API를 제공하는 핵심 구성 요소
- 쿠버네티스 프론트앤드로서 클러스터로 온 요청의 유효성 검증
- 다른 컴포넌트 간 통신을 중재
- kubectl 유틸리티가 접근하는 주체

### kube-scheduler
- 클러스터 안에서 자원 할당이 가능한 노드 중 알맞은 노드를 선택하는 역할
- Label / Selector / Affinity / Taint / Toleration 기능과 함께 동작
- pod 스케줄링 분류
  - 사용자가 특정 노드에 pod를 배치하고 싶을 때
    - nodeSelector
    - Node Affinity
    - Node Anti-Affinity
    - Inter Pod Affinity
    - Inter Pod Anti-Affinity
  - 관리자가 특정 노드에는 pod가 배치되는 것을 막고 싶을 때
    - Taints
    - Tolerations


  **Taints and Toleration**
  - 어떤 pod가 어떤 node에 스케줄링 될 수 있는지를 제한
  - **Taints** : node가 가지게 되는 성격(ex . taint: blue)
  - **Toleration** : pod가 가지게 되는 taint에 대한 toleration(ex . toleration: blue)
