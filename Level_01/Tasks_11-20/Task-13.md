# IPtables Installation And Configuration

### Task

We have one of our websites up and running on our Nautilus infrastructure in Stratos DC. Our security team has raised a concern that right now Apache’s port i.e 8084 is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with the following requirements:

    1. Install iptables and all its dependencies on each app host.
    2. Block incoming port 8084 on all apps for everyone except for LBR host.
    3. Make sure the rules remain, even after system reboot.

---

### Solution

1. Check reachability from JumpHost and LBR

```sh
curl http://stapp01:8084
curl http://stapp02:8084
curl http://stapp03:8084
```

2. Install and Configure iptables in each app server

```sh
# login to each app server and switch to root user
sudo -i

# check for iptables and firewalls
systemctl status iptables
systemctl status firewalls

```

Install iptables and dependencies

```sh
# install iptables
yum install iptables-services -y

# enable and start the service
systemctl enable --now iptables

```

Configure the iptables rules

> [!IMPORTANT]
> Here the rule #5 is rejecting all the access, any rules appended to the chain after the rule #5 will be ignored.
> - 0     0 REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

```sh

# list the rules
iptables -L
iptables -L INPUT -nv

# replace rule #5 and append the last rule
iptables -R INPUT 5 -p tcp --dport 8084 -s 172.16.238.14 -j ACCEPT
iptables -A INPUT -p tcp --dport 8084 -j DROP

# save for reboots
service iptables save

```

3. Check the connectivity from JumpHost and LBR

- Only the LBR connectivity should work, JumpHost curls will be timed-out.
