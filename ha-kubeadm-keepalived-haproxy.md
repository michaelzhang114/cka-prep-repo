# Set up Highly Available Kubernetes

## Multipass environment
| Node type     | VM name | RAM (GB) | CPU | Storage |
|---------------|---------|----------|-----|---------|
| Load balancer | lb1     | 1        | 2   | 5       |
| Load balancer | lb1     | 1        | 2   | 5       |
| Control plane | cp1     | 2        | 2   | 20      |
| Control plane | cp2     | 2        | 2   | 20      |
| Control plane | cp3     | 2        | 2   | 20      |

```
multipass launch -n lb1 -c 2 -m 1G -d 5G
multipass launch -n lb2 -c 2 -m 1G -d 5G
multipass launch -n cp1 -c 2 -m 2G -d 20G
multipass launch -n cp2 -c 2 -m 2G -d 20G
multipass launch -n cp3 -c 2 -m 2G -d 20G
```

![image](https://user-images.githubusercontent.com/31636398/163896780-cb54a8e2-5e37-4419-98f8-0d6c9e89c06a.png)
