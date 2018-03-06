# Ansible role: nginx

[![Build Status](https://travis-ci.org/jodyboucher/ansible-role-nginx.svg?branch=master)](https://travis-ci.org/jodyboucher/ansible-role-nginx)

An [Ansible](https://www.ansible.com/) role that installs and configures nginx web server.

This role is designed for Ubuntu 16.04 Xenial.

## Requirements

* [Ansible](https://docs.ansible.com/ansible/intro_installation.html) >= 2.4 (makes use of new import_tasks module)
* This role requires `root` access so be sure to enable privilege escalation:

```
# privilege escalation of play
- hosts: webservers
  become: true
  roles:
    -role: nginx

# escalation of single role only
- hosts: webservers
  roles:
    - role: nginx
      become: true
```

## Role Variables

The available variables of this role are listed here along with default values:
```
# The user under which nginx will run
nginx_user: www-data

# Top-level directory of nginx configuration
nginx_path_config: /etc/nginx

# Path of the main nginx configuration file
nginx_path_conf_file: /etc/nginx/nginx.conf

# Directory containing additional configuration settings to load
nginx_path_conf_directory: /etc/nginx/conf.d

# Path of the file contained MIME type definitions
nginx_path_mime_types_file: /etc/nginx/mime.types

# Path of the file holding the nginx main process ID
nginx_pidfile: /run/nginx.pid

# Directory containing SSL configuration
nginx_path_ssl_conf: /etc/nginx/ssl

# Path of the common (shared across sites) SSL configuration
nginx_path_ssl_common_conf_file: /etc/nginx/ssl/ssl-common.conf

# Directory containing per site (vhost) configuration
nginx_path_vhosts_conf: /etc/nginx/sites-available

# Directory containing symlinks to enabled site configurations
nginx_path_vhosts: /etc/nginx/sites-enabled
```

Additional variables are listed below, along with default values (see `defaults/main.yml`):
```
---
# Use the official Nginx PPA for Ubuntu, and the version to use if so.
nginx_ppa_enable: true
nginx_ppa_version: stable

nginx_enable_geoip: true

nginx_log_format: |
  '$remote_addr - $remote_user [$time_local] "$request" '
  '$status $body_bytes_sent "$http_referer" '
  '"$http_user_agent" "$http_x_forwarded_for" '
nginx_log_format_geoip: |
  {{ vars["nginx_log_format"]}}
  '$geoip_country_name $geoip_country_code '
  '$geoip_region_name $geoip_city '

# core module settings
nginx_core_worker_processes: auto
nginx_core_worker_connections: 1024
nginx_core_multi_accept: "off"
nginx_core_error_log: "/var/log/nginx/error.log warn"

# Any additional core module settings
nginx_core_extra: ""
# Example extra core module (main context) settings:
#   nginx_core_extra: |
#     env VARIABLE;
#     worker_processes 4;

# http module settings
nginx_http_server_tokens: "off"
nginx_http_access_log: /var/log/nginx/access.log
nginx_http_keepalive_timeout: 20s
nginx_http_sendfile: "on"
nginx_http_tcp_nopush: "on"
nginx_http_client_max_body_size: "4m"
nginx_http_gzip: "on"
nginx_http_gzip_comp_level: 5
nginx_http_gzip_min_length: 256
nginx_http_gzip_proxied: any
nginx_http_gzip_vary: "on"
nginx_http_gzip_types: |
    application/atom+xml
    application/javascript
    application/json
    application/ld+json
    application/manifest+json
    application/rss+xml
    application/vnd.geo+json
    application/vnd.ms-fontobject
    application/x-font-ttf
    application/x-web-app-manifest+json
    application/xhtml+xml
    application/xml
    font/opentype
    image/bmp
    image/svg+xml
    image/x-icon
    text/cache-manifest
    text/css
    text/plain
    text/vcard
    text/vnd.rim.location.xloc
    text/vtt
    text/x-component
    text/x-cross-domain-policy;

# Enable FastCGI caching
nginx_http_fastcgi_cache_enable: false

# Array of http upstream module settings
nginx_http_upstreams: []
# - name: backend
#   strategy: "ip_hash"
#   keepalive: 16
#   servers: {
#     "backend1.example.com weight=5;",
#     "backend2.example.com:8080",
#     "unix:/tmp/backend3"
#   }

# Any additional http module settings
nginx_http_extra: ""
# Example extra http options:
#    nginx_http_extra: |
#      proxy_buffering    off;
#      proxy_set_header   X-Real-IP $remote_addr;
#      proxy_set_header   X-Scheme $scheme;
#      proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
#      proxy_set_header   Host $http_host;

# Any sites (vhosts) to configure
nginx_vhosts: {}
#  example.com:
#    site_root: /var/www/example_com
#    enable_ssl: true
#    index: index.php
#    extra: |
#      location ~ \.php$ {
#        fastcgi_split_path_info ^(.+\.php)(/.+)$;
#        fastcgi_pass unix:/var/run/php5-fpm.sock;
#        fastcgi_index index.php;
#        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
#        include fastcgi_params;
#      }
#  api.example.com:
#    site_root: /var/www/api_example_com
#    enable_ssl: true
#    is_api: true
#    upstream_name: api

```

## Dependencies

None.

## Example Playbook

```
---
- hosts: webservers
  become: true
  vars_files:
    - vars/main.yml
  roles:
    - { role: nginx }
```

Inside `vars/main.yml`:

```
---
nginx_user: www-data

nginx_http_upstreams:
  - name: php
    servers: {
      "unix:/run/php/php7.0-fpm.sock"
    }
```

## Installation

On the command-line:
```
$ ansible-galaxy install git+https://github.com/jodyboucher/ansible-role-nginx.git
```

or in a role file (requirements.yml):

```
- name: nginx
  src: https://github.com/jodyboucher/ansible-role-nginx
  version: master
```

## License

MIT

## Author Information

This role was created by [Jody Boucher](https://jodyboucher.com/).
