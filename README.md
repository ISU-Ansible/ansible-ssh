# SSHD

[![Build Status](https://travis-ci.org/raznikk/ansible-ssh.svg?branch=master)](https://travis-ci.org/raznikk/ansible-ssh)
 
The sshd role allows you to manage your sshd configuration.  This role allows you to manage the /etc/ssh/ssh_config file as host or group variables. You can choose to use the default configuration settings, or replace the default variables with your own, preferred sshd_config file settings.

If you choose, you can also manage Match blocks using additional variables.


## Variables

### Global Variables
No global variables are used in this role.

### Role Variables
1. opensshd_server_settings (required)
1. opensshd_server_matchblocks (optional)
1. opensshd_server_allowusers (optional)
1. opensshd_server_allowgroups (optional)
1. opensshd_server_denyusers (optional)
1. opensshd_server_denygroups (optional)

### User Variables
Users are encouraged to modify the role variables inside their group_vars folder.


## Tasks

### Description
1. Modifies the SSH configuration file.
1. Restarts SSHD if necessary


### Changed Files
- /etc/ssh/sshd_config

### Installed Programs
The OpenSSH Server program may be installed as a part of this role.

## Example

### Sample Setup

Here is a sample of how you might set these variables.  Look at this carefully.  **Note that there is a colon after each parameter name in the list!  The parameters in an sshd_config file do not have colons after them!** You can't just cut and paste these paramters from an sshd_config file. You have to add the colons.
```
opensshd_server_settings:
  Protocol: "2"
  SyslogFacility: "AUTHPRIV"
  RSAAuthentication: "no"
  ChallengeResponseAuthentication: "no"
  AuthorizedKeysFile:     ".ssh/authorized_keys"
  TCPKeepAlive: "yes"
  Subsystem:  "sftp    /usr/libexec/openssh/sftp-server"
  PubkeyAuthentication: "no"
  PermitRootLogin: "without-password"
  GSSAPIAuthentication: "no"
  GSSAPICleanupCredentials: "no"
  KerberosAuthentication: "no"
  KerberosOrLocalPasswd: "no"
  X11Forwarding: "yes"
  PasswordAuthentication: "yes"
  UsePAM: "yes"
  Port: "22"

```

But whatever parameters you put here will be put into the sshd_config file in the correct format.

If different systems have different sshd_config settings, you will need to set a different *opensshd_server_settings* variable for each desired configuration.  But often you only want to change one or two parameters for different systems, not the entire list of parameters. It would be nice if you could define a common list of global paraters and then only have to set one or two parameters for a particular system.  Well, inside of the **opensshd_server_settings** variable, you can also set some of the parameters to be variables as well.  Kind of like variables inside a variable!  Here's an example that sets the values for Port and PasswordAuthentication as variables:
```yaml
my_sshd_Port: "22"
my_sshd_PasswordAuthention: "no"

opensshd_server_settings:
  Protocol: "2"
  SyslogFacility: "AUTHPRIV"
  RSAAuthentication: "no"
  ChallengeResponseAuthentication: "no"
  AuthorizedKeysFile:     ".ssh/authorized_keys"
  TCPKeepAlive: "yes"
  Subsystem:  "sftp    /usr/libexec/openssh/sftp-server"
  TCPKeepAlive: "yes"
  PubkeyAuthentication: "no"
  PermitRootLogin: "without-password"
  GSSAPIAuthentication: "no"
  GSSAPICleanupCredentials: "no"
  KerberosAuthentication: "no"
  KerberosOrLocalPasswd: "no"
  X11Forwarding: "yes"
  PasswordAuthentication: "{{ my_sshd_PasswordAuthentication }}"
  Port: "{{ my_sshd_Port }}"
```

The values for the **my_sshd_PasswordAuthentication** and **my_sshd_Port** variables will be substituted into the final values.  This way, you can have a common list of parameters but are able to override some of the values more easily on a group or host basis.

### Match blocks
Match blocks are an extremely powerful feature in openssh that allow you to set different SSH controls based on IP address, hostname,  or user that is cconnecting to SSH.

The match blocks are added at the end of the **sshd_config** file.  The **opensshd_server_matchblocks** variable is structured so you can define multiple match blocks, as needed.  Whatever parameters are assigned in the Match block override the default sshd settings set by **isu_sec_opensshd_server_settings**.

The opensshd_server_matchblocks structure is pretty straight forward.  Each item in the list begins with a new rule.

```
opensshd_server_matchblocks:
  - rule: "some name"
    criteria: User|Group|Host|Address
    match_list:
      - A list of patterns corresponding to the match criteria for this rule.
      - Can be an IP address or address block, hostname, username, or user group.
      - Each pattern added here is added to a comma separated list in the Match rule.
    keywords:
      [any sshd_config parameters that are valid within a Match block go here.
          One parameter/value per line.  Parameter name must end with a colon]
```

#### Match Example
Here's an example with two Match blocks set:

```
opensshd_server_matchblocks:
  - rule: "admin hosts"
    criteria: Address
    match_list:
      - 10.10.99.99
      - 129.186.99.1
    keywords:
      PubkeyAuthentication: "yes"
      PermitRootLogin: "without-password"
      PasswordAuthentication: "no"

  - rule: "trusted users"
    criteria: User
    match_list:
      - user1
      - user2
    keywords:
      PubkeyAuthentication: "yes"
      PasswordAuthentication: "yes"
```

The **opensshd_server_matchblocks** variable above will produce the following lines in sshd_config:

```
# match admin hosts
Match Address 1.2.3.4,2.3.4.5
PubkeyAuthentication yes
PermitRootLogin without-password
PasswordAuthentication no

# match trusted users
Match User user1,user2
PubkeyAuthentication yes
PasswordAuthentication yes
```

### Allow/Deny Users/Groups
Additionally, there is
