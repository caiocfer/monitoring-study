Requeriments:

* Kubernetes
* Helm
* Minikube

To start this project, first start a minikube cluster

```
Minikube start
user can customize it adding more nodes or naming something else, example:

minikube start --nodes $NODE_NUMBER  -p $PROFILE_NAME
```

And enable ingress access to the minikube
```
minikube addons enable ingress
```

After that deploy kube-prometheus-stack

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
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