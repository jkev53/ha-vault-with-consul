# Auto Unseal Vault HowTo


## Launch New Terminal

##### 1. SSH into operator terminal

```
vagrant ssh operator

```

##### 2 - Export Vault Address

```
VAULT_ADDR=http://10.10.10.11:8200

```

##### 5 - Operator Init (NOT FOR PRODUCTION)

```
vault operator init > init.txt
cat init.txt

```

##### 6 - Extract the keys into environment variables

```
UNSEAL_KEY1=`cat init.txt | grep "Unseal Key 1:" | cut -d' ' -f4`
echo $UNSEAL_KEY1

UNSEAL_KEY2=`cat init.txt | grep "Unseal Key 2:" | cut -d' ' -f4`
echo $UNSEAL_KEY2

UNSEAL_KEY3=`cat init.txt | grep "Unseal Key 3:" | cut -d' ' -f4`
echo $UNSEAL_KEY3

ROOT_KEY=`cat init.txt | grep "Initial Root Token" | cut -d' ' -f4`
echo $ROOT_KEY

```

##### 7 - Operator Unseal

```
vault operator unseal $UNSEAL_KEY1
vault operator unseal $UNSEAL_KEY2
vault operator unseal $UNSEAL_KEY3

```

##### 8 - Root Login

```
vault login $ROOT_KEY

```