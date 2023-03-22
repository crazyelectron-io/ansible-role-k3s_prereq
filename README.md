# role-k3s_prere

Ansible role to deploy the prerequisites for the K3s master and worker nodes

> Be aware that this role is opinionated and fits my preferences and way of working.
> It may or may not be suitable for your needs.

## Role variables

### Mandatory variables

None.

### Important optional parameters (with defaults)

`system_timezone: 'Europe/Amsterdam'` - Set the desired timezone (defaults to Europ/Amsterdam)

These variables can be specified in the `hosts.yaml` inventory file or in the `vars`section of an Ansible playbook, like this:

```yaml
- hosts: all
  become: true
  gather_facts: true
  vars:
    system_timezone: Europe/Berlin
  roles:
    - k3s-master # k3s-prereq is called from k3s-master role
  ...
```

Or in an inventory file:

```yaml
---
all:
  vars:
    system_timezone: Europe/Berlin
    ...
  children:
  ...

## Usage of this role

To use this role directly, include the following section in a `requirements.yml` file in the local `roles` directory:

```yaml
# Include the 'debian-base` role from GitHub
- src: git@github.com:crazyelectron-io/role-k3s_prereq.git
  scm: git
  version: main
  name: reboot
```

> Only include the 'top' roles, dependencies - when listed in `meta/main.yml` of the imported role - will be downloaded automatically.

To retrieve roles like this in your project, run `ansible-galaxy install -r roles/requirements.yml`.
Because these roles will not be updated locally when the repository is changed, to refresh an already retrieved role use `ansible-galaxy install -f -r roles/requirements.yml`

## Dependencies

None.

## Suggested project structure

```shell
├── inventories
│   ├── dev
│   │   ├── group_vars/
│   │   └── hosts.yml
│   └── prod
│       ├── group_vars/
│       └── hosts.yml
├── group_vars/
├── host_vars/
├── files/
├── templates/
├── roles
│   ├── local
│   │   ├── local_role1/
│   │   └── local_role2/
│   ├── requirements.yml
│   ├── .gitignore
├── ansible.cfg
├── README.md
├── some_playbook.yml
├── other_playbook.yml
```

Create a `roles/.gitignore` file to exclude the downloaded roles:

```ini
#Ignore everything in roles dir...
/*
# ... but current file...
!.gitignore
# ... external role requirement file
!requirements.yml
# ... and configured custom/local roles
!local*/
```

Add `roles_path = roles` to `ansible.cfg` to make sure that roles are searched and downloaded in our local folder.

### Workflow to deploy from the project

1. Clone the project repository
2. Download the external roles: `ansible-galaxy install -r roles/requirements`
3. Launch your playbook: `ansible-playbook -i inventories/dev some_playbook.yml -u ANSIBLE_USER`
