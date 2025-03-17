# A local deployment of ArgoCD for learning purposes

Run an ArgoCD instance on a local [k3d][] Kubernetes cluster on your laptop!


## Requirements

You'll need the following tools:
* [k3d][].
* The [kubectl][] and [kustomize][] command-line tools.
* [yq][].
* The [`argocd` command-line tool](https://argo-cd.readthedocs.io/en/stable/cli_installation/).

If you have [Nix][] installed, the included [Nix flake definition](./flake.nix)
brings all those bits in.

The local Kubernetes cluster will accept TCP connections on ports 80 and 443
of the loopback address 127.0.2.11. On macOS systems you need to bring that
address up with:

    $ sudo ifconfig lo0 alias 127.0.2.11 up

The effect of that command does not persist across reboots, and you'll have
to run that command every time you reboot your macOS device.

Under Linux the loopback interface already handles IPv4 connections to any
address in the 127.0.0.0/8 subnet, so you do not have to do anything.


## Bootstrapping the cluster

Run the `cluster/bootstrap` script:

    ./cluster/bootstrap

Activate the cluster's Kubeconfig:

    export KUBECONFIG=$(pwd)/cluster/kubeconfig.yaml

Verify that the Kubernetes cluster is up and running:

    $ kubectl get nodes
    NAME                          STATUS   ROLES                  AGE   VERSION
    k3d-argocd-sandbox-agent-0    Ready    <none>                 68s   v1.31.6+k3s1
    k3d-argocd-sandbox-agent-1    Ready    <none>                 68s   v1.31.6+k3s1
    k3d-argocd-sandbox-agent-2    Ready    <none>                 68s   v1.31.6+k3s1
    k3d-argocd-sandbox-agent-3    Ready    <none>                 68s   v1.31.6+k3s1
    k3d-argocd-sandbox-server-0   Ready    control-plane,master   70s   v1.31.6+k3s1

Install ArgoCD itself:

    ./argocd/bootstrap

Verify that all ArgoCD pods are running and ready:

    $ kubectl -n argocd get pods
    NAME                                                READY   STATUS    RESTARTS   AGE
    argocd-application-controller-0                     1/1     Running   0          43s
    argocd-applicationset-controller-86cff55c8b-zjd8w   1/1     Running   0          43s
    argocd-dex-server-7484d897c6-s8kf7                  1/1     Running   0          43s
    argocd-notifications-controller-7764f747c9-szf74    1/1     Running   0          43s
    argocd-redis-78474b46d7-hz4wb                       1/1     Running   0          43s
    argocd-repo-server-857c64f889-j88fl                 1/1     Running   0          43s
    argocd-server-575695cfc6-mb9kz                      1/1     Running   0          43s

At this point you should be able to access the ArgoCD UI at https://argocd.127.0.2.11.nip.io


## Destroying the cluster

Run the `cluster/teardown` script:

    ./cluster/teardown


[Nix]: https://nixos.org
[k3d]: https://k3d.io
[kubectl]: https://kubernetes.io/docs/tasks/tools/#kubectl
[kustomize]: https://kustomize.io/
[yq]: https://mikefarah.gitbook.io/yq
