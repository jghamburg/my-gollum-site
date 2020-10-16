# Setup for local k8s  

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
 