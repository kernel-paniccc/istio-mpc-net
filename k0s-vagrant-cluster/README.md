# k0s Vagrant cluster

Two Ubuntu 22.04 VMs: a k0s controller (also schedulable) and one worker. It is
adapted from the supplied k3s Vagrant template, but uses k0s's embedded
containerd instead of installing Docker.

## Start

```sh
cd k0s-vagrant-cluster
cp env/local.env.example env/local.env
source env/local.env
vagrant up --provider=libvirt
```

All local state is under `env/runtime/`: Vagrant metadata, the Vagrant home and
the cluster kubeconfig. `env/local.env` is ignored by Git and never modifies
`~/.kube/config`.

```sh
kubectl get nodes
kubens demo
```

These commands address the master API at `192.168.56.10:6443`, so the host has
the same cluster-administration access as the control-plane node. For a shell
session, keep it explicit and isolated:

```sh
kubectl cluster-info
kubens demo
```

`kubens` switches the namespace through the project-local kubeconfig, keeping
your other cluster contexts unchanged. If `demo` does not exist yet, create it:

```sh
kubectl create namespace demo
kubens demo
```

Re-run provisioning after changing Ansible files:

```sh
vagrant provision
```

Destroy only these VMs when finished:

```sh
vagrant destroy -f
```
