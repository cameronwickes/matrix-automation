<h1 align="center">
  <img alt="matrix logo" src="https://element.io/images/logo-mark-primary.svg" width="200px"/><br/>
  Matrix-Automation
</h1>

<p align="center">
  <img alt="Supported Platforms" src="https://img.shields.io/badge/Platform-Ubuntu-blueviolet?color=blue&style=for-the-badge">
  <img alt="Language" src="https://img.shields.io/badge/Language-Ansible-blue?color=blueviolet&style=for-the-badge">
  <img alt="License" src="https://img.shields.io/github/license/cameronwickes/matrix-automation?color=brightgreen&style=for-the-badge">
</p>

<p align="center">
  An <b>Ansible</b> role for automating the installation and configuration of a <b>Matrix Synapse</b> server to create secure organisational communication channels.
  <a href="https://galaxy.ansible.com/cameronwickes/matrix_automation" target="blank">Role</a>
</p>

<br/>

<p>
  <b>Matrix-Automation creates a functioning Synapse Homeserver by configuring four rootless Podman containers:</b>
  <ul>
    <li>A PostgreSQL Database - Storing Synapse Data</li>
    <li>A Synapse Homeserver - Federating Rooms</li>
    <li>A MA1SD Identity Server - Handling 3PID Mappings</li>
    <li>A MA1SD Extender API - Handling MA1SD Corporate Integration</li>
    <li>A Reverse Proxy - Link Connections to Synapse</li>
  </ul>
  
  </br>
  
  It supports LDAP and SSO integrations for login and user search, and can federate with other Synapse homeservers and identity servers. Example playbooks can be seen below.
</p>

## 🪄 Dependencies

Matrix-Automation uses Podman to create rootless containers. The Ansible Podman collection must therefore be installed before the role can be utilised:

`ansible-galaxy collection install containers.podman`

## ⚙️ Role Variables

The following variables are required by Matrix-Automation:

- `synapse_server_name`: The domain name of the server you wish to setup Synapse on.
- `postgres_user`: The username for PostgreSQL container access.
- `postgres_password`: The password for PostgreSQL container access. **Should be secure.**
- `synapse_federation_list`: The list of other owned homeservers to federate room state and identity with.
- `ma1sd_extender_username`: The username for MA1SD extender access.
- `ma1sd_extender_password`: The password for MA1SD extender access.

These variables are necessary to configure Synapse and Ma1sd effectively:

- `synapse_configuration`: Additional configuration settings to go in Synapse's *homeserver.yaml* file.
- `ma1sd_configuration`: Additional configuration settings to go in MA1SD's *ma1sd.yaml* file.

These variables help tweak configuration to user liking:

- `postgres_container_name`: The name to attach to the PostgreSQL container.
- `synapse_container_name`: The name to attach to the Synapse container.
- `https_portal_container_name`: The name to attach to the HTTPS Portal (Reverse Proxy) Container.
- `ma1sd_container_name`: The name to attach to the MA1SD container.
- `ma1sd_extender_container_name`: The name to attach to the MA1SD Extender container.
- `synapse_db_name`: The name of the synapse database within the PostgreSQL container.

## 🗒️ Example Playbook

### Single Server

An example playbook for a single server Synapse setup can be seen below:

```
# Example Single Server Matrix Automation Playbook
# Author: Cameron Wickes
# Date: 18/04/22
---
- hosts: server_one
  roles:
    - "matrix-automation"
  vars:
    postgres_user: synapse
    postgres_password: synapse
    postgres_database: synapse
    ma1sd_extender_username: ma1sd
    ma1sd_extender_password: ma1sd
    synapse_server_name: example.org
    synapse_federation_list: []
    synapse_configuration: |
      password_providers:
        - module: "ldap_auth_provider.LdapAuthProvider"
          config:
            enabled: true
            mode: "search"
            uri: "ldap://ldap.example.org:389"
            start_tls: false
            base: "ou=employees,dc=example,dc=org"
            attributes:
              uid: "uid"
              mail: "email"
              name: "givenName"
            bind_dn: "cn=synapse,ou=services,dc=example,dc=org"
            bind_password: "synapse"
            filter: ""
    ma1sd_configuration: |
      ldap:
        enabled: true
        lookup: true
        connection:
          host: "example.org"
          port: 389
          bindDn: "cn=synapse,ou=services,dc=example,dc=org"
          bindPassword: "synapse"
          baseDNs:
            - "ou=employees,dc=example,dc=org"
        attribute:
          uid:
            type: "uid"
            value: "uid"
          name: "givenName"
          email: "email"
          msisdn: "phone"
```

### Multiple Server

An example playbook for a multi-server Synapse setup can be seen below:


```
# Example Single Server Matrix Automation Playbook
# Author: Cameron Wickes
# Date: 18/04/22
---
- hosts: server_one
  roles:
    - "matrix-automation"
  vars:
    postgres_user: synapse
    postgres_password: synapse
    postgres_database: synapse
    ma1sd_extender_username: ma1sd
    ma1sd_extender_password: ma1sd
    synapse_server_name: example.org
    synapse_federation_list: [other.org]
    synapse_configuration: |
      password_providers:
        - module: "ldap_auth_provider.LdapAuthProvider"
          config:
            enabled: true
            mode: "search"
            uri: "ldap://ldap.example.org:389"
            start_tls: false
            base: "ou=employees,dc=example,dc=org"
            attributes:
              uid: "uid"
              mail: "email"
              name: "givenName"
            bind_dn: "cn=synapse,ou=services,dc=example,dc=org"
            bind_password: "synapse"
            filter: ""
    ma1sd_configuration: |
      ldap:
        enabled: true
        lookup: true
        connection:
          host: "example.org"
          port: 389
          bindDn: "cn=synapse,ou=services,dc=example,dc=org"
          bindPassword: "synapse"
          baseDNs:
            - "ou=employees,dc=example,dc=org"
        attribute:
          uid:
            type: "uid"
            value: "uid"
          name: "givenName"
          email: "email"
          msisdn: "phone"
          
- hosts: server_two
  roles:
    - "matrix-automation"
  vars:
    postgres_user: synapse
    postgres_password: synapse
    postgres_database: synapse
    ma1sd_extender_username: ma1sd
    ma1sd_extender_password: ma1sd
    synapse_server_name: other.org
    synapse_federation_list: [example.org]
    synapse_configuration: |
      password_providers:
        - module: "ldap_auth_provider.LdapAuthProvider"
          config:
            enabled: true
            mode: "search"
            uri: "ldap://ldap.other.org:389"
            start_tls: false
            base: "ou=employees,dc=other,dc=org"
            attributes:
              uid: "uid"
              mail: "email"
              name: "givenName"
            bind_dn: "cn=synapse,ou=services,dc=other,dc=org"
            bind_password: "synapse"
            filter: ""
    ma1sd_configuration: |
      ldap:
        enabled: true
        lookup: true
        connection:
          host: "other.org"
          port: 389
          bindDn: "cn=synapse,ou=other,dc=example,dc=org"
          bindPassword: "synapse"
          baseDNs:
            - "ou=employees,dc=other,dc=org"
        attribute:
          uid:
            type: "uid"
            value: "uid"
          name: "givenName"
          email: "email"
          msisdn: "phone"
```

## ⚖️ License

`Matrix-Automation` is free and open-source software licensed under the [MIT License](https://github.com/cameronwickes/matrix-automation/blob/main/LICENSE).
