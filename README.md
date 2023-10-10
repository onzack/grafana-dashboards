# Grafana Dashboards
ONZACK Grafana Dashboards for Kubernetes, OpenShift and other systems.

## Kubernetes / OpenShift Base Monitoring
ONZACK worked with LGT Bank in Liechtenstein to create two Grafana dashboards for Kubernetes and OpenShift. LGT Bank and ONZACK have agreed to make the dashboards available to the Kubernetes and OpenShift community so that everyone can benefit from the dashboards. ONZACK will continue to maintain and develop the dashboards in the future.

### Cluster Monitoring
![ONZACK Cluster Monitoring](https://github.com/onzack/grafana-dashboards/blob/main/docs/onzack-cluster-monitoring.png)

- [Download](https://github.com/onzack/grafana-dashboards/blob/main/grafana/kubernetes/without-recording-rules/onzack-cluster-monitoring.json) ONZACK Cluster Monitoring Dashboard without recording rules
- [Download](https://github.com/onzack/grafana-dashboards/blob/main/grafana/kubernetes/with-recording-rules/standard-cluster-monitoring.json) ONZACK Cluster Monitoring Dashboard with recording rules
- [Download](https://github.com/onzack/grafana-dashboards/blob/main/prometheus/recording-rules/onzack-cluster-monitoring-recording-rules.yaml) Prometheus Recording Rules for ONZACK Cluster Monitoring Dashboard

### Namespace Monitoring
![ONZACK Namespace Monitoring](https://github.com/onzack/grafana-dashboards/blob/main/docs/onzack-namespace-monitoring.png)

- [Download](https://github.com/onzack/grafana-dashboards/blob/main/grafana/kubernetes/without-recording-rules/onzack-namespace-monitoring.json) ONZACK Namespace Monitoring Dashboard without recording rules
- [Download](https://github.com/onzack/grafana-dashboards/blob/main/grafana/kubernetes/with-recording-rules/standard-namespace-monitoring.json) ONZACK Namespace Monitoring Dashboard with recording rules
- [Download](https://github.com/onzack/grafana-dashboards/blob/main/prometheus/recording-rules/onzack-namespace-monitoring-recording-rules.yaml) Prometheus Recording Rules for ONZACK Namespace Monitoring Dashboard

## Falco Monitoring
![ONZACK Falco Monitoring](https://github.com/onzack/grafana-dashboards/blob/main/docs/onzack-falco-monitoring.png)

Falco, the cloud-native runtime security project, is the de facto Kubernetes threat detection engine.
This dashboard gives a good overview of the events generated by Falco.

### Prerequisites
- [Falco](https://falco.org/)
- [Falcosidekick](https://github.com/falcosecurity/falcosidekick) -> to ship the Falco events to Loki
- [Loki](https://github.com/grafana/loki) (Datasource)

## Longhorn Monitoring
![ONZACK Longhorn Monitoring](https://github.com/onzack/grafana-dashboards/blob/main/docs/onzack-longhorn-monitoring.png)

Longhorn is a distributed block storage system for Kubernetes. This dashboard has been built to serve as an example for monitoring the Longhorn storage system.

### Prerequisites
- [Longhorn](https://longhorn.io)
- Prometheus [ServiceMonitor](https://longhorn.io/docs/1.3.1/monitoring/integrating-with-rancher-monitoring/) for Longhorn
- Add Longhorns example [PrometheusRules (Alerting Rules)](https://longhorn.io/docs/1.3.1/monitoring/alert-rules-example/)

## OPNsense & IDS/IPS Monitoring
![ONZACK OPNsense Monitoring](https://github.com/onzack/grafana-dashboards/blob/main/docs/onzack-opnsense-ids-monitoring.png)

Dashboards to monitor Firewall Rules & IDS/IPS potential Attacks.

### Prerequisites
- Loadbalancer to create virtual IP and to publish syslog ports (for example [MetalLB](https://metallb.universe.tf/))
- Promtail or Grafana Agent to listen on these ports and ship the logs to Loki
- Loki to index the logs
- Grafana to visualize the logs (datasource Loki configured)
- OPNsense syslog target configured

The Grafana Dashboard only works when your OPNsense IDS/IPS logs has a label ```job: syslog-ids``` and all other Firewall logs has a label ```job: syslog```.

Promtail Helm chart example values:

```yaml
config:
  lokiAddress: "http://loki-gateway/loki/api/v1/push"
  snippets:
    extraScrapeConfigs: |
      # Add an additional scrape config for syslog
      - job_name: syslog
        syslog:
          listen_address: 0.0.0.0:514
          idle_timeout: 60s
          label_structured_data: yes
          labels:
            job: "syslog"
        relabel_configs:
          - source_labels: ['__syslog_message_hostname']
            target_label: 'host'
      # Add an additional scrape config for OPNsense IDS alerts shipped over syslog
      - job_name: syslog-ids
        syslog:
          listen_address: 0.0.0.0:1514
          idle_timeout: 60s
          label_structured_data: yes
          labels:
            job: "syslog-ids"
        relabel_configs:
          - source_labels: ['__syslog_message_hostname']
            target_label: 'host'

extraPorts:
  # Add an additional port for syslog
  # serviceType "LoadBalancer" only works if you have deployed an Loadbalancer for example MetalLB
  syslog:
    name: tcp-syslog
    containerPort: 514
    protocol: TCP
    service:
      type: LoadBalancer
      #clusterIP: null
      port: 514
      #externalIPs: []
      #nodePort: 32682
      #annotations: {}
      #labels: {}
      #loadBalancerIP: null
      #loadBalancerSourceRanges: []
      #externalTrafficPolicy: null
  # Add an additional port for OPNsense IDS alerts shipped over syslog
  # serviceType "LoadBalancer" only works if you have deployed an Loadbalancer for example MetalLB
  syslog-ids:
    name: tcp-syslog-ids
    containerPort: 1514
    protocol: TCP
    service:
      type: LoadBalancer
      #clusterIP: null
      port: 1514
      #externalIPs: []
      #nodePort: 32682
      #annotations: {}
      #labels: {}
      #loadBalancerIP: null
      #loadBalancerSourceRanges: []
      #externalTrafficPolicy: null
```

## Velero Monitoring
![ONZACK Velero Monitoring](https://github.com/onzack/grafana-dashboards/blob/main/docs/onzack-velero-monitoring.png)

Velero is an open source tool to safely backup and restore, perform disaster recovery, and migrate Kubernetes cluster resources and persistent volumes.
This dashboard gives a good overview of the events and performance of Velero.

### Prerequisites
- [Velero](https://velero.io)

## Systemd Monitoring
![ONZACK Systemd Monitoring](https://github.com/onzack/grafana-dashboards/blob/main/docs/onzack-systemd-monitoring.png)

Monitor state of systemd services running on the node.

### Prerequisites
- Node Exporter with Systemd collector enabled (Note: in order to collect metrics you need to mount /var/run/dbus/system_bus_socket into node-exporter and allow read access in Apparmor, if enabled)

# Licence
Copyright 2023 ONZACK AG

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
