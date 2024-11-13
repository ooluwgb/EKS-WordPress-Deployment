
# EKS Monitoring with Prometheus and Grafana

This project provides step-by-step instructions to set up Prometheus and Grafana on an Amazon EKS (Elastic Kubernetes Service) cluster. It configures a robust monitoring stack with Grafana for visualization and Prometheus for metrics collection. The setup includes LoadBalancer services to enable external access to both UIs.

---

## Prerequisites

- An existing Amazon EKS cluster.
- AWS CloudShell access, available in the AWS Management Console.
- kubectl and Helm installed and configured to connect to your EKS cluster.

---

## Steps

### Step 1: Add Helm Repositories for Prometheus and Grafana

In AWS CloudShell, add the official Helm repositories for Prometheus and Grafana:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

---

### Step 2: Deploy Prometheus

We’ll use the **kube-prometheus-stack** Helm chart, which includes Prometheus and related components.

1. **Install Prometheus using Helm**:

   ```bash
   helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace
   ```

2. **Edit Prometheus Service to Use LoadBalancer**:

   After deploying Prometheus, update its service type to LoadBalancer to enable external access.

   ```bash
   kubectl edit svc prometheus-kube-prometheus-prometheus -n monitoring
   ```

   Change the `type` field from `ClusterIP` to `LoadBalancer`:

   ```yaml
   spec:
     type: LoadBalancer
   ```

3. **Wait for the External IP**:

   Check the external IP address of the Prometheus service by running:

   ```bash
   kubectl get svc prometheus-kube-prometheus-prometheus -n monitoring
   ```

   This will show an `EXTERNAL-IP` once the LoadBalancer is provisioned.

---

### Step 3: Deploy Grafana

1. **Install Grafana using Helm**:

   ```bash
   helm install grafana grafana/grafana --namespace monitoring
   ```

2. **Edit Grafana Service to Use LoadBalancer**:

   Similar to Prometheus, update the Grafana service to make it accessible externally:

   ```bash
   kubectl edit svc grafana -n monitoring
   ```

   Change the `type` field to `LoadBalancer`:

   ```yaml
   spec:
     type: LoadBalancer
   ```

3. **Retrieve the Grafana Admin Password**:

   The Grafana admin password is stored in a Kubernetes secret. To retrieve it:

   ```bash
   kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
   ```

4. **Wait for the External IP**:

   Check the external IP address of the Grafana service by running:

   ```bash
   kubectl get svc grafana -n monitoring
   ```

   Use this IP to access the Grafana UI.

---

### Step 4: Configure Prometheus as a Data Source in Grafana

1. **Access the Grafana UI**:
   - Open your browser and navigate to the Grafana LoadBalancer URL.
   - Log in using the username `admin` and the password retrieved in the previous step.

2. **Add Prometheus as a Data Source**:
   - In Grafana, go to **Configuration > Data Sources**.
   - Click **Add data source** and select **Prometheus**.

3. **Configure the Prometheus Data Source**:
   - Set the **URL** to the internal service URL for Prometheus:

     ```
     http://monitoring-kube-prometheus-prometheus.monitoring.svc.cluster.local:9090
     ```

   - Click **Save & Test**.

---

### Step 5: Import Pre-Built Kubernetes Dashboards in Grafana

1. **Go to the Import Dashboard page**:
   - In Grafana, go to **+** (plus icon) and select **Import**.

2. **Import Kubernetes Monitoring Dashboards**:
   - Use the following dashboard IDs to import common Kubernetes dashboards:
     - **Kubernetes Cluster Monitoring**: `315`
     - **Kubernetes / Compute Resources / Node**: `1860`
     - **Kubernetes / Compute Resources / Cluster**: `10000`

3. **Select Prometheus as the Data Source**:
   - For each imported dashboard, select **Prometheus** as the data source.

---

### Step 6: Additional Customization (Optional)

1. **Configure Alerts in Grafana**:
   - Go to **Alerting > Notification channels** to set up alerts for metrics.

2. **Explore Metrics in Prometheus**:
   - Access the Prometheus LoadBalancer URL to run queries directly.

---

### Step 7: Clean Up Resources

To remove Prometheus, Grafana, and associated resources, run:

```bash
helm uninstall prometheus -n monitoring
helm uninstall grafana -n monitoring
kubectl delete namespace monitoring
```

---

## Summary

- **Prometheus and Grafana**: Set up on EKS with LoadBalancer for external access.
- **Pre-Built Dashboards**: Provides monitoring for cluster, nodes, and pods.
- **Data Source Configuration**: Grafana is connected to Prometheus for visualizing metrics.

This setup provides comprehensive monitoring for your EKS cluster, allowing you to access Grafana and Prometheus externally and view metrics via detailed dashboards.
