# MariaDB Troubleshooting

### Task

There is a critical issue going on with the Nautilus application in Stratos DC. The production support team identified that the application is unable to connect to the database. After digging into the issue, the team found that mariadb service is down on the database server.

Look into the issue and fix the same.

---

### Solution

Troubleshooting steps

```bash

# login to the server and switch to root
ssh peter@stdb01

sudo -i


# check mariadb service
systemctl status mariadb

# tried to start the service and checked the status again
systemctl start mariadb
# ->
# the service showed the starting failed and to use journalctl for more details..

journalctl -u mariadb.service --no-pager
# -> following log line was there, but not enough data to get an idea..
mariadb.service: Installed new job mariadb.service/stop as 114

```

MariaDB error logs inspection.

```bash
tail -f -n 1000 /var/log/mysql/error.log

mariadb.service: Executing: /usr/libexec/mariadb-prepare-db-dir mariadb.service are-db-dir[2625]: Database MariaDB is not initialized, but the directory /var/lib/mysql is not empty, so initialization cannot be done. are-db-dir[2625]: Make sure the /var/lib/mysql is empty before running mariadb-prepare-db-dir.

```

- The issue seems to be the db dir was created before the db was initialized.
- The `/var/lib/mysql` was empty.
- Cleaning the dir didn't help at first, `rm -rf /var/lib/mysql/` but after removing the dir the service started.
- The issue seems to be the ownership of the data dir. Initally it was `mysql:mysql` and it should be `root:root`. 
- Not sure why... this [link](https://www.ucartz.com/clients/knowledgebase/1233/How-to-run-MariaDB-as-a-custom-user-and-group-other-than-mysql-on-CentOS-or-RHEL-7.html) shows the mariadb-server package is owned by `mysql` user.

Full log

```bash
mariadb.service: Got final SIGCHLD for state start-post. 
mariadb.service: Changed start-post -> running 
mariadb.service: Job 72 mariadb.service/start finished, result=done Started MariaDB 10.5 database server. Subject: A start job for unit mariadb.service has finished successfully Defined-By: systemd Support: https://access.redhat.com/support
A start job for unit mariadb.service has finished successfully.
The job identifier is 72. 
mariadb.service: Trying to enqueue job mariadb.service/stop/replace 
mariadb.service: Installed new job mariadb.service/stop as 114 
mariadb.service: Enqueued job mariadb.service/stop as 114 
mariadb.service: Changed running -> stop-sigterm Stopping MariaDB 10.5 database server... Subject: A stop job for unit mariadb.service has begun execution Defined-By: systemd Support: https://access.redhat.com/support
A stop job for unit mariadb.service has begun execution.
The job identifier is 114. 
mariadb.service: Got notification message from PID 1472 (STOPPING=1, STATUS=Shutdown in progress) 
mariadb.service: Got notification message from PID 1472 (STATUS=Dumping buffer pool page 1/176, EXTEND_TIMEOUT_USEC=30000000) 
mariadb.service: Got notification message from PID 1472 (STATUS=Waiting for page cleaner thread to exit, EXTEND_TIMEOUT_USEC=120000000) 
mariadb.service: Got notification message from PID 1472 (STATUS=Waiting to flush the buffer pool, EXTEND_TIMEOUT_USEC=30000000) 
mariadb.service: Got notification message from PID 1472 (STATUS=ensuring dirty buffer pool are written to log, EXTEND_TIMEOUT_USEC=30000000) 
mariadb.service: Got notification message from PID 1472 (STATUS=Free innodb buffer pool, EXTEND_TIMEOUT_USEC=30000000) 
mariadb.service: Got notification message from PID 1472 (STATUS=MariaDB server is down) 
mariadb.service: Got notification message from PID 1472 (STATUS=MariaDB server is down) 
mariadb.service: Child 1472 belongs to mariadb.service. 
mariadb.service: Main process exited, code=exited, status=0/SUCCESS (success) Subject: Unit process exited Defined-By: systemd Support: https://access.redhat.com/support
An ExecStart= process belonging to unit mariadb.service has exited.
The process' exit code is 'exited' and its exit status is 0. Aug 12 17:44:20 stdb01.stratos.xfusioncorp.com systemd[1]: 
mariadb.service: Deactivated successfully. Subject: Unit succeeded Defined-By: systemd Support: https://access.redhat.com/support
The unit mariadb.service has successfully entered the 'dead' state. 
mariadb.service: Service restart not allowed. 
mariadb.service: Changed stop-sigterm -> dead 
mariadb.service: Job 114 mariadb.service/stop finished, result=done Stopped MariaDB 10.5 database server. Subject: A stop job for unit mariadb.service has finished Defined-By: systemd Support: https://access.redhat.com/support
A stop job for unit mariadb.service has finished.
The job identifier is 114 and the job result is done. 
mariadb.service: Trying to enqueue job mariadb.service/start/replace 
mariadb.service: Installed new job mariadb.service/start as 243 
mariadb.service: Enqueued job mariadb.service/start as 243 
mariadb.service: Will spawn child (service_enter_start_pre): /usr/libexec/mariadb-check-socket 
mariadb.service: Failed to reset devices.allow/devices.deny: Operation not permitted 
mariadb.service: Failed to set 'trusted.invocation_id' xattr on control group /docker/4c2bd456b446b2112051125c2082bd48cfebc3c3de3210346418e55dd8f4ca11/system.slice/mariadb.service, ignoring: Operation not permitted 
mariadb.service: Failed to remove 'trusted.delegate' xattr flag on control group /docker/4c2bd456b446b2112051125c2082bd48cfebc3c3de3210346418e55dd8f4ca11/system.slice/mariadb.service, ignoring: Operation not permitted 
mariadb.service: About to execute /usr/libexec/mariadb-check-socket 
mariadb.service: Forked /usr/libexec/mariadb-check-socket as 2591 ]: PR_SET_MM_ARG_START failed: Operation not permitted 
mariadb.service: Changed dead -> start-pre Starting MariaDB 10.5 database server... Subject: A start job for unit mariadb.service has begun execution Defined-By: systemd Support: https://access.redhat.com/support
A start job for unit mariadb.service has begun execution.
The job identifier is 243. 
mariadb.service: User lookup succeeded: uid=27 gid=27 Bind-mounting / on /run/systemd/unit-root (MS_BIND|MS_REC "")... Applying namespace mount on /run/systemd/unit-root/run/credentials Bind-mounting /run/systemd/inaccessible/dir on /run/systemd/unit-root/run/credentials (MS_BIND|MS_REC "")... Successfully mounted /run/systemd/inaccessible/dir to /run/systemd/unit-root/run/credentials Applying namespace mount on /run/systemd/unit-root/run/systemd/incoming Followed source symlinks /run/systemd/propagate/mariadb.service → /run/systemd/propagate/mariadb.service. Bind-mounting /run/systemd/propagate/mariadb.service on /run/systemd/unit-root/run/systemd/incoming (MS_BIND "")... Successfully mounted /run/systemd/propagate/mariadb.service to /run/systemd/unit-root/run/systemd/incoming Applying namespace mount on /run/systemd/unit-root/tmp Bind-mounting /tmp/systemd-private-682f1dabf7c349f5a364cf8c7a3bf2dd-mariadb.service-BRHkau/tmp on /run/systemd/unit-root/tmp (MS_BIND|MS_REC "")... Successfully mounted /tmp/systemd-private-682f1dabf7c349f5a364cf8c7a3bf2dd-mariadb.service-BRHkau/tmp to /run/systemd/unit-root/tmp Applying namespace mount on /run/systemd/unit-root/var/tmp Bind-mounting /var/tmp/systemd-private-682f1dabf7c349f5a364cf8c7a3bf2dd-mariadb.service-Vz0aIB/tmp on /run/systemd/unit-root/var/tmp (MS_BIND|MS_REC "")... Successfully mounted /var/tmp/systemd-private-682f1dabf7c349f5a364cf8c7a3bf2dd-mariadb.service-Vz0aIB/tmp to /run/systemd/unit-root/var/tmp Remounted /run/systemd/unit-root/run/credentials. Remounted /run/systemd/unit-root/run/systemd/incoming. Remounted /run/systemd/unit-root/run/credentials. 
mariadb.service: Executing: /usr/libexec/mariadb-check-socket 
mariadb.service: Child 2591 belongs to mariadb.service. 
mariadb.service: Control process exited, code=exited, status=0/SUCCESS (success) Subject: Unit process exited Defined-By: systemd Support: https://access.redhat.com/support
An ExecStartPre= process belonging to unit mariadb.service has exited.
The process' exit code is 'exited' and its exit status is 0. 
mariadb.service: Running next control command for state start-pre. 
mariadb.service: Will spawn child (service_run_next_control): /usr/libexec/mariadb-prepare-db-dir 
mariadb.service: About to execute /usr/libexec/mariadb-prepare-db-dir mariadb.service 
mariadb.service: Forked /usr/libexec/mariadb-prepare-db-dir as 2625 ]: PR_SET_MM_ARG_START failed: Operation not permitted 
mariadb.service: User lookup succeeded: uid=27 gid=27 ]: Bind-mounting / on /run/systemd/unit-root (MS_BIND|MS_REC "")... ]: Applying namespace mount on /run/systemd/unit-root/run/credentials ]: Bind-mounting /run/systemd/inaccessible/dir on /run/systemd/unit-root/run/credentials (MS_BIND|MS_REC "")... ]: Successfully mounted /run/systemd/inaccessible/dir to /run/systemd/unit-root/run/credentials ]: Applying namespace mount on /run/systemd/unit-root/run/systemd/incoming ]: Followed source symlinks /run/systemd/propagate/mariadb.service → /run/systemd/propagate/mariadb.service. ]: Bind-mounting /run/systemd/propagate/mariadb.service on /run/systemd/unit-root/run/systemd/incoming (MS_BIND "")... ]: Successfully mounted /run/systemd/propagate/mariadb.service to /run/systemd/unit-root/run/systemd/incoming ]: Applying namespace mount on /run/systemd/unit-root/tmp ]: Bind-mounting /tmp/systemd-private-682f1dabf7c349f5a364cf8c7a3bf2dd-mariadb.service-BRHkau/tmp on /run/systemd/unit-root/tmp (MS_BIND|MS_REC "")... ]: Successfully mounted /tmp/systemd-private-682f1dabf7c349f5a364cf8c7a3bf2dd-mariadb.service-BRHkau/tmp to /run/systemd/unit-root/tmp ]: Applying namespace mount on /run/systemd/unit-root/var/tmp ]: Bind-mounting /var/tmp/systemd-private-682f1dabf7c349f5a364cf8c7a3bf2dd-mariadb.service-Vz0aIB/tmp on /run/systemd/unit-root/var/tmp (MS_BIND|MS_REC "")... ]: Successfully mounted /var/tmp/systemd-private-682f1dabf7c349f5a364cf8c7a3bf2dd-mariadb.service-Vz0aIB/tmp to /run/systemd/unit-root/var/tmp ]: Remounted /run/systemd/unit-root/run/credentials. ]: Remounted /run/systemd/unit-root/run/systemd/incoming. ]: Remounted /run/systemd/unit-root/run/credentials. ]: 
mariadb.service: Executing: /usr/libexec/mariadb-prepare-db-dir mariadb.service are-db-dir[2625]: Database MariaDB is not initialized, but the directory /var/lib/mysql is not empty, so initialization cannot be done. are-db-dir[2625]: Make sure the /var/lib/mysql is empty before running mariadb-prepare-db-dir. 
mariadb.service: Child 2625 belongs to mariadb.service. 
mariadb.service: Control process exited, code=exited, status=1/FAILURE Subject: Unit process exited Defined-By: systemd Support: https://access.redhat.com/support
An ExecStartPre= process belonging to unit mariadb.service has exited.
The process' exit code is 'exited' and its exit status is 1. 
mariadb.service: Got final SIGCHLD for state start-pre. 
mariadb.service: Failed with result 'exit-code'. Subject: Unit failed Defined-By: systemd Support: https://access.redhat.com/support
The unit mariadb.service has entered the 'failed' state with result 'exit-code'. 
mariadb.service: Service will not restart (restart setting) 
mariadb.service: Changed start-pre -> failed 
mariadb.service: Job 243 mariadb.service/start finished, result=failed Failed to start MariaDB 10.5 database server. Subject: A start job for unit mariadb.service has failed Defined-By: systemd Support: https://access.redhat.com/support
A start job for unit mariadb.service has finished with a failure.
The job identifier is 243 and the job result is failed. Aug 12 17:48:08 stdb01.stratos.xfusioncorp.com systemd[1]: 
mariadb.service: Unit entered failed state.

```

