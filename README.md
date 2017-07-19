cdriehuys.rds-postgres
======================

This role allows you to create a PostgreSQL database using AWS' RDS.

Requirements
------------

Since this role interacts with AWS, it requires the boto package be installed. The features in this role rely on `boto >= 2.26` being installed on the host the role runs on.

Role Variables
--------------

This role interacts with AWS, so you must set your credentials to use the role. If credentials are not provided as variables, Ansible will attempt to look them up from environment variables as described [here](http://docs.ansible.com/ansible/rds_module.html#notes).

```YAML
aws_access_key: your-access-key
aws_secret_key: your-secret-key
aws_region: your-aws-region
```

The role uses a few common AWS variables in order to provide default names for other parameters.

```YAML
aws_application_name: my-application
aws_tags: {}
```

The following parameters are used when creating the database instance. You will most likely want to change these parameters as they default to the smallest values possible.

```YAML
db_instance_name: "{{ aws_application_name }}-db"
db_instance_size: 5
db_instance_type: db.t2.micro

db_instance_default_tags:
  application: "{{ aws_application_name }}"
  role: database
db_instance_tags: "{{ db_instance_default_tags | combine(aws_tags) }}"

db_instance_subnet: default

# Credentials for the admin user
db_admin_username: dbadmin
db_admin_password: password
```

Dependencies
------------

N/A

Example Playbook
----------------

Since the role uses `local_action` commands, it makes the most sense to
set the host to the local machine.

    - hosts: localhost
      connection: local
      gather_facts: no
      roles:
         - cdriehuys.rds-postgres

License
-------

MIT

Author Information
------------------

Chathan Driehuys <cdriehuys@gmail.com>
