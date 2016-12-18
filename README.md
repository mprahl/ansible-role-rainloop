# rainloop

An Ansible role that automates the installation and updating of [RainLoop](https://www.rainloop.net) on Ubuntu.

## Role Variables

* **rainloop_ensure_latest** - determines whether or not to update RainLoop when there is a newer version of RainLoop available. This defaults to `false`.
* **rainloop_dir** - the path of to where RainLoop should be installed. This defaults to `/var/www/rainloop`.

## Requirements

This role must be run with sudo/become or as root, otherwise the role will fail.

## Dependencies

This role depends on two roles from Ansible Galaxy:
* [geerlingguy.apache](https://github.com/geerlingguy/ansible-role-apache)
* [geerlingguy.php](https://github.com/geerlingguy/ansible-role-mysql)

There are several variables that must be set on these roles for the rainloop role to work properly.
Please look at the example playbooks below to see which ones are needed.

## Example Playbook

HTTP deployment:

```yaml
- name: Deploy RainLoop
  hosts: mailserver
  become: yes

  vars:
  - rainloop_ensure_latest: true
  - apache_remove_default_vhost: true
  - apache_vhosts:
    - servername: domain.example.com
      documentroot: /var/www/rainloop

  - php_packages:
    - php7.0
    - php7.0-cli
    - php7.0-common
    - php7.0-curl
    - php7.0-gd
    - php7.0-curl
    - php7.0-json
    - php7.0-mysql
    - php7.0-xml
    - libapache2-mod-php7.0

  roles:
   - mprahl.rainloop
```

HTTPS deployment:

```yaml
- name: Deploy RainLoop
  hosts: mailserver
  become: yes

  vars:
  - rainloop_ensure_latest: true
  - apache_remove_default_vhost: true
  - apache_vhosts:
    - servername: domain.example.com
      documentroot: /var/www/html
      extra_parameters: |
        RewriteEngine On
        RewriteCond %{HTTPS} off
        RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
  - apache_vhosts_ssl:
    - servername: domain.example.com
      documentroot: /var/www/rainloop
      certificate_file: /path/to/certificate
      certificate_key_file: /path/to/key
      certificate_chain_file: /path/to/chain

  - php_packages:
    - php7.0
    - php7.0-cli
    - php7.0-common
    - php7.0-curl
    - php7.0-gd
    - php7.0-curl
    - php7.0-json
    - php7.0-mysql
    - php7.0-xml
    - libapache2-mod-php7.0

  roles:
   - mprahl.rainloop
```
