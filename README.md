# Ansible role for Prometehus

[![CircleCI](https://circleci.com/gh/angristan/ansible-prometheus.svg?style=svg)](https://circleci.com/gh/angristan/ansible-prometheus)

This role will setup [Prometheus](https://github.com/prometheus/prometheus/) on any Linux machine running systemd.

## Requirements

- systemd on the target host
- gnu-tar on Mac deployer host (`brew install gnu-tar`)

## Role Variables

- `prometheus_version`: the versions that will be installed and downloaded. (`2.8.0`).

The role will download the Prometheus release on the deployer and upload the needed files to the target host.

If the `/usr/local/bin/prometheus` binary already exists, the role will skip the install steps. You can force them (to update, for instance), by setting `prometheus_force_install` to true.

- `prometheus_config_dir`: the configuration directory (`/etc/prometheus`)
- `prometheus_data_dir`: the data directory (`/var/lib/prometheus`)
- `prometheus_web_listen_address`: the listening address of the web interface (`0.0.0.0:9090`)
- `prometheus_web_external_url` the external URL you will access the web interface with (`''`)
- `prometheus_storage_retention`: the retention period (`30d`)
- `prometheus_storage_retention_size`: the max retention size (`0`, unlimited)
- `prometheus_config_flags_extra`: extra flags passed to prometheus in the systemd unit (`{}`)
- `prometheus_alertmanager_config`: original YAML for the `alertmanager_config` in `prometheus.yml`. (`{}`)
- `prometheus_global`: defaults global intervals and timeouts (see `defaults/main.yml`)
- prometheus_scrape_configs: original YAML for the `scrape_configs` in `promtheus.yml`. Defaults to the native metrics (`localhost:9090`).
- `prometheus_alert_rules_files`: relative path of the custom alert rules files. (`prometheus/rules/*.rules`)
- `prometheus_alert_rules`: oiginal YAML of the rules written `rules/ansible_alert.rules`. Some rules are preconfigured, see `defaults/main.yml`.

You can write all your rules in `prometheus_alert_rules`, or keep the default rules and add the other as files with `prometheus_alert_rules_files`. You can remove the default rules by setting `prometheus_alert_rules` to `[]`.

## Example playbook

```yaml
---

- hosts: myhost
  roles: prometheus
  vars:
    prometheus_web_listen_address: "127.0.0.1:9090"
    prometheus_web_external_url: 'https://prometheus.domain.tld'
    prometheus_scrape_configs:
      - job_name: "prometheus"
        static_configs:
          - targets:
              - "localhost:9090"
      - job_name: "node_exporter"
        static_configs:
          - targets:
              - "localhost:9100"
    prometheus_alertmanager_config:
      - static_configs:
        - targets:
          - localhost:9093
```

## License

MIT. See LICENSE for more details.

## Credit

This role is largely inspired by [cloudalchemy/ansible-prometheus](https://github.com/cloudalchemy/ansible-prometheus).

## Author Information

See my other Ansible roles at [angristan/ansible-roles](https://github.com/angristan/ansible-roles).
