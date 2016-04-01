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

What this role not (yet) includes:
- configuration of jenkins (e.g. users, security, executors, system configuration...)
- customization
- automated job generation

Supported systems
- RedHat based systems (RHEL, CentOS)

# Requirements
- Ansible needs to be installed on your machine
- Java 7+ needs to be available on the target machine
- The role is designed that a remote user must be used who is allowed to become root (the example in usage section)

# Variables
| Name | Description | Default |
|:-----|:------------|:--------|
| jenkins_master_package_names | List of packages to ensure | ? |
| jenkins_master_package_state | Package state | installed |
| jenkins_master_service_names | List of service name | ? |
| jenkins_master_service_state | Service state | running |
| jenkins_master_service_enabled | Service enabled | true |
| jenkins_master_home_dir | default jenkins installation dir | /var/lib/jenkins |
| jenkins_master_cli_jar_location | location of the jenkins-client-cli.jar | /var/cache/jenkins/war/WEB-INF/jenkins-cli.jar |
| jenkins_master_url | the url on which the freshly installed jenkins is reachable | http://localhost:8080 |
| jenkins_master_plugins | list of plugins that will be installed on the first start | < list > |
| jenkins_master_repo_url | the official url to the jenkins repo list | ? |
| jenkins_master_key_url | the official key for jenkins repo | ? |
| jenkins_master_repo_folder | location where to put the repo file| ? |

# Tags
- jenkins_master_install: ensure that all packages in __jenkins_master_package_names__ are in state __jenkins_master_package_state__
- jenkins_master_config: ensure configuration
- jenkins_master_service: ensure that all services in __jenkins_master_service_names__ are in state __jenkins_master_service_state__
- jenkins_master_plugins: ensure that all plugins in __jenkins_master_plugins__ are installed

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
