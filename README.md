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

Before moving on, let's overwrite the IP from our Ingress to our minikube ip
```
sed -i "s/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+\.nip\.io/$(minikube ip).nip.io/g" overrides/grafana-override.yaml
```

Let's add all Helm Charts that we are going to need
```
#Kube-prometheus-stack
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add fluent https://fluent.github.io/helm-charts
helm repo add loki https://grafana.github.io/helm-charts
helm repo update
```

After that deploy kube-prometheus-stack

```
helm install prom-operator prometheus-community/kube-prometheus-stack -f overrides/prometheus-override.yaml -f overrides/grafana-override.yaml --namespace=monitoring --create-namespace
```

Install fluentbit

```
helm upgrade --install fluent-operator fluent/fluent-operator --namespace=fluentbit --create-namespace -f overrides/fluentbit-override.yaml 
```

Add CRs

```
kubectl apply -f fluentbit/
```

Install loki

```
helm install --values overrides/loki-override.yaml loki grafana/loki --namespace=monitoring

#Apply all crds
kubectl apply -f fluentbit/loki/
```

Access the grafana dashboard using the minikube ip

```
<minikube-ip>.nip.io/grafana
```

The default user/pass for this grafana deploy is

```
admin
prom-operator
```