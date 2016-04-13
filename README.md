Role Name
=========

Installs letsencrypt certs on a server using acmetool (installed by this role).
acmetool is used in proxy mode ( see https://hlandau.github.io/acme/userguide#challenge-completion-philosophy )

Requirements
------------

Currently apache as web server

Role Variables
--------------

TBD

Dependencies
------------

none

Example Playbook
----------------

    - hosts: servers
      roles:
        - { role: "ansible-letsencrypt", become: "yes" }
      vars:
        letsencrypt_acmetool_email: "name@example.com"
        letsencrypt_acmetool_want: "www.example.com"    

License
-------

BSD

Author Information
------------------

Artefactual Systems
