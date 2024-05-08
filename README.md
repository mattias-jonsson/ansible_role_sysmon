Ansible Role: sysmon
==============

This Ansible role installs, upgrade and configures Sysinternals Sysmon on Windows Server (2016, 2019, 2022), Windows 10 and Windows 11.

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
| `sysmon_config_file` | No | `C:\\Windows\\sysmonconfig.xml` | Location of the Sysmon configuration file. |
| `sysmon_eventlog_maxsize_gigabyte` | No | `1` | Maximum capacity of the Sysmon operational log in gigabytes (GB). This parameter defines the log’s storage limit before it automatically overwrites the oldest events. Sysmon logs are stored in the Windows Event Log at `Applications and Services Logs/Microsoft/Windows/Sysmon/Operational`." |
| `sysmon_installer_sha256sum` | No | `900a7bbf67b3c0e0c2109e3fb14a534a90f55f326d625a332bdd3c7d95d44c04` | SHA256 checksum for the Sysmon.zip installer. |
| `sysmon_installer_url` | No | `https://download.sysinternals.com/files/Sysmon.zip` | 	URL to the Sysmon.zip installer archive. |
| `sysmon_installer_version` | No | `15.14` | Version of Sysmon to install. |
| `sysmon_config_xml_data` | No | See `defaults/main.yml` | XML data for Sysmon configuration. The default value uses the SwiftOnSecurity Sysmon config, available at [SwiftOnSecurity sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config). |

Example Playbook
----------------

```shell
- hosts: servers

  vars:
    sysmon_eventlog_maxsize_gigabyte: 2
    sysmon_installer_sha256sum: 900a7bbf67b3c0e0c2109e3fb14a534a90f55f326d625a332bdd3c7d95d44c04
    sysmon_installer_version: '15.14'
    sysmon_config_xml_data: |
        <Sysmon schemaversion="4.82">
        <!-- Capture all hashes -->
        <HashAlgorithms>*</HashAlgorithms>
        <EventFiltering>
            <!-- Log all drivers except if the signature -->
            <!-- contains Microsoft or Windows -->
            <DriverLoad onmatch="exclude">
            <Signature condition="contains">microsoft</Signature>
            <Signature condition="contains">windows</Signature>
            </DriverLoad>
            <!-- Do not log process termination -->
            <ProcessTerminate onmatch="include" />
            <!-- Log network connection if the destination port equal 443 -->
            <!-- or 80, and process isn't InternetExplorer -->
            <NetworkConnect onmatch="include">
            <DestinationPort>443</DestinationPort>
            <DestinationPort>80</DestinationPort>
            </NetworkConnect>
            <NetworkConnect onmatch="exclude">
            <Image condition="end with">iexplore.exe</Image>
            </NetworkConnect>
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
