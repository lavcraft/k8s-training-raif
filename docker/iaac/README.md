TEST CONNECTION
===============
```shell
ansible -i hosts.yml -m shell -a 'uname -a' all --extra-vars 'ansible_user=root'
```

DRY RUN
=======
```shell
ansible-playbook site.yml -i hosts.yml --check --extra-vars 'ansible_user=root'
```

RUN PROVISIONING
================
```shell
ansible-playbook site.yml -i hosts.yml --extra-vars 'ansible_user=root'
```
