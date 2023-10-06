## Object
- kubernetes 시스템 내에서 영속성을 가지는 object
- cluster의 의도한 상태를 나타내기 위해 object를 이용
- status 필드는 kubernetes 시스템과 컴포넌트에 의한 object의 현재 상태를 나타내며, kubernetes control plane은 이 status를 사용자가 의도한 상태와 일치시키기 위해 끊임없이 능동적으로 관리

### 의도한 상태
- object에 대한 기본적인 정보와 의도한 상태를 기술한 object spec을 제시
- object 생성을 위한 kubernetes API 요청은 JSON 형식 정보를 포함
- 대부분의 경우 정보를 .yaml 파일로 kubectl에 제공
- kubectl은 API 요청이 이루어질 때 JSON 형식으로 정보를 변환

### kubectl apply
- 선언적 형태(Declarative)
- object가 없으면 create하고, 있으면 replace하는 등 더 유연하고 지능적으로 동작 

### kubectl create, replace
- 명령적 형태(Imperative)
- kubectl run --image=nginx nginx

### Yaml의 기본 구조
- apiVersion(string)
- kind(string)
- metadata(dictionary)
- spec(dictionary)
- status(dictionary)
```yaml
apiVersion: v1
kind: Service
metadata: 
  name: test-service
spec:
  type: Loadbalancer
  ports:
    - targetPort: 80
      port: 80
      NodePort: 30008
```

### status(dictionary)
- object의 실제 상태를 기술
- kubernetes control plane은 object의 실제 상태를 의도한 상태에 일치시키기 위한 방향으로 동작

### Node(Worker node)
- 워크로드가 돌아가는 컨테이너를 배치하는 물리(가상) 머신
- control plane에 의해 관리
- 일반적인 운영환경에서는 multi node로 운영
- 각 노드는 kubelet / container-runtime / kube-proxy 가 포함
```shell
# 노드 리스트 확인
$ k get node
$ k get node -o wide
$ k get node <node name> -o yaml
$ k describe node <node name>
$ k cordon <node name>
$ k uncordon <node name>
$ k drain <node name> --ignore-daemonsets
```

### Namespace
- 동일 물리 클러스터를 기반으로 하는 복수의 가상 클러스터를 지원하는 개념
- 클러스러를 논리적으로 나누고 액세스를 제한하여 리소스를 생성, 관리
- 논리적으로 구분되지만 격리된 것은 아님(격리를 위해서는 network policy 같은 다른 오브젝트를 추가로 사용해야 함)
- 예시
  - default - pod, deployment가 생성될 떄 기본적으로 생성되는 namespace
  - kebe-system - DNS, kube-proxy나 kubernetes dashboard처럼 시스템 제어 리소스가 사용하는 namespace
  - kube-public - 전체 클러스터 리소스에 대한 가시성을 제공하는 경우 사용(일반적으로 사용되지 않음)
  - custom namespace
    - prometheus, argo, istio 등등의 시스템 관련 솔루션들은 독자 namespace를 할당
    - microservice 별로 namespace를 할당하여 논리적으로 분리
- resource quota는 namespace에 할당할 수 있는 리소스를 제한하는 역할
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev

----

apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: "5Gi"
    limits.cpu: "10"
    limits.memory: "10Gi"
```

```shell
# namespace 생성 및 확인
$ k create ns my-app
$ k get ns --show-labels
$ k delete ns my-app

# 특정 namespace 내에 있는 자원 확인
$ k get pod -n kube-system
$ k get all -n kube-system

# 모든 namespace 내의 자원을 확인
$ k get all -A

# namespace에 종속된 리소스 확인
$ k api-resources --namespaced=true
$ k api-resources --namespaced=false
```

### Pod
- 최소 단위 쿠버네티스 객체
- docker 컨테이너와는 조금 다르게, pod는 하나 이상의 컨테이너를 포함 가능
- 애플리케이션 컨테이너(하나 또는 다수), 스토리지, 네트워크 등의 정보를 포함

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-prod
  namespace: app
  labels: 
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
      ports:
      - containerports: 8080
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp2-prod
  namespace: app
  labels: 
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
    - name: redis-container
      image: redis:3.2
```

- k taint nodes node1 app=blue:NoSchedule
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-prod
  namespace: app
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
  tolerations:
    - key: "app"
      operator: "Equal"
      value: "blue"
      effect: "NoSchedule"
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-prod
spec:
  containers:
    - name: nginx
      image: nginx
  affinity:
    nodeAffinity:
    requiredDuringSchedulingIgnoreDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key : size
          operator: In(|NotIn|Exists)
          value:
          - Large
          - Medium
```