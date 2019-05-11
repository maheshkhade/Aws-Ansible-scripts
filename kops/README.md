# Dependencies:
Local:
- ansible see requirements.txt for required version
- awscli properly configured, see requirements.txt for required version
- kops for exact version see the Dockerfile
- kubectl for exact version see the Dockerfile
- helm for exact version see the Dockerfile

Or build container and run with docker.


# Secret
Ansible-vault is used to encrypt secrets/credentials.
Ansible will look for the password in file: `./pass.txt` as configured in ansible.cfg file.
Password to unlock ansible-vault is stored in AWS parameters store key: `/k8s/all/k8s-configurations/ansible-vault-pass`
You can get that with this command:
`aws ssm get-parameter --name /k8s/all/k8s-configurations/ansible-vault-pass --with-decryption --query 'Parameter.Value' --output text`

To add new secret to the cluster:
- for shared secrets (stored in all clusters) add that to `shared_vars/kubectl_secrets.yaml`
- for environment/cluster secrets add that to `env_vars/<env>/kubect/secrets.yaml`

Generate encrypted variable with:
`echo -n "secret password" | ansible-vault encrypt_string --stdin-name 'password'`

Encrypt the file with:
`ansible-vault encrypt <file>`


# Quick start

## kops
Workflow:
1. edit kops variables in:
```
env_vars/{dev,rc,research}/kops/{cluster_spec, instance_groups}.yaml
```

2. Render kops cluster spec and instance group spec:
```
ansible-playbook -i inventories/<env> kops.yaml
```
The `env` are the folders inside inventories dir. Atm, we support *dev*, *rc*,
*research*. Those folders just set the variable `current_env` (in <env>/group_vars/all.yaml)
to point to correct environment. That way we can dynamically select the environtments
with cli.

3. new kops templates are rendered in:
```
rendered_templates/<env>/kops/{cluster_spec, instance_groups}.yaml
```
Then you can:
```
kops replace -f rendered_templates/<env>/kops/{cluster_spec, instance_groups}.yaml
```
3. commit the changes you have done in the chart values for the environment.

## Debug
There is a playbook dump_vars.yaml to print out all included variable for
current environment. Run below to see current environment variables:
```
ansible-playbook -i inventories/<env> dump_vars.yaml
```
**NOTE** Please check terraform/README.md and follow this.
