# Linux SSH Authentication

### Task

The system admins team of xFusionCorp Industries has set up some scripts on jump host that run on regular intervals and perform operations on all app servers in Stratos Datacenter. To make these scripts work properly we need to make sure thor user on jump host has password-less SSH access to all app servers through their respective sudo users. Based on the requirements, perform the following:

Set up a password-less authentication from user thor on jump host to all app servers through their respective sudo users.

---

### Solution

Create passwordless access

```bash
# create ssh key: I saved key in default path without passphrase.
ssh-keygen -t rsa -b 4096

# copy the ssh-key to app servers
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03

# verify
ssh tony@stapp01
ssh steve@stapp02
ssh banner@stapp03

```
