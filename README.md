# Kubernetes cluster with Ansible and kubeadm

---

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
cp inventory/example-hosts.yml inventory/hosts.yml
```

Now edit the inventory file and replace the example values with your own.

### Run the playbook

```shell
ansible-playbook playbook.yml -b
```