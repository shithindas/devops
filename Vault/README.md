# Vault Setup

### Installation

Refer the tutorial [kubernetes-minikube](https://learn.hashicorp.com/tutorials/vault/kubernetes-minikube) for installing vault in single node cluster

**Note:** Consul creates persistent volume claim using storage class. This was not working as IAM role was not attached to the server. Hence, as a work around, I have created that persistent volume claim manually. Manifest for this is added in `consul-pv.yaml`

### Configure Database Secrets Engine

You can login into vault container as below and login using `vault login`

```
kubectl exec --stdin=true --tty=true vault-0 -- /bin/sh
``` 

1. Enable the database secrets engine 
```
vault secrets enable database
```

2. Configure Vault with the MySQL plugin and connection information:

Note: Used `mysql-legacy-database-plugin` plugin 

```
vault write database/config/my-mysql-database \
    plugin_name=mysql-legacy-database-plugin \
    connection_url="root:<root-password>@tcp(<mysql-service-name>:3306)/" \
    allowed_roles="pipeline"
```
3. Configure a role that maps a name in Vault to an SQL statement to execute to create the database credential:

```
vault write database/roles/pipeline \
    db_name=my-mysql-database \
    creation_statements="GRANT ALL ON <DB_NAME>.* TO '{{name}}'@'%' IDENTIFIED BY '{{password}}';" \
    default_ttl="1h" \
    max_ttl="24h"
```

4. We can generate a new credential by reading from the /creds endpoint with the name of the role:

```
vault read database/creds/pipeline
```

5. Creating policy to allow access to secrets

```
 vault policy write node-web - <<EOF
path "database/creds/pipeline" {
  capabilities = ["read"]
}
EOF
```

6. Assigning Vault policy to Kubernetes Service Accounts 

```
vault write auth/kubernetes/role/node-web \
    bound_service_account_names=node-web \
    bound_service_account_namespaces=default \
    policies=node-web \
    ttl=1h
```

7. Configuring Kubernetes Authentication in Vault

```
vault auth enable kubernetes
```
```
vault write auth/kubernetes/config \
    token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
    kubernetes_host=https://${KUBERNETES_PORT_443_TCP_ADDR}:443 \
    kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt
```
#### Reference

- https://www.vaultproject.io/docs/secrets/databases/mysql-maria
- https://www.hashicorp.com/blog/dynamic-database-credentials-with-vault-and-kubernetes
- https://www.vaultproject.io/docs/platform/k8s/injector/examples


