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
</p>

<br/>

<p>
  <b>Matrix-Automation creates a functioning Synapse Homeserver by configuring four rootless Podman containers:</b>
  <ul>
    <li>A PostgreSQL Database - Storing Synapse Data</li>
    <li>A Synapse Homeserver - Federating Rooms</li>
    <li>A Ma1sd Identity Server - Handling 3PID Integrations</li>
    <li>A Reverse Proxy - Link Connections to Synapse</li>
  </ul>
  
  </br>
  
  It supports LDAP and SSO integrations for login and user search, and can federate with other Synapse homeservers and identity servers out of the box. Example playbooks can be seen below.
</p>

## 🪄 Dependencies

## ⚙️ Role Variables

## 🗒️ Example Playbook

## ⚖️ License

`Matrix-Automation` is free and open-source software licensed under the [MIT License](https://github.com/cameronwickes/matrix-automation/blob/main/LICENSE).
