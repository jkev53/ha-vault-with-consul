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

Sample resources/vault-ssh-helper/config.hcl:
```
#ca_cert = "/opt/vault-ssh-helper.d/vault.crt"
vault_addr = "http://10.10.10.14:8200"
ssh_mount_point = "ssh"
allowed_roles="*"
tls_skip_verify = true
```

##### add to ansible.

```yaml
 - name: vault-ssh-helper config is present
      template: src=resources/vault-ssh-helper/config.hcl dest=/opt/vault-ssh-helper/config/config.hcl owner=100 group=1000 mode=0640

```

##### Configure PAM module

/etc/pam.d/sshd:
```
#@include common-auth
auth requisite pam_exec.so quiet expose_authtok log=/tmp/vaultssh.log /usr/local/bin/vault-ssh-helper -config=/opt/vault-ssh-helper.d/config.hcl
auth optional pam_unix.so not_set_pass use_first_pass nodelay
```

/etc/ssh/sshd_config:
```
ChallengeResponseAuthentication yes
UsePAM yes
PasswordAuthentication no
```