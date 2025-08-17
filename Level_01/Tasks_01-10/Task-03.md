# Secure Root SSH Access

### Task

After doing some security audits of servers, xFusionCorp Industries security team has implemented some new security policies. One of them is to disable direct root login through SSH.

Disable direct SSH root login on all app servers in Stratos Datacenter.

---

### Solution

Configure app servers to block root login.

```sh

# login to servers and switch to root

cat /etc/ssh/sshd_config | grep PermitRoot

vi /etc/ssh/sshd_config

# change yes to no
PermitRootLogin yes --> no

cat /etc/ssh/sshd_config | grep PermitRoot

# restart sshd
systemct restart sshd
```
