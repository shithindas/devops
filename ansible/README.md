kubernetes-single-node
=========

This playbook calls the role `kubeadm-single-node` and configure single node kubernetes cluster. This playbook is compatible with Debian based servers. 

### Usage

**Provision Single node cluster**
```
ansible-playbook -i hosts.yml k8-single-node.yml --skip-tags reset
```

**Reset Kubeadm configuration**

Performs a best effort revert of changes made to the host by 'kubeadm init' command

```
ansible-playbook -i hosts.yml k8-single-node.yml --tags reset
```

**Reconfigure Kubeadm configuration after reset**

Run playbooks as follows
```
ansible-playbook -i hosts.yml k8-single-node.yml --tags configure
```