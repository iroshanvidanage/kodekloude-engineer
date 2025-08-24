# Linux Bash Scripts

### Task

The production support team of xFusionCorp Industries is working on developing some bash scripts to automate different day to day tasks. One is to create a bash script for taking websites backup. They have a static website running on App Server 3 in Stratos Datacenter, and they need to create a bash script named backup.sh which should accomplish the following tasks. (Also remember to place the script under /scripts directory)

1. Create a zip archive named xfusioncorp.zip of /var/www/html/blog directory.

2. Save the archive in /backup/ on App Server 3. This is a temporary storage, as backups from this location will be clean on weekly basis. Therefore, we also need to save this backup archive on Nautilus Backup Server.

3. Copy the created archive to Nautilus Backup Server server in /backup/ location.

4. Please make sure script will not ask for password while copying the archive file. Additionally, the respective server user (for example, steve in case of App Server 2) must be able to run it.

---

### Solution

Passwordless ssh

```bash

# from App Server 3 to backup_server
ssh-keygen -t rsa -b 4096

ssh-copy-id clint@stbkp01

```

Create the script

```bash
vi /script/backup.sh

sudo chmod 755 /script/backup.sh
```

Script

```bash
zip -r xfusioncorp.zip /var/www/html/blog
mv xfusioncorp.zip /backup/
scp -r /backup/xfusioncorp.zip clint@stbkp01:/backup/
```
