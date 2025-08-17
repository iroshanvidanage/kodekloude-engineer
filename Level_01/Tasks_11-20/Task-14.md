# Linux Process Troubleshooting

### Task

The production support team of xFusionCorp Industries has deployed some of the latest monitoring tools to keep an eye on every service, application, etc. running on the systems. One of the monitoring systems reported about Apache service unavailability on one of the app servers in Stratos DC.



Identify the faulty app host and fix the issue. Make sure Apache service is up and running on all app hosts. They might not have hosted any code yet on these servers, so you don’t need to worry if Apache isn’t serving any pages. Just make sure the service is up and running. Also, make sure Apache is running on port 5004 on all app servers.

---

### Solution

1. Check reachability from JumpHost

```sh
curl http://stapp01:5004 # faulty in my task
curl http://stapp02:5004
curl http://stapp03:5004

```

2. Troubleshoot the app server.

```sh

# login to server and switch to root
# check for service status
systemctl status httpd
# ->
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:httpd(8)
           man:apachectl(8)


systemctl start httpd
# ->
#Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.

systemctl status httpd
# ->
# (98)Address already in use: AH00072: make_sock: could not bind to addre...:5004

# in my case the port 5004 was bounded with another process
netstat -anp | grep 5004
# ->
# tcp        0      0 127.0.0.1:5004          0.0.0.0:*               LISTEN      649/sendmail: accep

# stopped the process
kill -9 649

# restart the service
systemctl restart httpd
systemctl status httpd

```

3. Check reachability from JumpHost

```sh
curl http://stapp01:5004
```
