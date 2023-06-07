# PPDM Roles for Ansible


the Roles utilize the rest API by using the URI Method

you can find the Roles in the Roles Directory
in the main directory, you can find several Playbooks vor example workflows
the vars/main.yaml essentially represents an interpolation from environemnt variables to ansible vars


you  calling the playbooks directly with an external vars file.

## Documentation
the modules have been foked from a larger repo i maintain personally, so i start here as a Version 1 and Documentation if not inline is Pending :-(

However, usecases are descriped from [terraforming DPS](https://github.com/bottkars/terraforming-dps)

## Examples:

```bash
ansible-playbook ansible_dps/ppdm/2.3-playbook_configure_cloud_dr_account.yml --extra-vars "@ppdm-prod/vars.yaml"
```

Also, most of the Variables may be looked up from ENV Vars, supporting the output from [terraforming DPS](https://github.com/bottkars/terraforming-dps)


here are some Examples..

## export outputs from terraform into environment variables:
```bash
export DDVE_PUBLIC_FQDN=$(terraform output -raw ddve_private_ip)                
export DDVE_USERNAME=sysadmin
export DDVE_INITIAL_PASSWORD=$(terraform output -raw ddve_instance_id)
export DDVE_PASSWORD=Change_Me12345_
export PPDD_PASSPHRASE=Change_Me12345_!
export DDVE_PRIVATE_FQDN=$(terraform output -raw ddve_private_ip)
export ATOS_BUCKET=$(terraform output -raw atos_bucket)
export PPDD_TIMEZONE="Europe/Berlin"
```


set the Initial DataDomain Password
```bash
ansible-playbook ~/workspace/ansible_ppdd/1.0-Playbook-configure-initial-password.yml
```
![image](https://user-images.githubusercontent.com/8255007/232750620-df339f28-bdac-4db2-984f-a2df1d14b38e.png)
If you have a valid dd license, set the variable PPDD_LICENSE, example:
```bash
export PPDD_LICENSE=$(cat ~/workspace/internal.lic)
ansible-playbook ~/workspace/ansible_ppdd/3.0-Playbook-set-dd-license.yml
```

next, we set the passphrase, as export PPDD_Lit is required for ATOS
then, we will set the Timezone and the NTP to GCP NTP link local  Server
```bash
ansible-playbook ~/workspace/ansible_ppdd/2.1-Playbook-configure-ddpassphrase.yml
ansible-playbook ~/workspace/ansible_ppdd/2.1.1-Playbook-set-dd-timezone-and-ntp-gcp.yml
```

Albeit there is a *ansible-playbook ~/workspace/ansible_ppdd/2.2-Playbook-configure-dd-atos-aws.yml* , we cannot use it, as the RestAPI Call to create Active Tier on Object is not available now for GCP...
Therefore us the UI Wizard


