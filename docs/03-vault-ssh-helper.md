# Vault SSH Helper

https://github.com/hashicorp/vault-ssh-helper

##### Install vault-ssh-helper via Ansible

```yaml
- name: download and install vault binary
  unarchive:
    src: "https://releases.hashicorp.com/vault-ssh-helper/{{ vault_ssh_helper_version }}/vault-ssh-helper_{{ vault_ssh_helper_version }}_linux_amd64.zip"
    dest: /usr/local/bin/
    remote_src: True
    creates: /usr/local/bin/vault-ssh-helper
  become: true
```

##### Create config file

Sample config.hcl:
```
vault_addr = "https://vault.example.com:8200"
ssh_mount_point = "ssh"
ca_cert = "/etc/vault-ssh-helper.d/vault.crt"
tls_skip_verify = false
allowed_roles = "*"

```

##### Configure PAM module

/etc/pam.d/sshd:
```
#@include common-auth
auth requisite pam_exec.so quiet expose_authtok log=/tmp/vaultssh.log /usr/local/bin/vault-ssh-helper -config=/etc/vault-ssh-helper.d/config.hcl
auth optional pam_unix.so not_set_pass use_first_pass nodelay
```

/etc/ssh/sshd_config:
```
ChallengeResponseAuthentication yes
UsePAM yes
PasswordAuthentication no
```