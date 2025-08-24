# Create Ansible Inventory for App Server Testing

### Task

Create Ansible Inventory for App Server Testing

---

### Solution

Generate the ssh key and copy to app servers.

```bash

# create the key
ssh-keygen -t rsa -b 4096

# copy the ssh-key
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03
```

Create the `hosts.ini` file.

```ini
[app_servers]
stapp01 ansible_user=tony
stapp02 ansible_user=steve
stapp03 ansible_user=banner
```
