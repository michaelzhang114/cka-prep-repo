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
