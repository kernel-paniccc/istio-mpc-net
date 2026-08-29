# k0s Vagrant cluster

This lab creates two Ubuntu 22.04 VMs with libvirt:

- `k0s-master`: k0s control plane, tainted `NoSchedule`;
- `k0s-worker`: k0s workload node.

Vagrant creates virtual machines only. Ansible applies the cluster roles in a
separate, explicit step; this prevents each VM from provisioning a multi-node
playbook independently.

## Deploy the base cluster

```sh
cd k0s-vagrant-cluster
vagrant up --no-provision --provider=libvirt
cd ansible
ansible-playbook site.yml
```

The playbook applies `common`, `k0s_controller`, and `k0s_worker` roles in that
order, then creates the `istio-mpc-net` namespace. It registers both VMs as
Kubernetes nodes.

## Host access

The playbook installs the `istio-mpc-net` context in the host's standard
`~/.kube/config`. The referenced CA, client certificate, and client key are
stored outside the repository in `~/.kube/istio-mpc-net/`; the private key has
mode `0600`.

After the playbook completes, use:

```sh
kubens istio-mpc-net
kubectl get nodes
```

## Lifecycle

```sh
vagrant halt
vagrant destroy -f
```
