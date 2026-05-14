# Getting Started With Kubernetes and Container Orchestration Notes

The purpose of this example is to provide instructions for running the Dockercoins sample app using K3d.

## Software Requirements

- OrbStack 2.0.5 or newer

- K3d 5.8.3 or newer

- Kubectl 1.36 or newer

- Kubernetes 1.36.0 or newer

## Tutorial

1.  create cluster

    ```zsh
    k3d cluster create --config k3d-config.yaml
    ```

    Note: Servers represent the control plan nodes and agents represents the worker nodes. For additional information, please read [here](https://rancher.com/docs/k3s/latest/en/architecture).

2.  install K8s Gateway API resources

    ```zsh
    kubectl kustomize "https://github.com/nginx/nginx-gateway-fabric/config/crd/gateway-api/standard?ref=v2.5.1" | kubectl apply -f -
    ```

3.  deploy Nginx Gateway Fabric

    ```zsh
    helm install ngf oci://ghcr.io/nginx/charts/nginx-gateway-fabric --create-namespace -n nginx-gateway
    ```

4.  create K8s gateway resource

    ```zsh
    kubectl apply -f gateway.yaml
    ```

5.  create K8s HTTPRoute resource

    ```zsh
    kubectl apply -f httproute.yaml
    ```

6.  create Redis service and deployment

    ```zsh
    kubectl apply -f redis.yaml
    ```

7.  create Hasher service and deployment

    ```zsh
    kubectl apply -f hasher.yaml
    ```

8.  create Rng service and deployment

    ```zsh
    kubectl apply -f rng.yaml
    ```

9.  create WebUI service and deployment

    ```zsh
    kubectl apply -f webui.yaml
    ```

10. create Worker deployment

    ```zsh
    kubectl apply -f worker.yaml
    ```

11. navigate to WebUI service in the browser

    ```zsh
    open http://localhost
    ```

12. scaling the Worker service

    ```zsh
    kubectl scale deploy/worker --replicas=10
    ```

13. teardown cluster

    ```zsh
    k3d cluster delete dockercoins
    ```

## References

- https://k3d.io

- https://training.play-with-kubernetes.com/kubernetes-workshop

- https://gateway-api.sigs.k8s.io

- https://docs.nginx.com/nginx-gateway-fabric/get-started

## Support

Bug reports and feature requests can be filed here:

- [File Bug Reports and Features](https://github.com/conradwt/dockercoins-using-k3d/issues)

## License

Dockercoins Using K3d is released under the [MIT license](./LICENSE.md).

## Copyright

Copyright &copy; 2020 - 2026 Conrad Taylor. All rights reserved.
