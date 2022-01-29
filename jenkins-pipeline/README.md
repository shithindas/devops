# Kubernetes Single Node Cluster Pipeline

This pipeline script calls Ansible playbook `k8-single-node.yml` and configures the Kubernetes cluster.  Add the IP address of the desired server in `hosts.yml` file and push your changes to `main` branch. 

### Resetting the cluster

If you need to reset the Kubeadm configuration, enable the `RESET_CLUSTER` option while running the job. This will call the ansible playbook with `reset` tag.