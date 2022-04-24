# Basics of app deployment in Kubernetes

## Deployment

Create a deployment with the nginx image
```
kubectl create deployment nginx --image=nginx
```

Notice that RS and pod got created when you create a deployment.
```
kubectl get events
```

See everything
```
kubectl get all
```

![image](https://user-images.githubusercontent.com/31636398/164512364-b48df1fa-87f3-4c73-ae11-f855ec62063b.png)


Scale
```
kubectl scale deployment/nginx --replicas=5
```

![image](https://user-images.githubusercontent.com/31636398/164512489-c84ccc0f-d149-42fe-83c6-83f455d92af1.png)



## Service

Take note of your pod IP addresses. Adding `-o wide` lets you quickly see the IPs of pods and which nodes they're currently running on.

![image](https://user-images.githubusercontent.com/31636398/164512566-c9677662-8841-4ab1-8c28-93d05a92a73f.png)

Expose a ClusterIP service. Note that port 80 is the port that nginx containers serve by default, which is why we're exposing this.
```
kubectl expose deployment nginx --type=ClusterIP --port=80
```

Understand that EPs are created with services
```
kubectl get ep
```

![image](https://user-images.githubusercontent.com/31636398/164512646-333c7417-c172-4eaa-8b36-ec409c295fc1.png)

Relationship between svc, ep: https://stackoverflow.com/questions/52857825/what-is-an-endpoint-in-kubernetes#:~:text=An%20endpoint%20is%20an%20object%20that%20gets%20IP,order%20to%20be%20able%20to%20communicate%20with%20them.

With a ClusterIP service, you can access this within the cluster (pods in the cluster, nodes part of the cluster)

Accessing the service from a pod
```
kubectl get svc
kubectl get po
kubectl exec -it nginx-6799fc88d8-57mm8 -- curl 10.98.159.255:80
```

Accessing the service from a node
```
# first get into your node, run
curl 10.98.159.255:80
```

Accessing the service from outside the cluster (aka another machine) will not get a curl response.

