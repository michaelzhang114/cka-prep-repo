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

![image](https://user-images.githubusercontent.com/31636398/164511160-092a8dcc-cdb4-46ce-8635-ab4f041b66c7.png)

Scale
```
kubectl scale deployment/nginx --replicas=5
```

![image](https://user-images.githubusercontent.com/31636398/164511228-b13bf909-4e1b-409c-903c-ceb96e333dd7.png)


## Service

Take note of your pod IP addresses. Adding `-o wide` lets you quickly see the IPs of pods and which nodes they're currently running on.

![image](https://user-images.githubusercontent.com/31636398/164511667-f37adc5f-db23-417a-aaba-2dd501adb213.png)


Expose a ClusterIP service
```
kubectl expose deployment nginx --type=ClusterIP --port=80 --target-port=8080
```

Understand that EPs are created
```
kubectl get ep
```





- Different types of services
- YAMLs vs commands
