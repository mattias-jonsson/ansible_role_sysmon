Ansible Role: sysmon
==============

This Ansible role installs, upgrade and configures Sysinternals Sysmon on Windows Server 2016, 2019, 2022, Windows 10 and Windows 11.

Requirements
---------------

This role requires the `ansible.windows`, `community.windows` Ansible collections. If not already installed, you can install them using the following command:

```shell
ansible-galaxy collection install ansible.windows community.windows
```

Role Variables
--------------

Available variables are listed below, along with default values where applicable (see `defaults/main.yml`):

| Variable | Required | Default | Comments |
| -------- | -------- | ------- | -------- |
| `sysmon_config_file` | No | 'C:\\Windows\\sysmonconfig.xml' | Specifies the location of Sysmon config file. |
| `sysmon_eventlog_maxsize_gigabyte` | No | 1 | Specifies maximum size in GigaBytes for Sysmon operational log. |
| `sysmon_sha256sum` | No | '900a7bbf67b3c0e0c2109e3fb14a534a90f55f326d625a332bdd3c7d95d44c04' | Specifies SHA256 checksum for Sysmon.zip installer archive. |
| `sysmon_installer` | No | 'https://download.sysinternals.com/files/Sysmon.zip' | Specifies URL to Sysmon.zip installer archive. |
| `sysmon_install_version` | No | '15.14' | Specifies version of Sysmon to install. |
| `sysmon_config_xml_data` | No | 'See defaults/main.yml' | Specifies XML file data for Sysmon config. |


Example Playbook
----------------

```shell
- hosts: servers

  vars:
    sysmon_eventlog_maxsize_gigabyte: 2
    sysmon_sha256sum: 900a7bbf67b3c0e0c2109e3fb14a534a90f55f326d625a332bdd3c7d95d44c04
    sysmon_install_version: '15.14'
    sysmon_config_xml_data: |
      <Sysmon schemaversion="4.50">
          <!-- Capture all hashes -->
          <HashAlgorithms>*</HashAlgorithms>
          <EventFiltering>

              <!-- Process creation -->
              <ProcessCreate onmatch="include">
                  <CommandLine condition="contains">powershell</CommandLine>
              </ProcessCreate>

              <!-- Network connection -->
              <NetworkConnect onmatch="include">
                  <DestinationIp condition="is not">127.0.0.1</DestinationIp>
                  <DestinationPort condition="is not">80</DestinationPort>
                  <Protocol condition="is">tcp</Protocol>
              </NetworkConnect>

              <!-- File creation time changed -->
              <FileCreateTime onmatch="include">
                  <TargetFilename condition="end with">.exe</TargetFilename>
              </FileCreateTime>

          </EventFiltering>
      </Sysmon>

  roles:
      - sysmon
```

License
-------

MIT

Author Information
------------------

Mattias Jonsson
