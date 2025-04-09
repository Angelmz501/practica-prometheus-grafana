# 🛠️ Práctica Final: Monitorización con Prometheus y Grafana

Este proyecto configura un entorno de monitorización utilizando **Docker Compose**, con los siguientes componentes:

- **Prometheus**: recolector de métricas.
- **Grafana**: visualización de métricas mediante dashboards.
- **Node Exporter**: exporta métricas del sistema (CPU, memoria, disco...).
- **cAdvisor**: monitoriza contenedores Docker.
- **nginx**: servicio de prueba con etiquetas para identificación.
  
---

## 📦 Servicios

| Servicio         | Puerto | Descripción                              |
|------------------|--------|------------------------------------------|
| Prometheus       | 9090   | UI en [http://localhost:9090](http://localhost:9090) |
| Grafana          | 3000   | UI en [http://localhost:3000](http://localhost:3000) |
| Node Exporter    | 9100   | Exporta métricas del sistema             |
| cAdvisor         | 8080   | Métricas de contenedores Docker          |
| nginx-labeled    | 8082   | Servidor nginx con etiquetas personalizadas |

---

## ▶️ Cómo levantar el proyecto

1. Clona el repositorio:
   ```bash
   git clone git@github.com:Angelmz501/practica-prometheus-grafana.git
   cd practica-prometheus-grafana
   ```

2. Lanza los servicios:
   ```bash
   docker-compose up -d
   ```

3. Accede a los servicios desde tu navegador:

   - Prometheus: [http://localhost:9090](http://localhost:9090)
   - Grafana: [http://localhost:3000](http://localhost:3000)
     - Usuario: `admin`
     - Contraseña: `admin` (o la que definas en Grafana)

---

## 📊 Dashboards de Grafana

Los dashboards están exportados en formato `.json` y se encuentran en la carpeta [`dashboards/`](./dashboards/).

### 📥 Importar un dashboard en Grafana

1. Abre Grafana → **"Dashboards" > "Import"**
2. Haz clic en **"Upload JSON file"**
3. Selecciona uno de los archivos:
   - `dashboard-principal.json`: paneles de percentil 90, percentil 95 y promedio de duración de solicitudes.
   - `recursos-sistema.json`: uso de CPU, porcentaje de uso del sistema de ficheros raíz, etc.

---

## ⚙️ Configuración de Prometheus

Tu archivo `prometheus.yml` define los siguientes *jobs*:

```yaml
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'prometheus-service-demo'
    metrics_path: /metrics
    static_configs:
      - targets:
          - prometheus-service-demo-0:8080
          - prometheus-service-demo-1:8080
          - prometheus-service-demo-2:8080

  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']
```

---

## 🟢 Panel especial: sistema de ficheros raíz

En Grafana se incluye un panel **Gauge** que muestra el uso de la raíz (`/etc/hosts` en entornos como WSL), con estos umbrales:

- 🟡 A partir de 80% → Naranja
- 🔴 A partir de 90% → Rojo

---

## 🧹 Cómo detener los servicios

```bash
docker-compose down
```

---

## 📁 Estructura del repositorio

```
practica-prometheus-grafana/
│
├── docker-compose.yml
├── prometheus.yml
├── dashboards/
│   ├── dashboard-principal.json
│   └── recursos-sistema.json
└── README.md
```
- Se ha añadido tambien prometheus.yml para mayor claridad aunque no sea necesario seg
---

## ✍️ Autor

- Ángel Martínez  
- Práctica final de DevOps con Prometheus y Grafana  
- Abril 2025

---

## ✅ Recomendaciones

- Asegúrate de que Docker Desktop tenga permisos suficientes para acceder a los volúmenes si usas Windows o WSL.
- Recuerda usar `host.docker.internal` si accedes a servicios desde Prometheus fuera de la red de Docker.
