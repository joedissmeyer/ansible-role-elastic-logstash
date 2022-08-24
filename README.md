<p><img src="https://cdn.worldvectorlogo.com/logos/elastic-logstash.svg" alt="logstash logo" title="logstash" align="right" height="60" /></p>

# Ansible Role: Elastic Logstash

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Ansible Role](https://img.shields.io/badge/ansible%20role-joedissmeyer.ansible_role_elastic_logstash-blue.svg)](https://galaxy.ansible.com/joedissmeyer/ansible_role_elastic_logstash)

## Description

Deploys and configures [Elastic Logstash](https://www.elastic.co/logstash/) to a target VM instance using Ansible. The purpose of this role is to quickly and efficiently install and configure Logstash. Minimal variables are used to achieve this goal.

This role performs the following tasks:

1. Installs the Logstash package (RPM).
2. Sets up a basic logstash pipeline for Elastic Beats that listens on TCP 5044.

## Requirements

- Ansible >= 2.12 (It might work on previous versions.)
- Install the role, `ansible-galaxy install joedissmeyer.ansible_role_elastic_logstash`
- This role is configured _only_ to work against RHEL-based instances at this time.
- The target virtual machine MUST have at least 2gb of memory.
- Assumes the ansible target is running firewalld.
- Assumes Elasticsearch security is enabled. (See notes in example below.)

## Role Variables

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below.

Several variables reference the official configuration items needed by Logstash to function. More details about every possible environment variable for Logstash can be found in the [official docs](https://www.elastic.co/guide/en/logstash/master/index.html).

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `logstash_version` | '7.17.5' | The version of Logstash to download and install. |
| `logstash_listen_port_beats` | 5044 | Listening port for Logstash to use with the beats pipeline. |
| `es_http_protocol` | https | HTTP protocol to use for the Elasticsearch target output in pipelines. Only http or https are the possible options. |
| `es_ingest_target` | localhost | Elasticsearch target for logstash pipeline outputs. |
| `es_port` | 9200 | The destination port for the Elasticsearch ingest endpoint. This is used in logstash pipelines. |
| `es_security_enabled` | true | Directory for the Logstash configuration files. |
| `es_logstash_user` | logstash | The Logstash user account ID in Elasticsearch when `es_security_enabled` is true. |
| `es_logstash_pass` | MyLogstashPass1 | The Logstash user account password in Elasticsearch when `es_security_enabled` is true. |
| `logstash_jvm_memory` | 1g | Logstash JVM memory setting for the Xmx and Xms setting in jvm.options. '1g' = 1 gigabyte. |
| `logstash_rhel_download_source` | https://artifacts.elastic.co/downloads/logstash/logstash-{{ logstash_version }}-x86_64.rpm | Base URL for where to download the RPM package. Depends on `logstash_version`. |
| `logstash_config_dir` | /etc/logstash | Directory for the Logstash configuration files. |
| `logstash_logs_dir` | /var/log/logstash | Directory where the Logstash service logs are written to. |
| `logstash_data_dir` | /var/lib/logstash| General Logstash data directory. |
| `logstash_queue_dir` | /opt/logstash/queue | Directory where Logstash will store persistent queue data. |
| `logstash_ssl_certificate_path` | /etc/logstash | Directory for TLS certificates. |
| `logstash_ssl_certificate_file` | "/etc/logstash/certificate.crt" | Signed TLS certificate file path. |
| `logstash_ssl_key_file` | "/etc/logstash/certificate.key" | Signed TLS key file path. |
| `logstash_ssl_ca_cert` | "/etc/logstash/ca.crt" | TLS CA cert file path. |
| `logstash_enabled_on_boot` | true | Defines whether or not to enable the systemd unit to be enabled at boot time. |

## Examples

Simply include the role in your playbook and modify variables as needed.

First, make sure the role is installed.
You can install via Ansible Galaxy or via requirements file.
To install using Galaxy,

```shell
$ ansible-galaxy install joedissmeyer.ansible_role_elastic_logstash
```

Or using a requirements file:

```
roles:
- name: joedissmeyer.ansible_role_elastic_logstash
  src: https://github.com/joedissmeyer/ansible-role-elastic-logstash.git
```

You may include any variable overrides in `vars` as needed.
It would be wise to place the Elasticsearch user account and password in a vars file with Ansible Vault.

There are two playbook examples I can provide both of which are based on if your Elasticsearch cluster has security enabled or disabled.

First example where security is enabled. 

```
### If your Elasticsearch cluster has SECURITY TURNED ON / ENABLED (i.e. XPack Security = True)
- name: Install Elastic Logstash
  become: yes
  gather_facts: yes
  hosts: logstash
  roles:
    - joedissmeyer.ansible_role_elastic_logstash
  vars:
    logstash_version: 7.17.5
    logstash_jvm_memory: "1g"
    es_logstash_user: logstash
    es_logstash_pass: MyLogstashPass1
    logstash_rhel_download_source: https://artifacts.elastic.co/downloads/logstash/logstash-{{ logstash_version }}-x86_64.rpm
```

Second example where security is disabled. 

```
### If your Elasticsearch cluster has SECURITY TURNED OFF / DISABLED (i.e. XPack Security = False)
- name: Install Elastic Logstash
  become: yes
  gather_facts: yes
  hosts: logstash
  roles:
    - joedissmeyer.ansible_role_elastic_logstash
  vars:
    logstash_version: 7.17.5
    logstash_jvm_memory: "1g"
    es_security_enabled: false
    logstash_rhel_download_source: https://artifacts.elastic.co/downloads/logstash/logstash-{{ logstash_version }}-x86_64.rpm
```

## License

This project is licensed under the Apache 2.0 license. See [LICENSE](/LICENSE) for more details.