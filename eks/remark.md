### EKS Node 생성이 정상적으로 되지 않을때
- IAM Role 체크

### Ingress 삭제되지 않을때
```shell
$ kubectl edit ingress argocd-ingress -n argocd
# finalizers 제거
```
