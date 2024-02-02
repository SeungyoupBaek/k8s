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

### Ingress 정상적으로 생성되지 않을 경우
1. ServiceAccount가 정상적으로 생성됐는지 확인한다.
```shell
# eksctl로 생성해야 정상적으로 IAM Role이 연결됐었다.
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
2. ServiceAccount가 정상적으로 생성된 상태에서 AWS Load Balancer Controller 설치
```shell
# AWS Load Balancer Controller log 확인
kubectl logs -f -n kube-system -l app.kubernetes.io/instance=aws-load-balancer-controller
```

- couldn't auto-discover subnets: unable to resolve at least one subnet (0 match VPC and tags)
```shell
$ aws ec2 create-tags --resources $SubnetID --tags "Key=kubernetes.io/role/elb,Value=1"
```
