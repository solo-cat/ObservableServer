# Overview

![ObservableArch](/scripts/pic/ObservableArchDesign.jpg "ObservableArch")

# Prep

# Install

```
kubectl get nodes
kubectl label nodes node-xxxx prometheus=true --overwrite
kubectl create ns monitoring
kubectl create secret tls observable-server-tls --cert=your.domain.pem --key=your.domain.key -n monitoring

cat > values.yaml << EOF
global:
  domain: onwalk.net
  namespace: monitoring
  secretName: observable-server-tls
loki-distributed:
  ruler:
    enabled: true
  ingress:
    hosts:
      - "loki.onwalk.net"
    tls:
      - secretName: observable-server-tls
        hosts:
          - loki.onwalk.net
deepflow:
  enabled: true
  grafana:
    enabled: true
    ingress:
      enabled: true
      ingressClassName: nginx
      hosts:
        - grafana.onwalk.net
      tls:
        - secretName: observable-server-tls
          hosts:
            - grafana.onwalk.net
prometheus:
  kube-state-metrics:
    image:
      repository: artifact.onwalk.net/k8s/kube-state-metrics
      tag: v2.7.0
  server:
    ingress:
      ingressClassName: nginx
      hosts:
        - prometheus.onwalk.net
      tls:
        - secretName: observable-server-tls
          hosts:
            - prometheus.onwalk.net
alertmanager:
  ingress:
    enabled: true
    className: "nginx"
    hosts:
      - host: alertmanager.onwalk.net
    tls:
       - secretName: observable-server-tls
         hosts:
           - alertmanager.onwalk.net
  configmapReload:
    enabled: false
  config:
    global:
      resolve_timeout: 5m #超时,默认5min
      smtp_smarthost: 'smtp.qq.com:465'
      smtp_from: '11111111@qq.com'
      smtp_auth_username: '11111111@qq.com'
      smtp_auth_password: '123456'
      smtp_require_tls: false
  
    templates:
      - '/etc/alertmanager/*.tmpl'
  
    receivers:
      - name: 'default-receiver'
        email_configs:
        - to: '{{ template "email.to" . }}'
          html: '{{ template "email.to.html" . }}'

    route:
      group_wait: 10s
      group_interval: 5m
      receiver: default-receiver
      repeat_interval: 1h
EOF

helm repo add stable https://artifact.onwalk.net/chartrepo/k8s/
helm repo update
helm upgrade --install observable-server stable/observableserver -n monitoring -f values.yaml 
```

# Configure

* https://grafana.your.domain  admin/deepflow
* https://loki.your.domain
* https://prometheus.your.domain

# LiveDemo

# Reference 

- https://helm.neo4j.com/neo4j
- https://grafana.github.io/helm-charts
- https://deepflowys.github.io/deepflow
- https://prometheus-community.github.io/helm-charts
- https://github.com/alerta/alerta-webui

- https://grafana.com/grafana/dashboards/13105-1-k8s-for-prometheus-dashboard-20211010/
- https://grafana.com/grafana/dashboards/15172-node-exporter-for-prometheus-dashboard-based-on-11074/

# Todo

## Dev Reference 
- https://github.com/YunaiV/ruoyi-vue-pro
- https://github.com/todoadmin/vue-admin-chart
- https://github.com/ClaudioWaldvogel/cloudwatch-loki-shipper
- https://github.com/cpsrepositorio/cps-marketplace-layout
- https://github.com/Hayaking/clickhouse-keeper-on-k8s
