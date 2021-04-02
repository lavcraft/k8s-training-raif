TEST CONNECTION
===============
```shell
ansible -i hosts.yml -m shell -a 'uname -a' all --ask-pass --ask-become-pass --extra-vars 'ansible_user=root'
```

DRY RUN
=======
```shell
ansible-playbook site.yml -i hosts.yml --check --ask-pass --ask-become-pass --extra-vars 'ansible_user=root'
```

RUN PROVISIONING
================
```shell
ansible-playbook site.yml -i hosts.yml --ask-pass --ask-become-pass --extra-vars 'ansible_user=root' 
```
