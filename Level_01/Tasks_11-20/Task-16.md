# Install and Configure Nginx as an LBR

### Task

Day by day traffic is increasing on one of the websites managed by the Nautilus production support team. Therefore, the team has observed a degradation in website performance. Following discussions about this issue, the team has decided to deploy this application on a high availability stack i.e on Nautilus infra in Stratos DC. They started the migration last month and it is almost done, as only the LBR server configuration is pending. Configure LBR server as per the information given below:



a. Install nginx on LBR (load balancer) server.


b. Configure load-balancing with the an http context making use of all App Servers. Ensure that you update only the main Nginx configuration file located at /etc/nginx/nginx.conf.


c. Make sure you do not update the apache port that is already defined in the apache configuration on all app servers, also make sure apache service is up and running on all app servers.


d. Once done, you can access the website using StaticApp button on the top bar.

---

### Solution

1. Install nginx in `stlb01` server.

```bash

yum install -y nginx

```

2. Set the following configurations in the `nginx.conf`.

```bash

# include the following block inside http{}

http {
    upstream backend_servers {
        server stapp01:6000;
        server stapp02:6000;
        server stapp03:6000;
    }

    # ... other http configurations
}

```

Configure the `nginx.conf` as follows

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

    upstream backend_servers {
        server stapp01:6000;
        server stapp02:6000;
        server stapp03:6000;
    }

    server {
        listen       80;
        listen       [::]:80;
        server_name  _;

        location / {
            proxy_pass http://backend_servers;
        }

        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }

# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2;
#        listen       [::]:443 ssl http2;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers PROFILE=SYSTEM;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }

}

```

Validate the nginx config.

```bash

nginx -t

```

3. Test spp server 6000 reachability

```bash

thor@jumphost ~$ curl http://stapp01:6000
Welcome to xFusionCorp Industries!thor@jumphost ~$ 
thor@jumphost ~$ 
thor@jumphost ~$ 
thor@jumphost ~$ curl http://stapp02:6000
Welcome to xFusionCorp Industries!thor@jumphost ~$ 
thor@jumphost ~$ 
thor@jumphost ~$ 
thor@jumphost ~$ 
thor@jumphost ~$ 
thor@jumphost ~$ curl http://stapp03:6000
Welcome to xFusionCorp Industries!thor@jumphost ~$ 
thor@jumphost ~$ 

```

4. Click on the `StaticApp` button to test the LBR.
