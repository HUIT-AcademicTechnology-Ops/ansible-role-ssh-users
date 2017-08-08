Ansible Role: SSH users
=========

This role will create unix users with groups and owned directories as specified by the input list. It will also manage authorized_ssh_keys for these users that are chosen from a list of common organization users' public ssh credentials which are retrieved from github.  A list of unix users with optional directories and github usernames is the expected input to the role.

Some assumptions are made to simplify the number of role variables required for this role:
- a group will be created for each ssh user with the same name as the user name
- the login shell will default to /bin/bash and can only be overriden at the list level, and not per user
- the set of public ssh authorized keys will be pulled from github urls, so users are expected to have stored their public keys in their account
- no ssh connection parameters are allowed per unix user


Requirements
------------
* Ansible 2.1+


Role Variables
--------------

| parameter                     | required | default    | comments                                                            |
|-------------------------------|----------|------------|---------------------------------------------------------------------|
| ssh_users                     | yes      |            | A list of unix users to create, with optional sublists              |
| ssh_users_github_domain       | no       | github.com | The domain name of the github server targeted for public ssh keys   |
| ssh_users_login_shell         | no       | /bin/bash  | The login shell for each unix user created on the remote machine    |


The `ssh_users` input list should look like the following:
```
    ssh_users:
      - name: adminUser
          github_authorized_keys:
            - githubUsername1
            - githubUsername2
          dirs:
            - /path/to/dir
            - /another/dir
        - name: deployUser
          github_authorized_keys:
            - githubUsername1
            - githubUsername3
            - githubUsername4
```

The `github_authorized_keys` sublist should refer to github usernames.  The role will attempt to pull down the public keys for that user from a github url (could be a github enterprise url).  This sublist is technically optional.  Also optional is a list of `dirs` that will be created and owned by the unix user specified by the `name` key for each item in the list.

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
              github_authorized_keys:
                - bermudezjd
            - name: deploy
              github_authorized_keys:
                - bermudezjd
                - jenkins
              dirs:
                - /opt/tlt
      roles:
         - { role: ssh-users }
```

License
-------

MIT

Author Information
------------------

Jaime Bermudez (jaime_bermudez@harvard.edu)
