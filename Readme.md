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
  server:
    ingress:
      ingressClassName: nginx
      hosts:
        - prometheus.onwalk.net
      tls:
        - secretName: observable-server-tls
          hosts:
            - prometheus.onwalk.net
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

# Todo

## Dev Reference 
- https://github.com/YunaiV/ruoyi-vue-pro
- https://github.com/todoadmin/vue-admin-chart
- https://github.com/ClaudioWaldvogel/cloudwatch-loki-shipper
- https://github.com/cpsrepositorio/cps-marketplace-layout
- https://github.com/Hayaking/clickhouse-keeper-on-k8s
