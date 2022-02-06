```bash
minikube start --kubernetes-version=v1.23.1 --addons=ingress $(if [ $(uname) != "Linux" ]; then echo "--vm=true"; fi)
minikube tunnel
```

Deploy the product version 1.0 of our sample app app-a:  

```bash
kubectl apply -f app-a-deployment.yaml
```

Deploy the canary version 2.0 of our sample app app-a:  

```bash
kubectl apply -f app-a-deployment-canary.yaml
```

Let's check what we have provisioned:  

```bash
kubectl get deployments -o wide
kubectl get pods -o wide --show-labels
kubectl get service -o wide
kubectl get ingress -o wide
```

To route requests based on the weight we configured for our canary (50%), run the following command a few times. Roughly 50% of our request should go to the canary:

```bash
curl -i http://127.0.0.1 -H "Host: local.example.com"
```

To make sure the request is always routed to the canary, add the http header "X-Canary: always":

```bash
curl -i http://127.0.0.1 -H "Host: local.example.com" -H "X-Canary: always"
```

To make sure the request is never routed to the canary, add the http header "X-Canary: never":  

```bash
curl -i http://127.0.0.1 -H "Host: local.example.com" -H "X-Canary: never"
```

```bash
minikube stop
```
