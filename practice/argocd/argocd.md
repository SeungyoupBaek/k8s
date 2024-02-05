### ArgoCD install
```shell
$ sudo curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64 
$ sudo chmod +x /usr/local/bin/argocd
$ helm repo add argocd https://argoproj.github.io/argo-helm
$ helm show values argocd/argo-cd > values.yaml
# values.yaml 에서 server.insecure: true 로 수정
$ kubectl create namespace argocd
$ helm install -n argocd argocd argocd/argo-cd -f values.yaml
$ kubectl get po -n argocd
```

### ArgoCD Uninstall
```shell
$ k delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/ha/install.yaml 
```

### Check admin password
```shell
$ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### Open external service
```shell
$ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

### Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/group.name: argo-group
    alb.ingress.kubernetes.io/group.order: '1'
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS": 443}]'
    alb.ingress.kubernetes.io/security-groups: sg-0976361b63ee5107b
    alb.ingress.kubernetes.io/manage-backend-security-group-rules: "true"
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:ap-northeast-2:688814567323:certificate/259fb76a-b04f-4d2d-a824-35e7921cc75c
    alb.ingress.kubernetes.io/ssl-redirect: '443'
  name: argocd-ingress
  namespace: argocd
spec:
  ingressClassName: alb
  rules:
    - http:
        paths:
          - backend:
              service:
                name: argocd-server
                port:
                  number: 80
            path: /
            pathType: Prefix
```

### SpringBoot App DockerFile
```dockerfile
FROM adoptopenjdk/openjdk17 AS builder
COPY gradle .
COPY gradle gradle
COPY build.gradle .
COPY settings.gradle .
COPY src src
RUN chmod +x ./gradlew
RUN ./gradlew bootJar

FROM openjdk:17-jdk-alpine
COPY bulid/libs/*.jar application.jar
ENTRYPOINT ["sh","-c","java ${JAVA_OPTS} -jar application.jar"]
```
