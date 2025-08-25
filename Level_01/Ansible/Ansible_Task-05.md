# Create Files on App Servers using Ansible

### Task

The Nautilus DevOps team is testing various Ansible modules on servers in Stratos DC. They're currently focusing on file creation on remote hosts using Ansible. Here are the details:


a. Create an inventory file ~/playbook/inventory on jump host and include all app servers.


b. Create a playbook ~/playbook/playbook.yml to create a blank file /home/appdata.txt on all app servers.


c. Set the permissions of the /home/appdata.txt file to 0755.


d. Ensure the user/group owner of the /home/appdata.txt file is tony on app server 1, steve on app server 2 and banner on app server 3.


Note: Validation will execute the playbook using the command ansible-playbook -i inventory playbook.yml, so ensure the playbook functions correctly without any additional arguments.

---

### Solution

1. Change into directory path `/home/thor/playbook/`.

```bash

cd playbook/

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
- name: Create file /home/appdata.txt
  hosts: app_servers
  become: true
  tasks:
    - name: Create blank file
      ansible.builtin.file:
        path: /home/appdata.txt
        state: touch

    - name: Change ownership
      ansible.builtin.file:
        path: /home/appdata.txt
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
        mode: '0755'

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

---

Terminal work...

```bash

thor@jumphost ~$ ls -la
total 32
drwxr----- 1 thor thor 4096 Aug 25 19:04 .
drwxr-xr-x 1 root root 4096 Jun  7  2024 ..
-rwxrwx--- 1 thor thor   18 Feb 15  2024 .bash_logout
-rwxrwx--- 1 thor thor  141 Feb 15  2024 .bash_profile
-rwxrwx--- 1 thor thor  784 Aug 25 19:04 .bashrc
drwxrwx--- 1 thor thor 4096 Jun  7  2024 .config
drwxr----- 2 thor thor 4096 Aug 25 19:04 .ssh
drwxr-xr-x 2 thor thor 4096 Aug 25 19:04 playbook
thor@jumphost ~$ cd playbook/
thor@jumphost ~/playbook$ ls -la
total 8
drwxr-xr-x 2 thor thor 4096 Aug 25 19:04 .
drwxr----- 1 thor thor 4096 Aug 25 19:04 ..
thor@jumphost ~/playbook$ vi inventory
thor@jumphost ~/playbook$ ls -la
total 12
drwxr-xr-x 2 thor thor 4096 Aug 25 19:14 .
drwxr----- 1 thor thor 4096 Aug 25 19:04 ..
-rw-r--r-- 1 thor thor  326 Aug 25 19:14 inventory
thor@jumphost ~/playbook$ vi playbook.yml
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ chmod a+x playbook.yml
thor@jumphost ~/playbook$ ls -la
total 16
drwxr-xr-x 2 thor thor 4096 Aug 25 19:26 .
drwxr----- 1 thor thor 4096 Aug 25 19:04 ..
-rw-r--r-- 1 thor thor  326 Aug 25 19:14 inventory
-rwxr-xr-x 1 thor thor  361 Aug 25 19:26 playbook.yml
thor@jumphost ~/playbook$ chmod a+x *
thor@jumphost ~/playbook$ ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (/home/thor/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/thor/.ssh/id_rsa
Your public key has been saved in /home/thor/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:btMSInoORHmU3Df76xLby+5s+AhA+RgCcYs0LYO7Pko thor@jumphost.stratos.xfusioncorp.com
The key's randomart image is:
+---[RSA 4096]----+
|++o..o           |
|o*.=o.. o        |
|..B =  . o       |
|.. + +  .        |
| .. + o S.       |
|.. . o o.o.      |
|.Eo . . ==..     |
|.o +   o+*+      |
|o . .   .BO.     |
+----[SHA256]-----+
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ ansible all -i inventory -m ping
stapp03 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'stapp03' (ED25519) to the list of known hosts.\r\nbanner@stapp03: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).",
    "unreachable": true
}
stapp02 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'stapp02' (ED25519) to the list of known hosts.\r\nsteve@stapp02: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).",
    "unreachable": true
}
stapp01 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'stapp01' (ED25519) to the list of known hosts.\r\ntony@stapp01: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).",
    "unreachable": true
}
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ ssh-copy-id tony@stapp01
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/thor/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
tony@stapp01's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'tony@stapp01'"
and check to make sure that only the key(s) you wanted were added.

thor@jumphost ~/playbook$ ssh-copy-id steve@stapp02
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/thor/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
steve@stapp02's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'steve@stapp02'"
and check to make sure that only the key(s) you wanted were added.

thor@jumphost ~/playbook$ ssh-copy-id banner@stapp03
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/thor/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
banner@stapp03's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'banner@stapp03'"
and check to make sure that only the key(s) you wanted were added.

thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ 
thor@jumphost ~/playbook$ ansible all -i inventory -m ping
stapp03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
stapp01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
stapp02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
thor@jumphost ~/playbook$ ansible-playbook -i inventory playbook.yml

PLAY [Create file /home/appdata.txt] **************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************
ok: [stapp02]
ok: [stapp01]
ok: [stapp03]

TASK [Create blank file] **************************************************************************************************************
changed: [stapp03]
changed: [stapp01]
changed: [stapp02]

TASK [Change ownership] ***************************************************************************************************************
changed: [stapp03]
changed: [stapp01]
changed: [stapp02]

PLAY RECAP ****************************************************************************************************************************
stapp01                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

thor@jumphost ~/playbook$ ssh tony@stapp01
Last login: Mon Aug 25 19:31:14 2025 from 172.16.238.3
[tony@stapp01 ~]$ cd ../
[tony@stapp01 home]$ ls -la
total 16
drwxr-xr-x 1 root    root    4096 Aug 25 19:29 .
drwxr-xr-x 1 root    root    4096 Aug 25 19:04 ..
drwx------ 1 ansible ansible 4096 Jul 16  2024 ansible
-rwxr-xr-x 1 tony    tony       0 Aug 25 19:31 appdata.txt
drwx------ 1 tony    tony    4096 Aug 25 19:30 tony
[tony@stapp01 home]$ 
[tony@stapp01 home]$ 
[tony@stapp01 home]$ stat -c '%a %n' appdata.txt 
755 appdata.txt
[tony@stapp01 home]$ 
[tony@stapp01 home]$ 
[tony@stapp01 home]$ 

```