Ansible Role: Common SSH users
=========

This role will create unix users with groups and owned directories as specified by the input list. It will also manage authorized_ssh_keys for these users that are chosen from a list of common organization users' public ssh credentials which are stored in the `files` directory of the role.  A list of unix users with optional directories and ssh_key file references is the expected input to the role.

Some assumptions are made to simplify the number of role variables required for this role:
- a group will be created for each user with the same name as the user name
- the login shell will default to /bin/bash and can only be overriden at the list level, and not per user
- the set of public ssh authorized keys will be pulled from a file managed with this role, as opposed to allowing keys from github urls or other sources
- no ssh connection parameters are allowed per unix user


Requirements
------------

* Ansible 2.1+


Role Variables
--------------

| parameter                     | required | default    | comments                                                            |
|-------------------------------|----------|------------|---------------------------------------------------------------------|
| ssh_users                     | yes      |            | A list of unix users to create, with optional sublists              |
| common_ssh_user_login_shell   | no       | /bin/bash  | The login shell for each unix user created on the remote machine    |


The `ssh_users` input list should look like the following:
    ssh_users:
        - name: adminUser
          authorized_keys:
            - orgUser1.pub
            - orgUser2.pub
          dirs:
            - /path/to/dir
            - /another/dir
        - name: deployUser
          authorized_keys:
            - orgUser1.pub
            - orgUser3.pub
            - orgUser4.pub

The `authorized_keys` sublist should refer to file names present in the `files` directory of this role.  These files should contain the public ssh/rsa key(s) for the organization user.  It's technically optional.  Also optional is a list of `dirs` that will be created and owned by the unix user specified by the `name` key for each item in the list.

Dependencies
------------

N/A

Example Playbook
----------------

```
    - hosts: remoteHost
      become: true
      vars:
        ssh_users:
            - name: ubuntu
              authorized_keys:
                - jaime_bermudez.pub
            - name: deploy
              authorized_keys:
                - jaime_bermudez.pub
                - jenkins.pub
              dirs:
                - /opt/tlt
      roles:
         - { role: common-ssh-users }
```

License
-------

MIT

Author Information
------------------

Jaime Bermudez (jaime_bermudez@harvard.edu)
