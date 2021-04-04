SETUP ENV
=========
```shell
export ANSIBLE_HOST_KEY_CHECKING=False
vi hosts.yml #TODOs
```

TEST CONNECTION
===============
```shell
ansible -i hosts.yml -m shell -a 'uname -a' all --ask-pass --ask-become-pass
```

DRY RUN
=======
```shell
ansible-playbook site.yml -i hosts.yml --check --ask-pass --ask-become-pass
```

RUN PROVISIONING
================
```shell
ansible-playbook site.yml -i hosts.yml --ask-pass --ask-become-pass 
```
