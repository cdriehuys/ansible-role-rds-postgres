cdriehuys.rds-postgres
======================

This role allows you to create a PostgreSQL database using AWS' RDS.

Requirements
------------

Since this role interacts with AWS, it requires the boto package be installed. The features in this role rely on `boto >= 2.26` being installed on the host the role runs on. We also requires `psycopg2` to interact with the database.

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

By default, the RDS instance is created with a security group that allows access from anywhere for the duration of the role. After the role, the security group doesn't allow any access by default. If the desired rules are not known at run time, we save a reference to the created security group as `db_security_group`.

```YAML
db_instance_sg_name: "{{ aws_application_name }}-db-sg"
db_instance_sg_description: Security group for '{{ aws_application_name }}' databases.

# Override this with your desired rules
db_instance_sg_rules: []

# These are the rules that allow access when the role is running
db_instance_sg_holes:
  - proto: tcp
    from_port: 5432
    to_port: 5432
    cidr_ip: 0.0.0.0/0
```

Each instance can host multiple databases. Databases are specified with a name and optional parameters. The users given for each database will have ALL privileges on the database.

```YAML
db_map: {}

# An example of multiple databases with multiple users
db_map:
  mydb:
    users:
      john: johnpassword
      alice: alicepassword

  otherdb:
    users:
      phil: philpassword
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
