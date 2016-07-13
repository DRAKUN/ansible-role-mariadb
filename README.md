# Ansible Role: MariaDB

[![Build Status](https://travis-ci.org/tschifftner/ansible-role-mariadb.svg?branch=master)](https://travis-ci.org/tschifftner/ansible-role-mariadb)

Installs MariaDB on Debian/Ubuntu linux servers.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

### Create database users

_Passwords are required!_

```
mariadb_users:
  - name: 'user1'
    password: '*******'
    priv: '*.*:USAGE'
    hosts:
      - localhost
      - 127.0.0.1

  - name: 'normaltravis'
    password: '*******'
    priv: '*.*:ALL,GRANT'
    hosts:
      - localhost
      - 127.0.0.1
      
  - name: 'old'
    state: absent
    hosts:
      - localhost
      - 127.0.0.1
```

### Create databases

All databases need to be defined in ```mariadb_databases```:

```
mariadb_databases:
  - name: 'testdb'
    collation: utf8_general_ci
    encoding: utf8
```

### Settings user priviledges on databases and tables

To define user priviledges use the following format:
```
db.table:priv1,priv2
```

Idempotent solution for multiple priviledges (@see http://stackoverflow.com/a/22959760)

```
mysql_privileges:
  - db1.*:ALL,GRANT
  - db2.*:USAGE
  
mariadb_users:  
  - name: 'user1'
    password: 'travis'
    priv={{ mysql_privileges|join('/') }}
    hosts:
      - localhost
      - 127.0.0.1
```

### Security

This role sets an administrative user and removes root entirely. Please define the following settings:

```
mariadb_admin_home: '/root'
mariadb_admin_user: 'admin'
mariadb_admin_password: 'Set strong password here!'
```

_When a custom admin username is used, a password must be set!_

## Dependencies

None.

## Example Playbook

    - hosts: server
      roles:
        - { role: tschifftner.mariadb }

## Supported OS

Ansible          | Debian Jessie    | Ubuntu 14.04    | Ubuntu 12.04
:--------------: | :--------------: | :-------------: | :-------------: 
1.9              | Yes              | Yes             | Yes
2.0.1*           | Yes              | Yes             | Yes

*) 2.0.0.0, 2.0.0.1, 2.0.0.2 are not supported!

## Root password lost

[If you cannot login anymore you can reset your credentials.](https://falseisnotnull.wordpress.com/2012/10/31/did-you-lose-your-mariadb-root-password-gnulinux/)

## Remove User
To remove a user define ```state: absent```
```
mariadb_users:
   - name: 'test'
     host: localhost
     password: 'test'
     priv: '*.*:ALL'
     state: 'absent'
```

## License

MIT / BSD

## Author Information

 - [Tobias Schifftner](https://twitter.com/tschifftner), [ambimax® GmbH](https://www.ambimax.de)