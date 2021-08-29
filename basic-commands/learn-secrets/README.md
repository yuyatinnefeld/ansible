
# Secrets

you can use ansible vault file to encrypt the secret information

```bash
mkdir group_vars
mkdir group_vars/all
touch group_vars/all/vars
touch group_vars/all/vault
```

```bash
# group_vars/all/vars
password: '{{vault_special_secretpwd}}'
```

```bash
# group_vars/all/vault
vault_special_secretpwd: thisIsMyPassword
```

```bash
cd group_vars/all
more vars
password: '{{vault_special_secretpwd}}'
```

```bash
ansible-vault encrypt vault
```

## encrypt
```bash
# ansible-vault encrypt vault (e.g. hallo)
New Vault password: 
Confirm New Vault password: 
Encryption successful
```

```bash
# more vault

$ANSIBLE_VAULT;1.1;AES256
65613437643434336264643636343861343661326536646438366165346262313765353865633733
3238663234353066353131336235383833366661643631300a313432363233363534656638613033
62306561663933316435326536396562616562356532373639326331326466326364356632393564
3566383166333039660a636537306164643166313666353736663332376164626365313038303734
37363461366263313466376637656265333463643336373162656666373863633566653964393634
3332643666363661653565376139393931303030376335323064
```

## edit
```bash
ansible-vault edit vault
```

after the vault setup you must use --ask-vault-pass for the playbook run
```bash
ansible-playbook -i inventory.ini tasks1.yml --ask-vault-pass
```

## decrypt vault
```bash
ansible-vault decrypt vault
```