# [Ansible role ssh_keys](#ssh_keys)

Manages ssh keys for users on linux users.

|GitHub|Downloads|Version|
|------|---------|-------|
|[![github](https://github.com/mullholland/ansible-role-ssh_keys/actions/workflows/molecule.yml/badge.svg)](https://github.com/mullholland/ansible-role-ssh_keys/actions/workflows/molecule.yml)|[![downloads](https://img.shields.io/ansible/role/d/mullholland/ssh_keys)](https://galaxy.ansible.com/mullholland/ssh_keys)|[![Version](https://img.shields.io/github/release/mullholland/ansible-role-ssh_keys.svg)](https://github.com/mullholland/ansible-role-ssh_keys/releases/)|
## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/mullholland/ansible-role-ssh_keys/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true
  vars:
    ssh_key_generate_keys:
      - user: "test1"
        path: "/home/test1/.ssh/id_rsa"
      - user: "test1"
        path: "/home/test1/.ssh/id_dsa"
        type: "dsa"
    ssh_key_generate_keys_host:
      - user: "test1"
        path: "/home/test1/.ssh/id_ecdsa"
        type: "ecdsa"
      - user: "test1"
        path: "/home/test1/.ssh/id_ed25519"
        type: "ed25519"
      - user: "test1"
        path: "/home/test1/.ssh/id_none"
        state: "absent"
    ssh_key_generate_keys_group:
      - user: "test2"
        group: "test2"
        path: "/home/test2/.ssh/id_rsa"
        size: "2048"
        type: "rsa"
        mode: "0600"
        passphrase: "SuperSecret"
        comment: "MyAnsibleGeneratedKey"

    ssh_key_copy_keys:
      - user: "test1"
        group: "test1"
        path: "/home/test1/.ssh/id_custom"
        private_key: "molecule/default/id_custom"
        public_key: "molecule/default/id_custom.pub"
    ssh_key_copy_keys_host:
      - user: "test1"
        path: "/home/test1/.ssh/id_custom2"
        private_key: "molecule/default/id_custom"
        public_key: "molecule/default/id_custom.pub"
    ssh_key_copy_keys_group:
      - user: "test1"
        path: "/home/test1/.ssh/id_custom3"
        private_key: "molecule/default/id_custom"
        public_key: "molecule/default/id_custom.pub"
      - user: "test1"
        path: "/home/test1/.ssh/id_custom4"
        state: "absent"

    ssh_key_authorized_keys:
      - name: "test1"
        authorized_keys:
          - key: "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGRjjIGC9+IuHJGTj9Uza95w1ftFJaWsE3JRFU0pnY55 test@key1"
      - name: "test1"
        authorized_keys:
          - key: "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAhD9U3aH1C1kp73VSqP7TPgOrdmISPeduwGS+KVoH72 test@key2"
            options: "none"
            path: "/home/test1/.ssh/authorized_keys2"
            manage_dir: "true"
            exclusive: "true"
            state: "present"
    ssh_key_authorized_keys_host:
      - name: "test1"
        authorized_keys:
          - key: "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIF0TnLs65tH9G97iVK04X4Bm6Ut6gt6mWrQQVWtSOqxW test@key3"
      - name: "test1"
        authorized_keys:
          - key: "{{ lookup('file', 'molecule/default/test-key5.pub') }}"
            state: "absent"
    ssh_key_authorized_keys_group:
      - name: "test1"
        authorized_keys:
          - key: "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIA87Rbzv+FAlhpeABa89m5kS85Pbrec6hmQ1VkS42s+6 test@key4"
      - name: "test1"
        authorized_keys:
          - key: "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFHRY74q8yDzW2NYwv6nLbSJUqIM2443vA//OM30bmix test@key6"
            state: "absent"

  roles:
    - role: "mullholland.ssh_keys"
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/mullholland/ansible-role-ssh_keys/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: true
  vars:
    pip_packages:
      - "cryptography"
      - "bcrypt"
    packages_debian:
      - "openssh-client"
    packages_redhat:
      - "openssh-clients"
    users_groups:
      - name: "test1"
      - name: "test2"

    users:
      - name: "test1"
        group: "test1"
      - name: "test2"
        group: "test2"
    packages_debian:
      - "openssh-client"
    packages_redhat:
      - "openssh-clients"
    users_groups:
      - name: "test1"
      - name: "test2"

    users:
      - name: "test1"
        group: "test1"
      - name: "test2"
        group: "test2"

  roles:
    - name: mullholland.repository_epel
    - name: mullholland.packages
    - name: mullholland.packages
    - name: mullholland.pip
    - name: mullholland.users
    - name: mullholland.users
```



## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/mullholland/ansible-role-ssh_keys/blob/master/defaults/main.yml):

```yaml
---
# Requirements
# The below requirements are needed on the host that executes this module.
# ssh-keygen (if backend=openssh)
# cryptography >= 2.6 (if backend=cryptography and OpenSSH < 7.8 is installed)
# cryptography >= 3.0 (if backend=cryptography and OpenSSH >= 7.8 is installed)


# Generate keys
# ssh_key_generate_keys, ssh_key_generate_keys_host and ssh_key_generate_keys_group
# are merged when managing the authorized keys.
# You can use the host and group lists to specify keys per host or group off hosts.
ssh_key_generate_keys: []
ssh_key_generate_keys_host: []
ssh_key_generate_keys_group: []
#  - user: ""        # (required)
#    group: ""       # (optional). Defaults to same as "owner"
#    path: ""        # (required). Where to create the key. Needs full Filename + Path (eg: -> /home/user/.ssh/id_rsa)
#    state: ""       # (optional). Defaults to "present"
#    size: ""        # (optional). Defaults to 4096
#    type: ""        # (optional).["rsa", "dsa", "rsa1", "ecdsa", "ed25519"] Defaults to rsa
#    mode: ""        # (optional). Defaults to "0600"
#    passphrase: ""  # (optional) Passphrase used to decrypt an existing private key or encrypt a newly generated private key.
#    comment: ""     # (optional)

ssh_key_copy_keys: []
#  - user: ""         # (required)
#    group: ""        # (optional). Defaults to same as "user"
#    path: ""         # (required). Where to create the key. Needs full Filename + Path (eg: -> /home/user/.ssh/id_rsa)
#    private_key: ""  # (optional). Will be created at the path
#    public_key: ""   # (optional).  Will be created at the path, with the ending ".pub"
ssh_key_copy_keys_host: []
ssh_key_copy_keys_group: []


# exclusively maanged the authorized keys file via template
# ssh_key_authorized_keys, ssh_key_authorized_keys_host and ssh_key_authorized_keys_group
# are merged when managing the authorized keys.
# You can use the host and group lists to specify keys per host or group off hosts.
ssh_key_authorized_keys: []
ssh_key_authorized_keys_host: []
ssh_key_authorized_keys_group: []
#  - name ""          # (Required). The username on the remote host whose authorized_keys file will be modified.
#    authorized_keys:
#      - key: ""           # (Required). The SSH public key(s), as a string or (since Ansible 1.9) url (https://github.com/username.keys).
#                                        at least 1 key is required. Can contain mutliplre keys as a list with multpile options.
#                                        if you want multiple exclusive keys in a file, all keys mus be in one key subelement
#        options: ""       # (optional). A string of ssh key options to be prepended to the key in the authorized_keys file.
#        path: ""          # (optional). Alternate path to the authorized_keys file. When unset, this value defaults to ~/.ssh/authorized_keys.
#        manage_dir: ""    # (optional). Whether this module should manage the directory of the authorized key file.
#                                        If set to true, the module will create the directory, as well as set the owner and permissions of
#                                        an existing directory. Be sure to set manage_dir=false if you are using an alternate directory for
#                                        authorized_keys, as set with path, since you could lock yourself out of SSH access.
#        exclusive: ""     # (optional). Whether to remove all other non-specified keys from the authorized_keys file.
#                                        Multiple keys can be specified in a single key string value by separating them by newlines.
#                                        This option is not loop aware, so if you use with_ , it will be exclusive per iteration of the loop.
#                                        If you want multiple keys in the file you need to pass them all to key in a single batch as mentioned above.
#        state: "present"  # (optional). Whether the given key (with the given key_options) should or should not be in the file.

#  - user: ""          # (Required). The username on the remote host whose authorized_keys file will be modified.
#    path: ""          # (optional). Alternate path to the authorized_keys file. When unset, this value defaults to ~/.ssh/authorized_keys.
#    manage_dir: ""    # (optional). Whether this module should manage the directory of the authorized key file.
#                                    If set to true, the module will create the directory, as well as set the owner and permissions of
#                                    an existing directory. Be sure to set manage_dir=false if you are using an alternate directory for
#                                    authorized_keys, as set with path, since you could lock yourself out of SSH access.
#    exclusive: ""     # (optional). Whether to remove all other non-specified keys from the authorized_keys file.
#                                    Multiple keys can be specified in a single key string value by separating them by newlines.
#                                    This option is not loop aware, so if you use with_ , it will be exclusive per iteration of the loop.
#                                    If you want multiple keys in the file you need to pass them all to key in a single batch as mentioned above.
#    keys: []          # (Required). List of multiple keys, which should be inside the file
#      - key: ""           # (Required). The SSH public key(s), as a string or (since Ansible 1.9) url (https://github.com/username.keys).
#                                        at least 1 key is required. Can contain mutliplre keys as a list with multpile options.
#                                        if you want multiple exclusive keys in a file, all keys mus be in one key subelement
#        options: ""       # (optional). A string of ssh key options to be prepended to the key in the authorized_keys file.
#        state: "present"  # (optional). Whether the given key (with the given key_options) should or should not be in the file.
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/mullholland/ansible-role-ssh_keys/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[mullholland.repository_epel](https://galaxy.ansible.com/mullholland/repository_epel)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-repository_epel/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-repository_epel/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-repository_epel/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-repository_epel)|
|[mullholland.pip](https://galaxy.ansible.com/mullholland/pip)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-pip/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-pip/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-pip/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-pip)|
|[mullholland.packages](https://galaxy.ansible.com/mullholland/packages)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-packages/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-packages/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-packages/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-packages)|
|[mullholland.users](https://galaxy.ansible.com/mullholland/users)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-users/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-users/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-users/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-users)|
|[mullholland.packages](https://galaxy.ansible.com/mullholland/packages)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-packages/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-packages/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-packages/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-packages)|
|[mullholland.users](https://galaxy.ansible.com/mullholland/users)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-users/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-users/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-users/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-users)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://mullholland.net) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/mullholland/ansible-role-ssh_keys/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/r/mullholland/enterpriselinux)|8, 9|
|[Fedora](https://hub.docker.com/r/mullholland/fedora/)|all|
|[Ubuntu](https://hub.docker.com/r/mullholland/ubuntu)|focal, jammy|
|[Debian](https://hub.docker.com/r/mullholland/debian)|all|

The minimum version of Ansible required is 2.10, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-ssh_keys/issues).

## [License](#license)

[MIT](https://github.com/mullholland/ansible-role-ssh_keys/blob/master/LICENSE).

## [Author Information](#author-information)

[mullholland](https://mullholland.net)
