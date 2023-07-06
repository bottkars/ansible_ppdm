# PPDM Roles for Ansible


the Roles utilize the rest API by using the URI Method

you can find the Roles in the Roles Directory
in the main directory, you can find several Playbooks for example workflows
the vars/main.yaml essentially represents an interpolation from environment variables to ansible vars




## calling the playbooks directly with an external vars file.
```bash
ansible-playbook ansible_dps/ppdm/2.3-playbook_configure_cloud_dr_account.yml --extra-vars "@ppdm-prod/vars.yaml"
```

## Documentation
the modules have been forked from a larger repo i maintain personally, so i start here as a Version 1 and Documentation if not inline is Pending :-(

However, usecases are described from [terraforming DPS](https://github.com/bottkars/terraforming-dps)

## Examples:


Also, most of the Variables may be looked up from ENV Vars, supporting the output from [terraforming DPS](https://github.com/bottkars/terraforming-dps)

```bash
# Refresh you Environment Variables if Multi Step !
eval "$(terraform output --json | jq -r 'with_entries(select(.key|test("^PP+"))) | keys[] as $key | "export \($key)=\"\(.[$key].value)\""')"
export PPDM_INITIAL_PASSWORD=Change_Me12345_
export PPDM_NTP_SERVERS='["169.254.169.254"]'
export PPDM_SETUP_PASSWORD=admin          # default password on the Cloud PPDM rest API
export PPDM_TIMEZONE="Europe/Berlin"
export PPDM_POLICY=PPDM_GOLD


```
Set the initial Configuration:    
```bash

ansible-playbook ~/workspace/ansible_ppdm/1.0-playbook_configure_ppdm.yml
```
verify the config:

```bash
ansible-playbook ~/workspace/ansible_ppdm/1.1-playbook_get_ppdm_config.yml
```
we add the DataDomain:  

```bash
ansible-playbook ~/workspace/ansible_ppdm/2.0-playbook_set_ddve.yml 
```
we can get the sdr config after Data Domain Boost auto-configuration for primary source  from PPDM

```bash
ansible-playbook ~/workspace/ansible_ppdm/3.0-playbook_get_sdr.yml
```
and see the dr jobs status
```bash
ansible-playbook ~/workspace/ansible_ppdm/31.1-playbook_get_activities.yml --extra-vars "filter='category eq \"DISASTER_RECOVERY\"'"
```

create a kubernetes policy and rule ...

```bash
ansible-playbook ~/workspace/ansible_ppdm/playbook_add_k8s_policy_and_rule.yml 
```

## Some advanced K8S
```json
{"details":{"k8s":{"configurations":[{"type":"CONTROLLER_CONFIG","key":"k8s.ppdm.vspherecsi.use.fsagent","value":"true"},{"type":"CONTROLLER_CONFIG","key":"k8s.docker.registry","value":"harbor.pks.home.labbuildr.com"},{"type":"CONTROLLER_CONFIG","key":"ppdm.backup.concurrency","value":"2"},{"type":"POD_CONFIG","key":"VELERO","value":"c3BlYzoKICB0ZW1wbGF0ZToKICAgIG1ldGFkYXRhOgogICAgICBsYWJlbHM6CiAgICAgICAgYXBwLmt1YmVybmV0ZXMuaW8vbmFtZTogdmVsZXJvLXBwZG0KICAgIHNwZWM6CiAgICAgIGltYWdlUHVsbFNlY3JldHM6CiAgICAgICAtIG5hbWU6IHJlZ2NyZWQ="},{"type":"CONTROLLER_CONFIG","key":"k8s.image.pullsecrets","value":"regcred"}]}}}
```


```bash
ansible-playbook ../ansible_ppdm/playbook_rbac_add_k8s_to_ppdm_reg_cred.yml \
--extra-vars="ppdm_fqdn=ppdm-demo.home.labbuildr.com ppdm_new_password=Change_Me12345_ rbac_source=/Users/bottk/workspace/ocs_vsphere/rbac/" \
-e="dockerconfigjson=eyJhdXRocyI6eyJoYXJib3IucGtzLmhvbWUubGFiYnVpbGRyLmNvbSI6eyJ1c2VybmFtZSI6InJvYm90JHBwZG1fYWNjb3VudCIsInBhc3N3b3JkIjoiTnJVU2ZWMGVPVFFmVGN0RHVienhNblV1a3IwRDBXbnkiLCJhdXRoIjoiY205aWIzUWtjSEJrYlY5aFkyTnZkVzUwT2s1eVZWTm1WakJsVDFSUlpsUmpkRVIxWW5wNFRXNVZkV3R5TUVRd1YyNTUifX19" \
-e='{"details":{"k8s":{"configurations":[{"type":"CONTROLLER_CONFIG","key":"k8s.ppdm.vspherecsi.use.fsagent","value":"true"},{"type":"CONTROLLER_CONFIG","key":"k8s.docker.registry","value":"harbor.pks.home.labbuildr.com"},{"type":"CONTROLLER_CONFIG","key":"k8s.image.pullsecrets","value":"regcred"},{"type":"POD_CONFIG","key":"VELERO","value":"c3BlYzoKICB0ZW1wbGF0ZToKICAgIG1ldGFkYXRhOgogICAgICBsYWJlbHM6CiAgICAgICAgYXBwLmt1YmVybmV0ZXMuaW8vbmFtZTogdmVsZXJvLXBwZG0KICAgIHNwZWM6CiAgICAgIGltYWdlUHVsbFNlY3JldHM6CiAgICAgICAtIG5hbWU6IHJlZ2NyZWQ="}]}}}'
```