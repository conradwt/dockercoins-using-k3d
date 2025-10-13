# Getting Started With Kubernetes and Container Orchestration Notes

The purpose of this example is to provide instructions for running the Dockercoins sample app using K3d.

## Software Requirements

- Docker Desktop For Mac 4.22.0 or newer

- K3d 5.8.3 or newer

- Kubectl 1.34 or newer

- Kubernetes 1.34.1 or newer

## Create Cluster

```zsh
k3d cluster create --config k3d-config.yaml
```

Note: Servers represent the control plan nodes and agents represents the worker nodes. For additional information, please read [here](https://rancher.com/docs/k3s/latest/en/architecture).

## Create Necessary Environment Variables

```zsh
export REGISTRY=dockercoins
export TAG=v0.1
```

## Create Redis Service and Deployment

redis.yaml:
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: redis
spec:
  type: ClusterIP
  selector:
    app: redis
  ports:
    - protocol: TCP
      # port of the service
      port: 6379
      # port of the pod
      targetPort: 6379
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
        - name: redis
          image: redis:8.2.1
          # resources:
          #   limits:
          #     memory: '128Mi'
          #     cpu: '500m'
          ports:
            - containerPort: 6379
```

```zsh
kubectl apply -f redis.yaml
```

## Create Hasher Service and Deployment

hasher.yaml:
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: hasher
spec:
  type: ClusterIP
  selector:
    app: hasher
  ports:
    - protocol: TCP
      # port of the service
      port: 80
      # port of the pod
      targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hasher
spec:
  selector:
    matchLabels:
      app: hasher
  template:
    metadata:
      labels:
        app: hasher
    spec:
      containers:
        - name: hasher
          image: dockercoins/hasher:v0.1
          # resources:
          #   limits:
          #     memory: '128Mi'
          #     cpu: '500m'
          ports:
            - containerPort: 80
```

```zsh
kubectl apply -f hasher.yaml
```

## Create Rng Service and Deployment

rng.yaml:
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: rng
spec:
  type: ClusterIP
  selector:
    app: rng
  ports:
    - protocol: TCP
      # port of the service
      port: 80
      # port of the pod
      targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rng
spec:
  selector:
    matchLabels:
      app: rng
  template:
    metadata:
      labels:
        app: rng
    spec:
      containers:
        - name: rng
          image: dockercoins/rng:v0.1
          # resources:
          #   limits:
          #     memory: '128Mi'
          #     cpu: '500m'
          ports:
            - containerPort: 80
```

```zsh
kubectl apply -f rng.yaml
```

## Create WebUI Service and Deployment

webui.yaml:
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: webui
spec:
  type: LoadBalancer
  selector:
    app: webui
  ports:
    - protocol: TCP
      # port of the service
      port: 8082
      # port of the pod
      targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webui
spec:
  selector:
    matchLabels:
      app: webui
  template:
    metadata:
      labels:
        app: webui
    spec:
      containers:
        - name: webui
          image: dockercoins/webui:v0.1
          # resources:
          #   limits:
          #     memory: '128Mi'
          #     cpu: '500m'
          ports:
            - containerPort: 80
```

```zsh
kubectl apply -f webui.yaml
```

## Create Worker Deployment

worker.yaml:
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: worker
spec:
  selector:
    matchLabels:
      app: worker
  template:
    metadata:
      labels:
        app: worker
    spec:
      containers:
        - name: worker
          image: dockercoins/worker:v0.1
          # resources:
          #   limits:
          #     memory: '128Mi'
          #     cpu: '500m'
          # ports:
          #   - containerPort: <Port>
```

```zsh
kubectl apply -f worker.yaml
```

## Navigate To WebUI Service In The Browser

```zsh
open http://localhost
```

## Scaling The Worker Service

```zsh
kubectl scale deploy/worker --replicas=10
```

## Teardown Cluster

```zsh
k3d cluster delete dockercoins
```

## References

- https://k3d.io

- https://training.play-with-kubernetes.com/kubernetes-workshop

## Support

Bug reports and feature requests can be filed here:

- [File Bug Reports and Features](https://github.com/conradwt/dockercoins-using-k3d/issues)

## License

Dockercoins Using K3d is released under the [MIT license](./LICENSE.md).

## Copyright

Copyright &copy; 2020 - 2025 Conrad Taylor. All rights reserved.
