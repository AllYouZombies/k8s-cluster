# Kubernetes cluster with Ansible and kubeadm

---

## Prerequisites

- Get from 2 to 5 or more nodes with Ubuntu installed.
- Get the SSH key with root access to the nodes.

### Clone the repository and install dependencies

```shell
git clone https://gitlab.anttek.io/devops/k8s-cluster-kubeadm.git
cd k8s-cluster-kubeadm
python3 -m venv venv
```

### Activate the virtual environment

On Linux and macOS:
```shell
. venv/bin/activate
```

On Windows:
```shell
.\venv\Scripts\activate
```

### Install dependencies

```shell
pip install ansible
```

### Create the inventory file

```shell
cp -R example_cluster cluster
```

Now edit the inventory file and replace the example values with your own.

## Prepare nodes

Ansible will only installs and configures all the necessary packages on the nodes.

Packages installed:
- containerd
- runc
- CNI plugins
- kubeadm
- kubelet
- kubectl

You will still need to initialize the cluster and join the nodes to it manually.

### Run the playbook

```shell
ansible-playbook playbook.yml -b
```

---

## Initialize the cluster

> Please, refer to the [official documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#initializing-your-control-plane-node) to get more information about the `kubeadm init` command.

### Notes

#### API server advertise address

In some cases, you may need to add the `--apiserver-advertise-address` and `--apiserver-cert-extra-sans` flags to the `kubeadm init` command.  
It depends on your network configuration.  
In my case, without these flags, the API server was not accessible from pods.  

Example:
```shell
kubeadm init --apiserver-advertise-address=0.0.0.0 --apiserver-cert-extra-sans=<CURRENT_NODE_EXTERNAL_IP>,<CURRENT_NODE_INTERNAL_IP>,api-server.<CLUSTER_DOMAIN>
```
  
After the cluster is initialized, refer to the 
[next section of the official documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#more-information).  
You may need to join other nodes to the cluster and download the kubeconfig file.

---

> From there you're free to do whatever you want with the cluster.  
> However, you still need to install a pod network add-on to make the cluster usable.  
> All the steps below are **just my personal preferences**.

---

## Install a pod network add-on

After some searching and comparisons, I decided to use [Cilium](https://cilium.io/) as the pod network add-on.
Installation is pretty straightforward.
I recommend you to follow the [official documentation](https://docs.cilium.io/en/stable/gettingstarted/k8s-install-default/).

> In my case, there was a network issue when Cilium tried to communicate with the Kubernetes API server.
> I had to add the `--set k8sServiceHost` and `--set k8sServicePort` flags to the `cilium install` command.  
> 
> `--k8sServiceHost` is the Kubernetes API server address.  
> It might be one of the addressess you specified in the `--apiserver-cert-extra-sans` flag.

---
