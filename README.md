Requeriments:

* Kubernetes
* Helm
* Minikube

To start this project, first start a minikube cluster

```
Minikube start
user can customize it adding more nodes or naming something else, example:

minikube start --nodes $NODE_NUMBER  -p $PROFILE_NAME --container-runtime=containerd
```

And enable ingress and metrics server
```
minikube addons enable ingress
minikube addons enable metrics-server
```

After that deploy kube-prometheus-stack

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prom-operator prometheus-community/kube-prometheus-stack -f overrides/prometheus-override.yaml --namespace=monitoring --create-namespace
```

Install fluentbit
```
helm repo add fluent https://fluent.github.io/helm-charts
helm repo update
helm upgrade --install fluent-operator fluent/fluent-operator --namespace=fluentbit --create-namespace -f overrides/fluentbit-override.yaml 
```

Add CRs
```
kubectl apply -f fluentbit/exporter.yaml
kubectl apply -f fluentbit/telegraf.yaml
```

When all pods are up and running, the user can the deploy the ingress found in this repo, changing the ip address from the yaml to the one the user got from the command "minikube ip".

```
minikube ip
```

or the user can run this command to make this change automatically

```
sed -i "s/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+\.nip\.io/$(minikube ip).nip.io/g" grafana-ingress.yaml
```
Access the grafana dashboard using the minikube ip

```
<minikube-ip>.nip.io
```

The default user/pass for this grafana deploy is
```
admin
prom-operator
```