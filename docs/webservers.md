---
layout: page
title: Webservers
permalink: /docs/webservers/
---

The following webservers have been tested:

## Apache + mod_php

```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        ServerName mintyphp.dev

        DocumentRoot /home/maurits/public_html/mintyphp/web
        <Directory /home/maurits/public_html/mintyphp/web>
                AllowOverride All
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

Benchmarks:

```
ab -n 2000 -c 10 http://mintyphp.dev/hello/world
Requests per second:    3948.10 [#/sec] (mean)
```

## Nginx + php-fpm

Install PHP-FPM:

```
sudo apt-get install php-fpm
```

Configure Nginx:

```
$ cat /etc/nginx/sites-enabled/mintyphp
server {
    listen 80;
    server_name mintyphp.dev;

    root /home/maurits/public_html/mintyphp/web;
    try_files $uri @proxy;

    location ~ \.php$ {
        return 403;
    }

    location ~ ^/debugger/$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $document_root/debugger/index.php;
        include fastcgi_params;
    }

    location @proxy {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $document_root/index.php;
        include fastcgi_params;
    }

}
```

Benchmarks:

```
ab -n 2000 -c 10 http://mintyphp.dev/hello/world
Requests per second:    2836.15 [#/sec] (mean)
```
