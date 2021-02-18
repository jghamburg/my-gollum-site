# Setup for local k8s  

## Web UI  

Install web ui.  

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.4/aio/deploy/recommended.yaml
#
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
EOF
#
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF
# Retrieve token for accessing the dashboard.
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
# start k8s proxy to connect dashboard
kubectl proxy
```

## Chart Repositories  

Check if you have the insteresting chart repositories installed:  

```bash
helm repo list
# NAME   	URL
# stable 	https://kubernetes-charts.storage.googleapis.com
# bitnami	https://charts.bitnami.com/bitnami
# loki   	https://grafana.github.io/loki/charts
# oteemo 	https://oteemo.github.io/charts
```

## chartmuseum  

```bash
helm install chartmuseum stable/chartmuseum
helm repo add localcharts http://chartmuseum-chartmuseum.default.svc.cluster.local:8080
# "localcharts" has been added to your repositories
```

## Sonarqube  

```bash
helm repo add oteemo https://oteemo.github.io/charts
helm upgrade --install sonarqube --namespace default oteemo/sonarqube
```

## Localstack for local development  

```bash
helm upgrade --install localstack --namespace default localstack
```

 If you want to use a aws service from a component you can reference by k8s service url:

 ```text
 http://localstack.default.svc.cluster.local
 ```

 inside of application.yaml configuration. Or you can access the web frontend if using port forwarding:

 ```bash
 kubectl port-forward --namespace default localstack 8099
 open http://localhost:8099
 ```
 
 ## Loki Monitoring  

 Prepare installation:

```bash
k create namespace loki-stack
# namespace/logmon created
helm repo add loki https://grafana.github.io/loki/charts
helm upgrade --install loki --namespace=loki-stack --version=2.3.1 loki/loki-stack
#
helm install --namespace loki-stack  prometheus  bitnami/kube-prometheus
```

To work with loki, prometheus and graphana open the browser to grafana:

```bash
open http://grafana.loki-stack.svc.cluster.local
```

## Resources  

* [Installation Loki](https://grafana.com/docs/loki/latest/installation/helm/)  
* [Basic loki installation - chart repo and descriptions](https://grafana.github.io/loki/charts/)  
