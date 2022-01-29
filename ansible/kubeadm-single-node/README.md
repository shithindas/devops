kubeadm-single-node
=========

This role is used to create Single node cluster using `kubeadm` utility. It performs the following: 
- installs docker, kubeadm, kubelet and other dependencies
- Install Flannel Network Addon 
- Untaint the master node for pod scheduling

Role Variables
--------------

We use `Flannel` as our pod networking addon. The Cidr used for pod networking is specified in `defaults/main.yml` file

Tags used in Roles
----------------

* reset: Running this role with this tag enabled will reset the kubeadm. 
* configure: After resetting the kubeadm, this tag can be used to configure it again.

Note: for configuring the cluster, run playbook with `--skip-tags reset` 