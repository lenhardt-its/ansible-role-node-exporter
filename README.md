# Ansible Role: node exporter

[![ubuntu-18](https://img.shields.io/badge/ubuntu-18.x-orange?style=flat&logo=ubuntu)](https://ubuntu.com/)
[![ubuntu-20](https://img.shields.io/badge/ubuntu-20.x-orange?style=flat&logo=ubuntu)](https://ubuntu.com/)
[![debian-9](https://img.shields.io/badge/debian-9.x-orange?style=flat&logo=debian)](https://www.debian.org/)
[![debian-10](https://img.shields.io/badge/debian-10.x-orange?style=flat&logo=debian)](https://www.debian.org/)

[![License](https://img.shields.io/badge/license-MIT%20License-brightgreen.svg?style=flat)](https://opensource.org/licenses/MIT)
[![GitHub issues](https://img.shields.io/github/issues/OnkelDom/ansible-role-node-exporter?style=flat)](https://github.com/OnkelDom/ansible-role-node-exporter/issues)
[![GitHub tag](https://img.shields.io/github/tag/OnkelDom/ansible-role-node-exporter.svg?style=flat)](https://github.com/OnkelDom/ansible-role-node-exporter/tags)
[![GitHub action](https://github.com/OnkelDom/ansible-role-node-exporter/workflows/ansible-lint/badge.svg)](https://github.com/OnkelDom/ansible-role-node-exporter)

## Description

Deploy Prometheus Node Exporter [node exporter](https://github.com/prometheus/node_exporter) using ansible.

## Requirements

- Ansible >= 2.9 (It might work on previous versions, but we cannot guarantee it)
## Role Variables

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `proxy_env` | {} | Proxy environment variables |
| `node_exporter_version` | 1.1.0 | install version |
| `node_exporter_web_listen_address` | 0.0.0.0 | default listen address |
| `node_exporter_web_listen_port` | 9100 | default listen port |
| `node_exporter_binary_install_dir` | /usr/local/bin | default bin dir |
| `node_exporter_system_user` | prometheus | default system user |
| `node_exporter_system_group` | prometheus | default system group |
| `node_exporter_user_additional_groups` | "adm" | additional user group |
| `node_exporter_textfile_dir` | /var/lib/node_exporter | directory used by the [Textfile Collector](https://github.com/prometheus/node_exporter#textfile-collector). To get permissions to write metrics in this directory, users must be in `{{ prometheus_user | default('prometheus') }}` system group. |
| `node_exporter_config_dir` | /etc/node_exporter | config dir |
| `node_exporter_log_level` | warn | default log level |
| `node_exporter_log_format` | json | default log format |
| `node_exporter_allow_firewall` | false | allow port on firewall |
| `node_exporter_disable_go_metrics` | true | disable useless metrics |
| `node_exporter_textfile_collectors` | [] | list of textfile collectors which should be copied and crontab setup for them. |
| `node_exporter_tls_server_config` | {} | tls config |
| `node_exporter_http_server_config` | {} | http config |
| `node_exporter_basic_auth_users` | {} | basic auht config |
| `node_exporter_enabled_collectors` | [ systemd, textfile ] | list of additionally enabled collectors. It adds collectors to [those enabled by default](https://github.com/prometheus/node_exporter#enabled-by-default) |
| `node_exporter_disabled_collectors` | [defaults/main.yml](defaults/main.yml) | list of disabled collectors. By default node_exporter disables collectors listed [here](https://github.com/prometheus/node_exporter#disabled-by-default). |
| `node_exporter_webconfig` | [] | Define Node-Exporter Webconfig |

## Example

### Playbook

### Advanced playbook
Following playbook sets up few more advanced features of this role. Mostly:
- Every role run checks and tries to update node_exporter binary to latest available version
- node_exporter is run as `root` user, which allows to use `systemd` collector
- `systemd` collector is enabled
- enables cron job running two textfile collectors: [`yum.sh`](https://github.com/prometheus-community/node-exporter-textfile-collector-scripts/blob/master/yum.sh) and [`md_info_detail.sh`](https://github.com/prometheus-community/node-exporter-textfile-collector-scripts/blob/master/md_info_detail.sh) Textfile collector is also copied from deployer host with path specified in `src`.

```yaml
- hosts: all
  roles:
    - onkeldom.node_exporter
  vars:
    node_exporter_enabled_collectors:
      - systemd
    node_exporter_textfile_collectors:
      - src: "/tmp/md_info_detail.sh"
        special_time: "hourly"
      - src: "/tmp/yum.sh"
        user: node-exp
        minute: "*"
        hour: "*"
        day: "*"
        month: "*"
        weekday: "*"
```

## Contributing

See [contributor guideline](CONTRIBUTING.md).

## License

This project is licensed under MIT License. See [LICENSE](/LICENSE) for more details.
