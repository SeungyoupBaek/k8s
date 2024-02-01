### EKS Node 생성이 정상적으로 되지 않을때
- IAM Role 체크

### Ingress 삭제되지 않을때
```shell
$ kubectl edit ingress <ingress명> -n argocd
# finalizers 제거
# or
$ kubectl patch ingress <ingress명> -p '{"metadata":{"finalizers":[]}}' --type=merge
$ kubectl delete ingress <ingress명>
```

### LoadBalancerController 삭제
```shell
$ helm delete aws-load-balancer-controller.md -n kube-system
$ eksctl delete iamserviceaccount --cluster <cluster-name> --namespace kube-system --name aws-load-balancer-controller.md
```
### LoadBalancer ServiceAccount
```shell
$ eksctl create iamserviceaccount \
  --cluster=<cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --region ap-northeast-2 \
  --approve
```
