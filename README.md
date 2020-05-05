# Ansible-Bindproxy
Ansible playbook for updating bind proxy configurations for multiple servers at once

## Variables

    ldapmod_path - Path of ldapmodify if not using ldap_attr module [/opt/symas/bin/ldapmodify]
    server_uri - URI of slapd instance [ldapi://%2Fvar%2Fsymas%2Frun%2Fldapi]
    bind_dn - Bind DN of cn=config [Defaults to bindproxy_dn extra variable]
    bind_pw - Bind PW of cn=config [Defaults to bindproxy_pw extra variable]

## Instructions

There are 2 ways to run this playbook:

If the python-ldap dependency is able to be installed on all the target servers it is preferrable to run the playbook with the default task. This is because the ldap_attr module only makes changes when necessary.

If python-ldap is not available, you can default to using ldapmodify to update cn=config. Note you will need to modify tasks/update_targets by uncommenting out the ldapmodify portion and commenting the ldap_attr portion.

The playbook is dependent on passing targets as extra variables. This can be done either through json or yml but the yaml format is as follows:

    servers:
      - target1:
          - dn: olcDatabase={5}ldap,cn=config
            suffix: ou=test,local
            urls:
              - 127.0.0.1
              - 10.0.0.1
      - target2: ...

Note that the suffix is not required, it is simply there as a reference to differentiate each target. Each target corresponds to a server name. This configuration assumes all instances share the same server uri. If a server requires multiple changes, you can simply list them again:

    servers:
      - target1:
          - dn: olcDatabase={5}ldap,cn=config
            suffix: ou=test,local
            urls:
              - 127.0.0.1
              - 10.0.0.1
          - dn: olcDatabase={6}ldap,cn=config
            suffix: ou=test2,local
            urls:
              - 127.0.0.8
              - 10.0.0.2

Any urls listed will be concatenated into a single string which will be added to the dn entry specifics for each target. Also, the target names should match the hostname of the inventory targets.
