common
-----------

![License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat)&nbsp;&nbsp;&nbsp;&nbsp;[![Build Status](https://travis-ci.org/lindahu1/ansible-common-base.svg?branch=master)](https://travis-ci.org/lindahu1/ansible-common-base#)

![Platform](http://img.shields.io/badge/platform-centos-932279.svg?style=flat) ![Platform](http://img.shields.io/badge/platform-redhat-cc0000.svg?style=flat) ![Platform](http://img.shields.io/badge/platform-ubuntu-dd4814.svg?style=flat) ![Platform](http://img.shields.io/badge/platform-Amazon-3c6eb4.svg?style=flat) ![Platform](http://img.shields.io/badge/platform-opensuse-73ba25.svg?style=flat)

An Ansible role to install and configure some basic OS setting for an initial server.
- set timezone
- install/remove common package by apt or yum: you can configure what packages you need to install or remove.
  - install EPEL repository for EL
- install/remove pip and python packages:
  - if **install_pip** == True, this role will install pip and install/remove the python packages according your configuration.
- Basic configurations:
  - add **PROMPT_COMMAND** in bashrc for audit log when **bashrc_path** is configured.
  - set **root:** alias in /etc/aliases for sendmail when **rootalias** is configured.
  - add ntp update in cron if **ntp_cron_min** and **ntp_cron_hour** are configured.
- Confiure **sshd_config**: Base on your configuration.
  - set **ListenAddress** and remove comment when **enable_sshd_ListenAddress** is True.
  - configure **PasswordAuthentication**
  - configure **PermitEmptyPasswords**
  - configure **PermitRootLogin**


Requirements
------------

- [ansible](https://ansible.com) >= 2.3


Role Variables
--------------

- **timezone**: (Default: Asia/Taipei)
- **ntp_cron_min**: the minute to execute ntpdate. (Default: 2)
- **ntp_cron_hour**: the hour to execute ntpdate. (Default: '*/3')
- **common_pkgs**: a list of packages you want to install. (Default: vim, curl, wget)
- **rootalias**: the email setting for root. (Default: '')
- **sshd_service**: the name of sshd service. ex: Debian: ssh, RedHat: sshd.
- **bashrc_path**: the bashrc path. ex: Debian: /etc/bash.bashrc, RedHat: /etc/bashrc.
- **sshd_config_path**: the path of sshd_config. (Default: /etc/ssh/sshd_config)
- **install_pip**: flag to install pip. (Default: True)
  - if you want to run this role on **CentOS5**, MUST set `install_pip: False`.
  - This role does NOT support install pip and install/remove python packages on CentOS5.
- **source_dir**: the path of directory to store downloaded files. (Default: /tmp)
- **must_removed_pkgs**: a list of packages you want to remove. (Default: postfix, python-pip, python2-pip, python27-pip)
- **sshd_config**: a dictionary of sshd_config, Default value is as below.
  - **PasswordAuthentication**: 'yes'
  - **PermitEmptyPasswords**: 'no'
  - **PermitRootLogin**: 'no'
  - **ListenAddress**: '0.0.0.0'
- **pip_install_pkgs**: a list of python packages you want to install.
- **pip_remove_pkgs**: a list of python packages you want to remove.


Dependencies
------------

None


Example Playbook
----------------
```yaml
---
- hosts: localhost
  become: yes

  roles:
    - { role: lindahu1.common }
```


Example Variable Files
----------------------

* Default values: `default/main.yml`
```yaml
timezone: 'Asia/Taipei'
ntp_cron_min: 2
ntp_cron_hour: '*/3'
common_pkgs: []
rootalias: 'test@test.com'
sshd_service: sshd
bashrc_path: '/etc/bashrc'
sshd_config_path: '/etc/ssh/sshd_config'
install_pip: true
source_dir: '/tmp'
must_removed_pkgs:
  - postfix
  - python-pip
  - python2-pip
  - python27-pip

sshd_config:
  PasswordAuthentication: 'yes'
  PermitEmptyPasswords: 'no'
  PermitRootLogin: 'no'
  ListenAddress: '0.0.0.0'

pip_install_pkgs:
  - awscli
  - { name: awscli, state: latest }
  - { name: simplejson, version: 3.11.0 }

pip_remove_pkgs:
  - awscli
```

* Example OS variables setting: `vars/Debian.yml`
```yaml
---
sshd_service: ssh
bashrc_path: '/etc/bash.bashrc'
common_pkgs:
  - vim
  - curl
  - wget
  - python
  - python-setuptools
  - sysstat
  - sendmail
  - heirloom-mailx
  - telnet
```

Tags
----

- **pkgs**: install/remove packages by apt or yum.
- **pip**: install pip and install/remove python packages.
- **config**: 
  - set timezone
  - add ntp update in cron
  - set PROMPT_COMMAND for audit log
  - set root: alias for sendmail.
- **sshd**: configure /etc/ssh/sshd_config.


Reminders
---------
- You can move `lindahu1.common/vars/` sample yaml to `[you_playbook]/vars/`.
- If you set **install_pip** == True, you better add the following packages in **common_pkgs**.
  or you may get `Failed building wheel for [cryptography](https://cryptography.io/en/latest/installation/)` while installing some python packages. 
  - For Debian and Ubuntu
  ```yaml=
   - python
   - python-setuptools
   - python-dev
   - build-essential
   - libssl-dev
   - libffi-dev
  ```

  - For RHEL-derivatives
  ```yaml=
   - python
   - python-setuptools
   - python-dev
   - gcc
   - libffi-devel
   - openssl-devel
  ```

  - For Amazon
  ```yaml=
   - python27
   - python27-setuptools
   - python27-dev
   - gcc
   - libffi-devel
   - openssl-devel
  ```

  - For OpenSuse
  ```yaml=
   - python
   - python-setuptools
   - python-dev
   - gcc
   - libffi-devel-gcc5
   - openssl-devel
  ```

