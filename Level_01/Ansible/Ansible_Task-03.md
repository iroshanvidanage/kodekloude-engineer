# Configure Default SSH User for Ansible

### Task

On the jump host, modify the default configuration of Ansible to enable the use of anita as the default SSH user for all hosts. Ensure to make changes within Ansible's default configuration without creating a new one.

---

### Solution

```bash

# edit the [defaults] in the ansible.cfg
sudo vi /etc/ansible/ansible.cfg

```

Add the following to the `ansible.cfg`

```bash
[defaults]
remote_user = anita
```
