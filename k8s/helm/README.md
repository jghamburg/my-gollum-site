# Localstack for local development  

## How to use in local K8s  

```bash
 helm upgrade --install localhost --namespace default  localstack
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
 