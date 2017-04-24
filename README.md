ansible-jenkins-master
=========
- [Introduction](#introduction)
- [Requirements](#requirements)
- [Variables](#variables)
- [Tags](#tags)
- [Usage](#usage)
- [Author](#author)

# Introduction
This is an ansible role to do a basic installation of jenkins. This includes:
- install jenkins
- setup the service on the system
- install plugins
- providing a backup with git
- basic security setup with ldap
- matrix based auth configuration

What this role not (yet) includes:
- most configuration areas of jenkins (e.g. users)
- customization
- automated job generation
- ssl setup

Supported systems
- RedHat based systems (RHEL, CentOS)
- Ubuntu (Not yet fully working -> security is always on)

# Requirements
- Ansible > 2.2 needs to be installed on your machine
- Java 7+ needs to be available on the target machine
- The role is designed that a remote user must be used who is allowed to become root (the example in usage section)

# Variables
This is a list of the most needed variables. Please look at **defaults/main.yml** and **vars/RedHat.yml** to see a list of all variables available.

__GENERAL:__

| Name | Description | Default |
|:-----|:------------|:--------|
| jenkins_master_service_state | Service state | running |
| jenkins_master_service_enabled | Service enabled | true |
| jenkins_master_host | the host on which jenkins is reachable | 127.0.0.1 |
| jenkins_master_port | the port on which jenkins is reachable | 8080 |
| jenkins_master_jnlp_port | the jnpl port on which jenkins is reachable. 0 means open for all. | 0 |
| jenkins_master_numberof_executor | the number of executor slots on the master | 0 |
| jenkins_master_labels | labels of the master node | noservice |
| jenkins_master_mode | the mode on how jobs are allocated to the master | NORMAL |

__Files and Folders__

| Name | Description | Default |
|:-----|:------------|:--------|
| jenkins_master_copy_files | wrapper of the ansible copy module to copy local files to the master | '' |
| jenkins_master_ensure_files | wrapper of the ansible file module to ensure files on the master | '' |

NOTE: This variables allow you to copy further config files and ensuring folders (e.g. ensuring .ssh folder and keys on the master)

```bash
# Example of ensuring ssh file / folder
jenkins_master_copy_files:
  - src:  ssh/config
    dest: '{{ jenkins_master_home_dir }}/.ssh/config'
    mode: '0644'
jenkins_master_ensure_files:
  - path: '{{ jenkins_master_home_dir }}/.ssh'
    state: directory
    mode: '0700'
```

__Authentication:__

| Name | Description | Default |
|:-----|:------------|:--------|
| jenkins_master_ad_enabled | specifies if the ad plugin should be used | false |
| jenkins_master_ad_domain | the active directory domain value | ' ' |
| jenkins_master_ad_site | the active directory site value | ' ' |
| jenkins_master_ad_server | the active-directory server | ' ' |
| jenkins_master_ad_bind_user | the active-directory user to use | ' ' |
| jenkins_master_ad_bind_password | the active-directory password for the user | ' ' |
| jenkins_master_matrix_auth_enabled | defines if matrix auth based rules should be used | true |
| jenkins_master_matrix_auth_strategies | list of dicts of the rules to use | <list> |

NOTE: The list of dicts has following form for the matrix auth based strategy:
```bash
jenkins_master_matrix_auth_strategies:
  - name: Jenkins.ADMINISTER
    value: anonymous
    # - name: Jenkins.ADMINISTER
    #   value: authenticated
    # - name: Jenkins.READ
    #   value: anonymous
    # - name: Item.DISCOVER
    #   value: anonymous
    # - name: Item.READ
    #   value: anonymous
    # - name: Item.WORKSPACE
    #   value: anonymous
    # - name: View.READ
    #   value: anonymous
```

__Plugins:__

| Name | Description | Default |
|:-----|:------------|:--------|
| jenkins_master_plugins | list of plugins that will be installed on the first start | < list > |

NOTE: This parameter requires a list of dicts with following content:
```bash
jenkins_master_plugins:
  - name: active-directory #(required)
    version: 1.49 #(optional)
    pinned: true / false #(optional)
    enabled: true / false #(optional)
  - name: another-plugin
  - name: yet-another-one

```

__Backup:__

| Name | Description | Default |
|:-----|:------------|:--------|
| jenkins_master_git_install_backup_job | place a job in jenkins that pushes all config changes to the configured git repo | false |
| jenkins_master_git_repo | The git repo to get the initial config from and save the changes to | ' ' |

NOTE: if you use the git backup, please ensure that you can reach and clone the git repo from the master without issues. Otherwise it can happen that the used git ansible module gets stuck in the process.

# Tags
- jenkins_master_install
- jenkins_master_config
- jenkins_master_service
- jenkins_master_plugins
- jenkins_master_gitbackup

# Usage

Example playbook

```yaml
- hosts: 127.0.0.1
  remote_user: xyz
  become: true
  become_method: sudo
  become_user: root

  roles:
  - role: jenkins-master
```

# Author
[Bjoern Jessen-Noak](djsm@gmx.de)
