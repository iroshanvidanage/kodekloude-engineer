# Setup SSL for Nginx

### Task

The system admins team of xFusionCorp Industries needs to deploy a new application on App Server 3 in Stratos Datacenter. They have some pre-requites to get ready that server for application deployment. Prepare the server as per requirements shared below:

1. Install and configure nginx on App Server 3.

2. On App Server 3 there is a self signed SSL certificate and key present at location /tmp/nautilus.crt and /tmp/nautilus.key. Move them to some appropriate location and deploy the same in Nginx.

3. Create an index.html file with content Welcome! under Nginx document root.

4. For final testing try to access the App Server 3 link (either hostname or IP) from jump host using curl command. For example curl -Ik https://<app-server-ip>/.

---

### Solution

`nginx.conf` file.

```bash
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    # server {
    #     listen       80;
    #     listen       [::]:80;
    #     server_name  _;
    #     root         /usr/share/nginx/html;

    #     # Load configuration files for the default server block.
    #     include /etc/nginx/default.d/*.conf;

    #     error_page 404 /404.html;
    #     location = /404.html {
    #     }

    #     error_page 500 502 503 504 /50x.html;
    #     location = /50x.html {
    #     }
    # }
    server {
        listen 80;
        listen [::]:80;
        server_name _;

        return 301 https://$host$request_uri;
    }

    server {
       listen       443 ssl http2;
       listen       [::]:443 ssl http2;
       server_name  _;
       root         /usr/share/nginx/html;

       ssl_certificate "/etc/pki/nginx/nautilus.crt";
       ssl_certificate_key "/etc/pki/nginx/private/nautilus.key";
       ssl_session_cache shared:SSL:1m;
       ssl_session_timeout  10m;
       ssl_ciphers PROFILE=SYSTEM;
       ssl_prefer_server_ciphers on;

       # Load configuration files for the default server block.
       include /etc/nginx/default.d/*.conf;

       error_page 404 /404.html;
           location = /40x.html {
       }

       error_page 500 502 503 504 /50x.html;
           location = /50x.html {
       }
    }

}

```

`index.html` file.

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome!</title>
</head>
<body>
    <header>
        <h1>Welcome!</h1>
    </header>

    <p>Welcome!</p>
    
</body>
</html>

```

Terminal workflow.

```bash
# install nginx and start
[root@stapp03 ~]# yum install nginx -y
[root@stapp03 ~]# systemctl start nginx
[root@stapp03 ~]# systemctl status nginx
```

```bash
# install net-tools to use netstat command (optional)
[root@stapp03 ~]# yum install net-tools -y
```

```bash
# place the crt file and key files in appropriate locations
[root@stapp03 ~]# mkdir -p /etc/pki/nginx/private/
[root@stapp03 ~]# 
[root@stapp03 ~]# 
[root@stapp03 ~]# mv /tmp/nautilus.crt /etc/pki/nginx/
[root@stapp03 ~]# mv /tmp/nautilus.key /etc/pki/nginx/private/
[root@stapp03 ~]# 
[root@stapp03 ~]# 
[root@stapp03 ~]# 
[root@stapp03 ~]# chmod 755 /etc/pki/nginx/nautilus.crt 
[root@stapp03 ~]# chmod 755 /etc/pki/nginx/private/nautilus.key 
[root@stapp03 ~]# 
[root@stapp03 ~]# 
[root@stapp03 ~]# chgrp nginx /etc/pki/nginx
[root@stapp03 ~]# 
```

```bash
# edit the nginx.conf file
[root@stapp03 ~]# cd /etc/nginx/
[root@stapp03 nginx]# vi nginx.conf
[root@stapp03 nginx]# 
```

```bash
[root@stapp03 nginx]# systemctl reload nginx
[root@stapp03 nginx]# systemctl status nginx
[root@stapp03 nginx]# netstat -anp|grep nginx
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      2439/nginx: master  
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      2439/nginx: master  
tcp6       0      0 :::443                  :::*                    LISTEN      2439/nginx: master  
tcp6       0      0 :::80                   :::*                    LISTEN      2439/nginx: master  
```

```bash
# create the index.html file and restart the nginx.service
[root@stapp03 nginx]# cd /usr/share/nginx/html/
[root@stapp03 html]# ls
404.html  50x.html  icons  index.html  nginx-logo.png  poweredby.png  system_noindex_logo.png
[root@stapp03 html]# 
[root@stapp03 html]# 
[root@stapp03 html]# 
[root@stapp03 html]# mv index.html index_old.html 
[root@stapp03 html]# vi index.html
[root@stapp03 html]# systemctl restart nginx

```