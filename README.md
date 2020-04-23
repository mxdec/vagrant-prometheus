# Prometheus Infrastructure

Demo monitoring infrastructure showing how to:
- Setup Consul server.
- Register nodes to consul with Consul agent.
- Discover targets from Consul.
- Scrape metrics with Node Exporter.
- Ping hosts with Blackbox Exporter.
- Export metrics to remote storage with VictoriaMetrics.
- Dashboard provisionning for metrics visualization in Grafana.

This playbook deploys the following services:
- [prometheus](https://github.com/prometheus/prometheus)
- [alertmanager](https://github.com/prometheus/alertmanager)
- [victoriametrics](https://github.com/victoriametrics/victoriametrics)
- [node_exporter](https://github.com/prometheus/node_exporter)
- [blackbox_exporter](https://github.com/prometheus/blackbox_exporter)
- [consul](https://github.com/hashicorp/consul)
- [grafana](https://github.com/grafana/grafana)

### Requirements

- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Vagrant](https://www.vagrantup.com/)

### Usage (dev)

Clone repo:
```
$ git clone git@github.com:maxdrc/vagrant-prometheus.git
$ cd vagrant-prometheus
```

Generate encrypt key for Consul:
```
$ export CONSUL_KEY=`openssl rand -base64 32`
```

Create VM:
```
$ vagrant up
```

Manual provisionning:
```
$ vagrant up --provision
```

Connect into the VM:
```
$ vagrant ssh vag-prom01
```

Remove VMs:
```
$ vagrant destroy -f
```

### UI

- Consul: http://192.168.50.22:8500
- Prometheus http://192.168.50.20:9090
- Alertmanager http://192.168.50.20:9093
- Grafana http://192.168.50.21:3000
