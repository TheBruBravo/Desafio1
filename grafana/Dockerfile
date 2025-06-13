FROM grafana/grafana:latest

# Copia el provisioning del datasource
COPY provisioning.yaml /etc/grafana/provisioning/datasources/provisioning.yaml

# Copia los dashboards
COPY dashboard-cadvisor.json /etc/grafana/provisioning/dashboards/dashboard-cadvisor.json
COPY dashboard-node-exporter.json /etc/grafana/provisioning/dashboards/dashboard-node-exporter.json

# Provisiona el dashboard automáticamente
COPY provisioning-dashboard.yaml /etc/grafana/provisioning/dashboards/provisioning-dashboard.yaml
