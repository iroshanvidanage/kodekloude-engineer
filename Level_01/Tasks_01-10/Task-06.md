# Create a Cron Job

### Task

The Nautilus system admins team has prepared scripts to automate several day-to-day tasks. They want them to be deployed on all app servers in Stratos DC on a set schedule. Before that they need to test similar functionality with a sample cron job. Therefore, perform the steps below:

a. Install `cronie` package on __all Nautilus app servers__ and start `crond` service.
b. Add a cron __*/5 * * * * echo hello > /tmp/cron.text__ for `root` user.

---

### Solution

Repeat following steps in all app servers.

1. Install cronie in app server.

```bash

sudo -i

yum install cronie -y
systemctl start crond

```

2. Set the cronjob for `root` user.

```bash
crontab -e

# add this and save
*/5 * * * * echo hello > /tmp/cron.text

```

3. Verify cronjob.

```bash
crontab -l -u root
```
`-u root` is not needed `crontab -l` will show the cronjob list fro the current logged in user.

4. Restart the daemon

```bash
systemctl restart crond

ls -la /var/spool/cron
cat /var/spool/cron/root
```
