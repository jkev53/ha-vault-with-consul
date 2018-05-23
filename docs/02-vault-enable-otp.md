# One-Time SSH Passwords

https://www.vaultproject.io/docs/secrets/ssh/one-time-ssh-passwords.html#create-a-role

##### Mount the secrets engine

```
vault secrets enable ssh

```

##### Create a Role

```
vault write ssh/roles/operator_1 \
    key_type=otp \
    default_user=operator_1 \
    admin_user=true \
    cidr_list=10.10.10.11/32

```

##### Create a Credential

Create an OTP credential for an IP of the remote host that belongs to operator_1.

```
vault write ssh/creds/operator_1 ip=10.10.10.11

```

```
Key             Value
lease_id        ssh/creds/operator_1/73bbf513-9606-4bec-816c-5a2f009765a5
lease_duration  600
lease_renewable false
port            22
username        operator_1
ip              10.10.10.11
key             2f7e25a2-24c9-4b7b-0d35-27d5e5203a5c
key_type        otp
```

##### Establish an SSH session
```
ssh operator_1@10.10.10.11
Password: <Enter OTP>
operator_1@ip:~$
```

##### Automate it!
A single CLI command can be used to create a new OTP and invoke SSH with the correct parameters to connect to the host.

```
$ vault ssh -role operator_1 -mode otp operator_1@10.10.10.11

```

[Note: Install `sshpass` to automate typing in OTP]
Password: <Enter OTP>
The OTP will be entered automatically using sshpass if it is installed.

```
vault ssh -role operator_1 -mode otp -strict-host-key-checking=no operator_1@10.10.10.11

```