ansible-acmetool
================

Installs [acmetool](https://github.com/hlandau/acme) and gets Let's Encrypt certificates (to /var/lib/acme/live).

acmetool is used in proxy mode for apache, and stateless mode for nginx
( ref. [acmetool's user's guide](https://hlandau.github.io/acme/userguide#challenge-completion-philosophy) )


Requirements
------------
- OS: ubuntu 14.04 (it may work on others but not tested yet )
- web server: apache or nginx already installed and the non-encrypted site config working.

Example Playbook
----------------

    - hosts: servers
      roles:
        - { role: "ansible-acmetool", become: "yes" }
      vars:
        acmetool_websrv: "nginx"
        acmetool_responses_email: "name@example.com"
        acmetool_want: "www.example.com"    

This will install acmetool to validate using nginx stateless mode, and then will run `acmetool want www.example.com` to get the certs and private key for the site `www.example.com`, which will be available to use by the web server at `/var/lib/acme/live/www.example.com`

Notes:
-----

1) This ansible role only deals with installing and running acmetool and getting the certs. You need to separately configure the web server (apache or nginx) to enable https and to use the obtained certificates.

2) When used to work with nginx stateless mode, the role will pause to allow the user to edit the nginx site configuration to include a file containing a location for the stateless ACME challenge (this is done as we haven't found a reliable way to modify the site configuration for all the possible cases). Just add `include /etc/nginx/acmetool-location.conf;` inside the server block along the other location definitions:

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

3) In case multiple certs are required define `acmetool_want` as a list (one list item per cert). In case a cert is required for multiple sites, create one item with the site names separated by a space. Example:

      acmetool_want:
        - "www.example.com example.com"
        - "www.mysite.com"

This will get a cert valid for www.example.com and example.com and another cert valid for www.mysite.com

License
-------

BSD

Author Information
------------------

Artefactual Systems
