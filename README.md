ansible-acmetool
================

Installs [acmetool](https://github.com/hlandau/acme) and gets Let's Encrypt certificates (to /var/lib/acme/live).

acmetool is used in proxy mode for apache, and stateless mode for nginx
( ref. [acmetool's user's guide](https://hlandau.github.io/acme/userguide#challenge-completion-philosophy) )


Requirements
------------
- OS: ubuntu 14.04/16.04, Red Hat/CentOS 7
- web server: apache or nginx

Example Playbook
----------------
```yaml
    - hosts: servers
      roles:
        - { role: "ansible-acmetool", become: "yes" }
      vars:
        acmetool_websrv: "nginx"
        acmetool_responses_email: "name@example.com"
        acmetool_want: "www.example.com"    
```

This will install acmetool to validate using nginx stateless mode, and then will run `acmetool want www.example.com` to get the certs and private key for the site `www.example.com`, which will be available to use by the web server at `/var/lib/acme/live/www.example.com`

Notes:
-----

1) This ansible role only deals with installing and running acmetool and getting the certs. You need to separately configure your web server to enable https for your site and to use the obtained certificates.

2) In case multiple certs are required define `acmetool_want` as a list (one list item per cert). In case a cert is required for multiple sites, create one item with the site names separated by a space. Example:

    ```yaml
    acmetool_want:
      - "www.example.com example.com"
      - "www.mysite.com"
    ```

This will get a cert valid for www.example.com and example.com and another cert valid for www.mysite.com

Notes for nginx stateless mode:
------------------------------

1) This role will add and temporarily enable configuration for a site to get the initial LE certificate (the site is disabled by the role after getting the certs, the configuration file is kept (disabled) for reference in the directory `/etc/nginx/conf.d/`)

2) The role creates file `/etc/nginx/acmetool-location.conf`, with configuration for a stateless ACME challenge location. Include this file in your site configuration, inside the server block along the other location definitions, in order for automated cert renewal to work, for example like this:

```
server {
        listen 80;

        root /usr/share/nginx/html;
        index index.html;
        server_name www.example.com;

        include /etc/nginx/acmetool-location.conf;    # ===> ADD THIS

        location / {
                try_files $uri $uri/ =404;
        }
...
...
```


License
-------

BSD

Author Information
------------------

Artefactual Systems
