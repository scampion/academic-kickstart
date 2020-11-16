---
title: Kubernetes setup
image: "../images/k8s.png"
description: A short memo to setup a k8s cluster
date: "2019-05-02T19:25:30+02:00"
publishDate: "2019-01-01T19:05:00+02:00"
---

![k8s](../images/k8s.png)

In this memo, I detail how to setup a Kubernetes cluster with Træfik as Ingress controller.


## Setup the cluster

My K8s Ansible playbook will configure SELinux and install `kubectl` and `kubeadm` applications.

We deploy the server as explained in the [Kubernetes documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm).
After running `kubeadm` on the master, we follow the instructions and use the k8s user setup the environment.
As K8s user:


        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config


In order to join the network, you can use Ansible with:

        ansible k8s_slaves -a  "kubeadm join XX.XX.XX.XX:6443 --token 9hr3fh.5dt95193fixaeiev  \
          --discovery-token-ca-cert-hash sha256:32e....6cfa" \
          -u root  --become

## Pod network

    kubectl apply -f \
    "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

## Dashboard

The setup of users and generation of tokens is explained [here](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md).

### Creating a Service Account

First, we create a Service Account with name `admin-user` in namespace `kubernetes-dashboard`.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

### Creating a ClusterRoleBinding

In most cases after provisioning a cluster using `kops`, `kubeadm` or any other popular tool, the `ClusterRole` `cluster-admin` already exists in the cluster. We can use it and create only `ClusterRoleBinding` for our `ServiceAccount`.
If it does not exist, then you need to create this role first and grant required privileges manually.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```


### Getting a Bearer Token

We now need to find the token we can use to log in. Execute the following command:

For Bash:

```bash
kubectl -n kubernetes-dashboard describe secret \
$(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```


### Deploy the Dashboard

The following file is not mandatory, albeit useful:

        kubectl apply -f \
        https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended.yaml

Then, start a proxy:

        kubectl proxy



Setup a SSH Tunnel from your workstation like that:

    ssh -L 8888:127.0.0.1:8001 k8s@XX.XX.XX.XX kubectl proxy


## Ingress & Load balancing with Traefik
![](../images/traefik.logo.png)

The Traefik configuration files are available in the Traefik directory on the dedicated [git repository K8s](https://gitlab.com/ssapilot/k8s/-/tree/master/traefik)

Next, deploy the following steps:

1. Enabling role-based access control (RBAC)
    - Add a Service account: `traefik-service-acc.yaml`
    - Create a Cluster Role: `traefik-cr.yaml`
    - Create a Cluster Role Binding: `traefik-crb.yaml`
2. Deploy Traefik to a Cluster
    - Deploy `traefik-deployment.yaml`
3. Create NodePorts for External Access `traefik-svc.yaml` and get ports `kubectl describe svc traefik-ingress-service --namespace=kube-system`
4. Accessing Traefik
    To access the Traefik Web UI in the browser, you can use the “admin” NodePort 30729 (please note that your NodePort value might differ). Since we have not added any frontends, the UI will be empty at the moment.
    We will also get a 404 response for Ingress endpoint, because we have not yet given Traefik any configuration.
    `curl $(master ip):30565`

5. Add Ingress to the Cluster `traefik-webui-svc.yaml` and get the port `kubectl describe svc traefik-web-ui --namespace=kube-system`
    Next, we need to create an Ingress resource pointing to the Traefik Web UI backend with `traefik-ingress.yaml`

!!! warning
    To make the Traefik Web UI accessible in the browser via the traefik-ui.k8s, we need to add a new entry in our /etc/hosts file.
    `echo "$(master ip) traefik-ui.k8s" | sudo tee -a /etc/hosts`

    You should now be able to visit `http://traefik-ui.k8s:<AdminNodePort>` in the browser and view the Traefik web UI. Do not forget to append the “admin” NodePort to the host address.


### Useful links

- [https://medium.com/kubernetes-tutorials/deploying-traefik-as-ingress-controller-for-your-kubernetes-cluster-b03a0672ae0c](https://medium.com/kubernetes-tutorials/deploying-traefik-as-ingress-controller-for-your-kubernetes-cluster-b03a0672ae0c)
- [https://kubernetes.github.io/ingress-nginx/deploy/baremetal/](https://kubernetes.github.io/ingress-nginx/deploy/baremetal/)
- [https://www.linuxtechi.com/install-kubernetes-1-7-centos7-rhel7/](https://www.linuxtechi.com/install-kubernetes-1-7-centos7-rhel7/)
- [https://kubernetes.io/fr/docs/concepts/services-networking/ingress/](https://kubernetes.io/fr/docs/concepts/services-networking/ingress/)
- [https://www.net7.be/blog/article/kubernetes_ingress_bare_metal_load_balancing.html](https://www.net7.be/blog/article/kubernetes_ingress_bare_metal_load_balancing.html)
- [https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)
