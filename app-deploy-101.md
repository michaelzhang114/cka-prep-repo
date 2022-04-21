# Basics of app deployment in Kubernetes

Create a deployment with the nginx image
```
kubectl create deployment nginx --image=nginx
```

Watch the RS and pod getting created
```
kubectl get events
```

Scale
```
kubectl scale deployment/nginx --replicas=5
```

Expose a ClusterIP service
```
kubectl expose deployment nginx --type=ClusterIP --port=80 --target-port=8080
```

Understand that EPs are created
```
kubectl get ep
```
