## EBS CIS Driver Controller
`eksctl get iamserviceaccount --cluster myeks --name ebs-csi-controller-sa`

`eksctl create addon --name aws-ebs-csi-driver --cluster myeks --service-account-role-arn <ROLE_ARN> --force`

`eksctl get addon --cluster myeks`

`kubectl get pod -n kube-system -l "app.kubernetes.io/component=csi-driver"`

---

## Metrics Server
`kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml`

`kubectl get deployment metrics-server -n kube-system`

---

## Cluster Autoscler
`curl -O https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml`

`cluster-autoscaler-autodiscover.yaml`

```yaml

...
apiVersion: apps/v1
kind: Deployment
...
spec:
  template
    metadata:
      ...
      annotations:
        ...
        cluster-autoscaler.kubernetes.io/safe-to-evect: 'false'
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
        - image: k8s.gcr.io/autoscaling/cluster-autoscaler:v1.24.0
            ...
          command:
            - ./cluster-autoscaler
            - --v=4
            - --stderrthreshold=info
            - --cloud-provider=aws
            - --skip-nodes-with-local-storage=false
            - --expander=least-waste
            - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/myeks
            - --balance-similar-node-groups
            - --skip-nodes-with-system-pods=false
...


```
`kubectl create -f cluster-autoscaler-autodiscover.yaml`

`kubectl logs -f deployment/cluster-autoscaler -n kube-system`

---