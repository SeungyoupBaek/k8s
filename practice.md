### install kubectl
ref : https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-macos/
```shell
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s
https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"
https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"
```

### zsh alias setting
```shell
$ vi ~/.zshrc

      # kubectl auto completion
      source <(kubectl completion zsh)
      # kubectl - k alias
      alias k=kubectl
      compdef __start_kubectl k
      
$ source ~/.zshrc
```

### kubeconfig context
```shell
# context switch
$ kubectl config use-context minikube # check current context
$ kubectl config current-context
# get context list
$ kubectl config get-contexts
```

### command
```shell
# check node
$ k get node
# check namespace 
$ k get ns
# check pod
$ k get pod
$ k get pod -A $ k get all
$ k get all -A
```

### pod command
```shell
# start nginx
$ k run nginx --image=nginx $ k get pod
$ k describe pod nginx
# check minikube dashboard 
$ minikube dashboard
# delete pod
$ k delete pod nginx $ k get pod
```
