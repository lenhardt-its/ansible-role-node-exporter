# Ansible Role: node exporter

[![License](https://img.shields.io/badge/license-MIT%20License-brightgreen.svg)](https://opensource.org/licenses/MIT)
[![GitHub tag](https://img.shields.io/github/tag/OnkelDom/ansible-role-node-exporter.svg)](https://github.com/OnkelDom/ansible-role-node-exporter/tags)
[![GitHub issues](https://img.shields.io/github/issues/OnkelDom/ansible-role-node-exporter)](https://github.com/OnkelDom/ansible-role-node-exporter/issues)
[![GitHub license](https://img.shields.io/github/license/OnkelDom/ansible-role-node-exporter)](https://github.com/OnkelDom/ansible-role-node-exporter/blob/master/LICENSE)

## Description

Deploy Prometheus Node Exporter [node exporter](https://github.com/prometheus/node_exporter) using ansible.

## Requirements

- Ansible >= 2.5 (It might work on previous versions, but we cannot guarantee it)

## Role Variables

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `node_exporter_version` | 0.18.1 | Node exporter package version. Also accepts latest as parameter. |
| `node_exporter_web_listen_address` | "0.0.0.0:9100" | Address on which node exporter will listen |
| `node_exporter_system_group` | "node-exp" | System group used to run node_exporter |
| `node_exporter_system_user` | "node-exp" | System user used to run node_exporter |
| `node_exporter_enabled_collectors` | [ systemd, textfile ] | List of additionally enabled collectors. It adds collectors to [those enabled by default](https://github.com/prometheus/node_exporter#enabled-by-default) |
| `node_exporter_disabled_collectors` | [] | List of disabled collectors. By default node_exporter disables collectors listed [here](https://github.com/prometheus/node_exporter#disabled-by-default). |
| `node_exporter_textfile_dir` | "/var/lib/node_exporter" | Directory used by the [Textfile Collector](https://github.com/prometheus/node_exporter#textfile-collector). To get permissions to write metrics in this directory, users must be in `node-exp` system group. |
| `node_exporter_textfile_collectors` | [] | List of textfile collectors which should be copied and crontab setup for them. |

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
    - onkeldom.ansible-role-node-exporter
  vars:
    node_exporter_system_group: "root"
    node_exporter_system_user: "root"
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
