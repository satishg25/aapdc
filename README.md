# Apache and Tomcat Ansible Sandbox

Sandbox implementation for four catalogue workflows:

- **UC19a** — Microsoft Windows Server 2022 standalone with Tomcat 9.x
- **UC19b** — Microsoft Windows Server 2022 two-node HA with Tomcat 9.x
- **UC22** — RHEL 8.x two-node HA with Apache httpd 2.x
- **UC23** — RHEL 8.x standalone with Apache httpd 2.x

The repository follows the orchestration model **Cloud Studio → AAP Template → use-case playbook → reusable roles → tasks**. Use-case playbooks remain thin. Existing VMware, BlueCat, Windows/RHEL baseline, agent, HA integration, and operational-onboarding roles are invoked through `shared_role_hooks`; replace the hook list with the real shared role names used by the platform repository.

## Safety and scope

- All inventory addresses are documentation-only examples.
- Secrets must be supplied with AAP credentials or Ansible Vault, never normal survey variables.
- UC19b provisions and configures both Tomcat nodes, but its VIP and failover or load-balancing mechanism must be connected to the approved shared platform implementation before `tomcat_ha_design_approved` is enabled.
- UC22 requires approved production fencing before use outside a sandbox.
- The supplied UC23 workflow contains four DB2 cluster activities that conflict with its standalone Apache label. This repository implements the intended Apache workload only when `uc23_scope_correction_approved: true`.

## Install collections

~~~bash
ansible-galaxy collection install -r requirements.yml
~~~

## Validate

~~~bash
ansible-playbook -i inventories/sandbox/hosts.yml site.yml --syntax-check
ansible-playbook -i inventories/sandbox/hosts.yml site.yml --list-tasks -e requested_use_case=19b
~~~

## Run

~~~bash
ansible-playbook -i inventories/sandbox/hosts.yml site.yml \
  -e requested_use_case=19a \
  --ask-vault-pass
~~~

In AAP, create one job template per use case and bind the appropriate inventory, machine credential, Vault credential, and approved survey variables.
