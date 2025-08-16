## Linux Network Services

### Task

Our monitoring tool has reported an issue in Stratos Datacenter. One of our app servers has an issue, as its Apache service is 
not reachable on port 6000 (which is our Apache port). The service itself could be down, the firewall could be at fault, or 
something else could be causing the issue.

Use tools like telnet, netstat, etc. to find and fix the issue. Also make sure Apache is reachable from the jump host without 
compromising any security settings.

---

### Solution

1. Check which app server has the issue.

Use curl command to check which app server has the issue.

```sh
curl http://stapp01:6000
curl http://stapp02:6000
curl http://stapp03:6000

# in my case the app server 02 had the issue.

# use telnet to check the reachability

telnet stapp02 6000 # this was timing out other servers had no issue.

```
> [!IMPORTANT]
> Assumptions:
>   - Routing hasn't configured properly
>   - Apache server has an issue

---

2. Troubleshooting apache server.

```sh

# login to the app server
ssh steve@stapp02
sudo -i

# check the apache server status
systemctl status httpd
# the service status was failed
# logs indicated the address is already in use (stapp02:6000)

netstat -anp | grep 6000
# -> A process was listening on this port
# kill the process
kill -9 <pid>

# restart the httpd service
systemctl restart httpd
systemctl status httpd
# -> active

# the app server is running need to check the routing
firewall-cmd --state # firewall wasn't running in any of the servers

iptables -L
iptables -L INPUT -nv
# other servers had a rule to allow ingress trafic for port 6000
iptables -I INPUT -p tcp --dport 6000 -j ACCEPT
service iptables save

```

---

3. Ensure the connectivity.

Check the connectivity from the jomphost `curl http://stapp02:6000`.
