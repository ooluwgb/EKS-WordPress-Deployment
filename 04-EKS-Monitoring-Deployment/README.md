
# EKS Monitoring with Prometheus and Grafana (Using values.yaml)

This guide provides step-by-step instructions to set up Prometheus and Grafana on an Amazon EKS (Elastic Kubernetes Service) cluster using custom configuration values in a `values.yaml` file. This setup configures both services with LoadBalancer access for external UIs and includes enhanced monitoring capabilities for a more comprehensive view of your cluster.

---

## Prerequisites

- An existing Amazon EKS cluster.
- AWS CloudShell access with **kubectl** and **Helm** installed.
- AWS CLI configured to connect to your EKS cluster.

---

## Steps

### Step 1: Add Helm Repositories for Prometheus and Grafana

Add the official Helm repositories for Prometheus and Grafana:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

---

### Step 2: Create the `values.yaml` Configuration

Create a `values.yaml` file to configure Prometheus and Grafana with LoadBalancer services and enhanced monitoring.

1. **Create a file named `prometheus-grafana-values.yaml`** with the following content:

   ```yaml
   prometheus:
     service:
       type: LoadBalancer  # Expose Prometheus on the internet
     prometheusSpec:
       serviceMonitorSelector:
         matchLabels: {}
       podMonitorSelector:
         matchLabels: {}

   grafana:
     service:
       type: LoadBalancer  # Expose Grafana on the internet
     adminPassword: "admin"  # Set Grafana admin password (change for security)

   kube-state-metrics:
     enabled: true  # Enables Kubernetes object metrics (deployments, pods, nodes)

   nodeExporter:
     enabled: true  # Enables node-level metrics (CPU, memory, disk)

   alertmanager:
     enabled: false  # Disables Alertmanager if not needed
   ```

   This configuration sets both Prometheus and Grafana services to `LoadBalancer` for external access and enables additional metrics collection.

---

### Step 3: Deploy Prometheus and Grafana Using Helm

Deploy the monitoring stack with the custom `values.yaml` file:

```bash
helm install monitoring prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace -f prometheus-grafana-values.yaml
```

This installs Prometheus, Grafana, Node Exporter, and Kube State Metrics with LoadBalancer access and custom configurations.

---

### Step 4: Retrieve the External IPs for Prometheus and Grafana

1. **Check the LoadBalancer IPs for both Prometheus and Grafana**:

   ```bash
   kubectl get svc -n monitoring
   ```

2. Find the `EXTERNAL-IP` entries for both `prometheus-kube-prometheus-prometheus` and `monitoring-grafana`. Use these IPs to access the UIs in your browser.

---

### Step 5: Access Grafana and Add Prometheus as a Data Source

1. **Access the Grafana UI**:
   - Go to the Grafana LoadBalancer URL in your browser.
   - Log in with **username**: `admin` and **password**: `admin` (or the password you set in the values file).

2. **Add Prometheus as a Data Source in Grafana**:
   - In Grafana, go to **Configuration > Data Sources**.
   - Click **Add data source** and select **Prometheus**.
   - Set the **URL** to `http://monitoring-kube-prometheus-prometheus.monitoring.svc.cluster.local:9090`.
   - Click **Save & Test**.

---

### Step 6: Import Kubernetes Dashboards in Grafana

1. **Import Pre-Built Dashboards**:
   - Go to **+** (plus icon) > **Import** in Grafana.

2. **Use the following dashboard IDs** to import popular Kubernetes dashboards:
   - **Kubernetes Cluster Monitoring**: `315`
   - **Kubernetes / Compute Resources / Node**: `1860`
   - **Kubernetes / Compute Resources / Cluster**: `10000`

3. **Set Prometheus as the Data Source** when prompted.

---

### Step 7: Optional Customization

1. **Set Up Alerts in Grafana** (optional):
   - Go to **Alerting > Notification channels** in Grafana to configure alerts.

2. **Explore Metrics in Prometheus**:
   - Use the Prometheus LoadBalancer URL to explore and test queries directly.

---

### Step 8: Clean Up Resources

To remove Prometheus, Grafana, and related resources, run:

```bash
helm uninstall monitoring -n monitoring
kubectl delete namespace monitoring
```

---

## Summary

- **Prometheus and Grafana**: Deployed on EKS with external access enabled.
- **Pre-Built Dashboards**: Includes Kubernetes cluster monitoring for nodes, pods, and namespaces.
- **Data Source Configuration**: Prometheus configured as a data source in Grafana.

This setup provides detailed monitoring for your EKS cluster, accessible via external UIs, and is designed to capture a broad range of Kubernetes metrics.
