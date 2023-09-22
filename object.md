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
