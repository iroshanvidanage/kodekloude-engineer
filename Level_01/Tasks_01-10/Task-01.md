# Linux User Setup with Non-Interactive Shell

### Task

Create a user in the server with non-interactive shell.

---

### Solution

I cannot remember the user name I was given in this task, hence using `user`.

```bash
sudo -i

# check of user is present
id user

# create user with non-interactive shell
adduser user -s /sbin/nologin

id user

cat /etc/passwd | grep user

```
