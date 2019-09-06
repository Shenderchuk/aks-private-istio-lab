# Excercise - istio

## Deploy and test sample workloads

### Steps

- Go to Cloud Shell and verify kubectl is pointed to this cluster
- Get External LoadBalancer IP address (Install Istio step in AKS.md)
- Deploy Istio Gateway

```bash
kubectl apply -f definitions/001-simple-http-gateway.yaml
```

- Deploy simple nginx and Istio Virtual Service

```bash
kubectl apply -f definitions/001-nginx-probe-80.yaml
```

- Go to http://IP/nginx-probe in browser

expected result:

```html
to see HTML page with the following content:
<h1>test_ok</h1>
```

### Expected result

- Istio Gateway deployed and in working condition
- Simple Nginx pod, service, virtualsevice deployed and in working condition
- There is access to the html page via browser

### Cleanup

Remove Gateway and nginx-probe resources:

```bash
kubectl delete -f denitions/001-nginx-probe-80.yaml
kubectl delete -f denitions/001-simple-http-gateway.yaml
```
