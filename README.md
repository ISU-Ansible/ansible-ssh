SSHD
====

[![Build Status](https://travis-ci.org/ISU-Ansible/ansible-ssh.svg?branch=master)](https://travis-ci.org/ISU-Ansible/ansible-ssh)

The sshd role allows you to manage your sshd configuration. This role allows you to manage the /etc/ssh/ssh_config file as host or group variables. You can choose to use the default configuration settings, or replace the default variables with your own, preferred sshd_config file settings.


Variables
=========

Role Variables
--------------
1. opensshd_enabled
1. opensshd_manage_service
1. opensshd_allow_reload
1. opensshd_manage_var_run
1. opensshd_skip_defaults

Openssh Configuration Variables
-------------------------------
* opensshd_defaults
* opensshd
* opensshd_*SSHD Directive*

Default OS Variables
--------------------------
These variables should not be modified, as they are set for each OS.

1. opensshd_packages
1. opensshd_config_owner
1. opensshd_config_group
1. opensshd_config_mode
1. opensshd_config_file
1. opensshd_binary
1. opensshd_service
1. opensshd_sftp_server
1. opensshd_defaults
1. opensshd_os_supported

User Variables
--------------
Users are encouraged to modify the role variables inside their group_vars folder.


Tasks
=====

Description
-----------
1. Installs sshd, if necessary.
1. Modifies the SSHD configuration file.
1. Restarts SSHD, if necessary.

Changed Files
-------------
- /etc/ssh/sshd_config

Installed Programs
------------------
The OpenSSH Server program may be installed as a part of this role.

Role Actions
------------
This role installs sshd and configures it using the following variables in the order from most specific to least specific:

1. **opensshd\__Directive_**
1. **opensshd**
1. **opensshd_defaults**


Example
=======

**vars/default.yml**

    opensshd_defaults:
      Port: 22

**group_vars/_somegroup_**

    opensshd:
      Port: 222

**host_vars/_somehost.somegroup_**

    opensshd_Port: 2222

Given the previously mentioned setup, the following are true:

* For any host that is not in the group _somegroup_, and not named _somehost_, the ssh service will be run on port 22.
* For any host that is part of the group _somegroup_, but not named _somehost_, the ssh service will be run on port 222.
* For the singular host named _somehost_, the ssh service will be run on port 2222.

