# Getting Started With Kubernetes and Container Orchestration Notes

The purpose of this example is to provide instructions for running the Dockercoins sample app using K3d.

## Software Requirements

- Docker Desktop For Mac 4.22.0 or newer

- K3d 5.7.3 or newer

- Kubectl 1.30 or newer

- Kubernetes 1.30.3 or newer

## Create Cluster

```zsh
k3d cluster create dockercoins --servers 3 --agents 3 -p "8082:30080@agent:0" --servers 3 --wait
```

or

```zsh
k3d cluster create --config k3d.yaml
```

Note: Servers represent the control plan nodes and agents represents the worker nodes. For additional information, please read [here](https://rancher.com/docs/k3s/latest/en/architecture).

## Create Necessary Environment Variables

```zsh
export REGISTRY=dockercoins
export TAG=v0.1
```

## Create Redis Deployment

```zsh
kubectl create deployment redis --image=redis
```

## Create Other Deployments

```zsh
for SERVICE in hasher rng webui worker; do
  kubectl create deployment $SERVICE --image=$REGISTRY/$SERVICE:$TAG
done
```

## Create Redis, Rng, And Hasher Services Using ClusterIP Type

```zsh
kubectl expose deployment redis --port 6379
kubectl expose deployment rng --port 80
kubectl expose deployment hasher --port 80
```

## Create WebUI Service Using NodePort Type

```zsh
kubectl create service nodeport webui --node-port=30080 --tcp=8082:80
```

## Navigate To WebUI Service In The Browser

```zsh
open http://localhost:8082
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

Copyright &copy; 2020 - 2024 Conrad Taylor. All rights reserved.
