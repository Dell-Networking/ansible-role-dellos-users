Users Role for Dell EMC Networking OS
======================================

This role facilitates the configuration of global system user attributes. It supports the configuration of CLI users. This role is abstracted for dellos6, dellos9, and dellos10.


Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-users
```

Requirements
------------
This role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in Dell EMC Networking OS connection variables, or the ``provider``
dictionary.

Role Variables
--------------

Any role variable with a corresponding state variable set to absent negates the configuration of that variable. For variables with no state variable, setting an empty value for the variable negates the corresponding configuration.

The variables and its values are case-sensitive.

``dellos_users``list contains the following keys:

|        Key | Type                      | Notes                                                                                                                                                                                     |
|------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| userrole | stirng (required) | Configures role name which can be configured for the users. This key is not supported in dellos10 devices and dellos6 devices. |
| userrole_state | string, choices: absent, present* |If set to absent, deletes the userrole with specified name. |
| username | string (required)         | Configures username. The username must adhere to specific format guidelines. Valid usernames begin with A-Z, a-z, or 0-9 and can also contain any of the following characters: @#$%^&*-_= +;<>,.~  |
|   password | string                    | Configures password set for the username. Password length must be at least eight characters in dellos10 and dellos6 devices.                                              |
|   role | string                    | Configures the role assigned to the user. This key is not supported on dellos6 devices.             |
|  privilege | int                | Configures the privilege level for the user. For dellos9 devices, the permitted values are integers between 0 and 15. For dellos6 devices, the value can be either 0, 1, or 15. For dellos9 and dellos6, if you omit this key, the default privilege is 1. This key is not supported in dellos10 devices.                                      |
| access_class | string       |Configures the access-class for the user. This key is not supported in dellos0 and dellos6 devices. |
| pass_key | integer, choices: 0*, 7 |Configures password as encrypted if set to 7 in dellos9 devices. This key is not supported in dellos10 devices and dellos6 devices. |
| secret | string | Configures line password as secret in dellos9 devices. This key is not supported in dellos10 devices and dellos6 devices.|
| secret_key | integer, choices: 0*, 5| Configures secret line password using md5 encrypted algorithm. This key is not supported in dellos10 devices and dellos6 devices. |
| state | string, choices: absent, present*     | If set to absent, deletes a user account.                                                                                                                                 |
```
Note: Asterisk (*) denotes the default value if none is specified. 
```

Connection Variables
--------------------

Ansible Dell EMC Networking roles require the following connection information to establish 
communication with the nodes in your inventory. This information can exist in
the Ansible group_vars or host_vars directories, or in the playbook itself.



|         Key | Required | Choices    | Description                              |
| ----------: | -------- | ---------- | ---------------------------------------- |
|        host | yes      |            | Hostname or address for connecting to the remote device over the specified ``transport``. The value of this key is the destination address for the transport. |
|        port | no       |            | Port used to build the connection to the remote device. If the value of this key does not specify the value, the value defaults to 22. |
|    username | no       |            | Configures the username that authenticates the connection to the remote device. The value of this key authenticates the CLI login. If this key does not specify a value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password that authenticates the connection to the remote device. If this key does not specify the value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If this key does not specify the value, the value of environment variable ANSIBLE_NET_AUTHORIZE is used instead. If not specified, the device attempts to execute all commands in non-privileged mode.|
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If ``authorize`` is set to no, then this key is not applicable. If this key does not specify the value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. This key supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all of the above connection arguments as a dictonary object. All constraints (such as required, choices) must be met either by individual arguments or values in this dictonary. |


```
Note: Asterisk (*) denotes the default value if none is specified.
```

Dependencies
------------

The dellos-users role is built on modules included in the core Ansible code.
These modules were added in Ansible version 2.2.0.

Example Playbook
----------------

The following example uses the dellos.dellos-users role to completely set up
CLI users. 
The example creates a ``hosts`` file with the switch details and corresponding 
variables.
It writes a simple playbook that only references the dellos-users role. 
By including the role, you automatically get access to all of the tasks to configure user
features. 


Sample ``hosts`` file:
 
    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9/dellos6/dellos10)>

Sample ``host_vars/leaf1``:

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: xxxxx 
      password: xxxxx
      authorize: yes
      auth_pass: xxxxx 
      transport: cli
	  
    dellos_users:
       - userrole: role1
         userrole_state: present
       - username: u1
         password: test
         role: sysadmin
         privilege: 0
         state: absent
       - username: u1
         password: false
         privilege: 1
         access_class: a1
         role: netadmin
         state: present
       - username: u2
         secret: test1
         secret_key : 0
         access_class: a2
         privilege: 3
         role: sysadmin
         state: present

 
Simple playbook to setup users, ``leaf.yaml``:

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-users

Then run with:

    ansible-playbook -i hosts leaf.yaml

License
--------

Copyright (c) 2016, Dell Inc. All rights reserved.
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
    http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
