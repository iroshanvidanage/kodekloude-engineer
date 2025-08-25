# Copy Data to App Servers using Ansible

### Task

The Nautilus DevOps team needs to copy data from the jump host to all application servers in Stratos DC using Ansible. Execute the task with the following details:


a. Create an inventory file /home/thor/ansible/inventory on jump_host and add all application servers as managed nodes.


b. Create a playbook /home/thor/ansible/playbook.yml on the jump host to copy the /usr/src/itadmin/index.html file to all application servers, placing it at /opt/itadmin.


Note: Validation will run the playbook using the command ansible-playbook -i inventory playbook.yml. Ensure the playbook functions properly without any extra arguments.

---

### Solution

1. Change into directory path `/home/thor/ansible/`.

```bash

cd ansible/

```

2. Create the `inventory` file.

```bash
[app_servers]
stapp01 ansible_connection=ssh ansible_user=tony ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp02 ansible_connection=ssh ansible_user=steve ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp03 ansible_connection=ssh ansible_user=banner ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

3. Create the `playbook.yml` file.

```yaml
---
- name: Copy /usr/src/finance/index.html to remote server
  hosts: app_servers
  become: true
  tasks:
    - name: Copy file to remote server
      ansible.builtin.copy:
        src: /usr/src/finance/index.html
        dest: /opt/finance/
```

4. Change the execution permissions of the files.

```bash

chmod a+x playbook.yml

```

5. Set the passwordless connectivity.

```bash

# create the ssh-key
ssh-keygen -t rsa -b 4096

# copy the ssh-key to other servers
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03

```

6. Test the connectivity.

```bash

ansible all -i inventory -m ping

```
