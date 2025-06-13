# Suite de Monitoreo

Este proyecto contiene una configuración básica de Prometheus para monitoreo, con la opción de levantar el servicio usando Docker Compose o manualmente con Docker. Incluye dashboards de ejemplo para Prometheus, cAdvisor y Node Exporter en Grafana.

## Levantar la Suite de Monitoreo con Docker Compose

1. Clona este repositorio.
2. Abre una terminal en la carpeta del proyecto.
3. Ejecuta:

   docker-compose up -d

4. Accede a Prometheus en: http://localhost:9090
5. Accede a Grafana en: http://localhost:3000 (usuario y contraseña por defecto: admin / admin, puede actualizar la contraseña si lo desea)

---

## Crear una red Docker para conectar los contenedores

Antes de levantar los servicios por separado, crea una red personalizada para que los contenedores puedan comunicarse entre sí:

   docker network create monitoring

---

## Levantar Prometheus solo con Docker (sin Docker Compose)

1. Construye la imagen personalizada de Prometheus:

   docker build -t thebrubravo/prometheus-custom:v1.0.0 ./prometheus

2. Crea un volumen para los datos persistentes:

   docker volume create prometheus_data

3. Ejecuta el contenedor de Prometheus en la red `monitoring`:

   docker run -d --name prometheus --network monitoring -p 9090:9090 \
     -v prometheus_data:/prometheus \
     thebrubravo/prometheus-custom:v1.0.0 \
     --config.file=/etc/prometheus/prometheus.yaml \
     --storage.tsdb.path=/prometheus \
     --storage.tsdb.retention.time=30d \
     --web.enable-lifecycle

4. Accede a Prometheus en: http://localhost:9090

---

## Levantar Grafana solo con Docker (sin Docker Compose)

1. Construye la imagen personalizada de Grafana:

   docker build -t thebrubravo/grafana-custom:v1.0.0 ./grafana

2. Crea un volumen para los datos persistentes:

   docker volume create grafana_data

3. Ejecuta el contenedor de Grafana en la red `monitoring`:

   docker run -d --name grafana --network monitoring -p 3000:3000 \
     -v grafana_data:/var/lib/grafana \
     thebrubravo/grafana-custom:v1.0.0

4. Accede a Grafana en: http://localhost:3000 (usuario y contraseña por defecto: admin / admin)

---

## Levantar cAdvisor y Node Exporter solo con Docker (sin Docker Compose)

### cAdvisor

1. Ejecuta el contenedor de cAdvisor en la red `monitoring`:

   docker run -d --name cadvisor --network monitoring -p 8080:8080 \
     -v /:/rootfs:ro \
     -v /var/run:/var/run:ro \
     -v /sys:/sys:ro \
     -v /var/lib/docker/:/var/lib/docker:ro \
     gcr.io/cadvisor/cadvisor:latest

2. Accede a la interfaz de cAdvisor en: http://localhost:8080

### Node Exporter

1. Ejecuta el contenedor de Node Exporter en la red `monitoring`:

   docker run -d --name node_exporter --network monitoring -p 9100:9100 prom/node-exporter:latest

2. Accede a las métricas de Node Exporter en: http://localhost:9100/metrics

---

## Dashboards de ejemplo

En la carpeta `grafana` se incluyen dashboards de ejemplo para cAdvisor y Node Exporter. Estos dashboards se cargan automáticamente al iniciar Grafana, pero puedes modificarlos o crear los tuyos propios según tus necesidades.

- `dashboard-cadvisor.json`: Ejemplo para cAdvisor.
- `dashboard-nodeexporter.json`: Ejemplo para Node Exporter.

---

## Archivos importantes
- `docker-compose.yaml`: Orquestación de servicios y volúmenes.
- `prometheus/Dockerfile`: Imagen personalizada de Prometheus.
- `prometheus/prometheus.yaml`: Configuración de Prometheus.
- `grafana/Dockerfile`: Imagen personalizada de Grafana y provisioning.
- `grafana/provisioning.yaml`: Datasource de Prometheus para Grafana.
- `grafana/provisioning-dashboard.yaml`: Provisioning de dashboards para Grafana.

---

## Notas
- El servicio Prometheus se expone en el puerto 9090.
- Grafana se expone en el puerto 3000.
- Los datos se almacenan en volúmenes Docker para persistencia.
- Puedes modificar `prometheus.yaml` para agregar más jobs o targets según tus necesidades.
- Los dashboards incluidos son solo ejemplos y pueden ser personalizados.