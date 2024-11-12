
# Setting up Helm on AWS CloudShell and Installing AWS EFS CSI Driver

This guide provides step-by-step instructions to set up Helm on AWS CloudShell, connect to your EKS cluster, deploy WordPress with an EFS-backed storage configuration, and finally clean up all resources.

---

## Prerequisites

- An existing Amazon EKS cluster.
- AWS CloudShell access, available in the AWS Management Console.
- kubectl installed and configured to connect to your EKS cluster.

---

## Steps

### 1. Connect to the EKS Cluster from AWS CloudShell

1. **Open AWS CloudShell**:
   - In the AWS Management Console, click the **CloudShell** icon in the top navigation bar.
2. **Configure kubectl for EKS**:
   - Run the following command to retrieve the kubeconfig for your cluster:

     ```bash
     aws eks update-kubeconfig --region <your-region> --name <your-cluster-name>
     ```

   - Replace `<your-region>` and `<your-cluster-name>` with your AWS region and EKS cluster name.
3. **Verify Connection**:
   - Ensure that you can connect to the cluster by listing all pods:

     ```bash
     kubectl get pods --all-namespaces
     ```

**Result**: You should now be connected to your EKS cluster and able to interact with it via `kubectl`.

---

### 2. Set up Helm on AWS CloudShell

1. **Install Helm**:
   - Run the following command to download and install Helm:

     ```bash
     curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
     ```

2. **Verify Helm Installation**:
   - Confirm that Helm is installed by checking the version:

     ```bash
     helm version
     ```

**Result**: Helm should be successfully installed in your AWS CloudShell environment.

---

### 3. Add the EFS CSI Driver Helm Repository

1. **Add the EFS CSI Driver repository**:

   ```bash
   helm repo add aws-efs-csi-driver https://kubernetes-sigs.github.io/aws-efs-csi-driver/
   ```

2. **Update Helm repositories**:

   ```bash
   helm repo update
   ```

**Result**: The Helm repository for the AWS EFS CSI Driver will be added and updated.

---

### 4. Install the AWS EFS CSI Driver

1. **Install the EFS CSI Driver**:
   - Use Helm to install the EFS CSI Driver in the `kube-system` namespace:

     ```bash
     helm install aws-efs-csi-driver aws-efs-csi-driver/aws-efs-csi-driver --namespace kube-system
     ```

2. **Verify Installation**:
   - Check if the EFS CSI Driver pods are running in the `kube-system` namespace:

     ```bash
     kubectl get pods -n kube-system -l app=efs-csi-controller
     ```

**Result**: The AWS EFS CSI Driver should be installed successfully in your EKS cluster.

---

### 5. Deploy WordPress with EFS Storage on EKS

Apply each configuration file in the following order to deploy WordPress on your EKS cluster.

1. **Create the EFS StorageClass**:

   ```bash
   kubectl apply -f 01-efs-storageclass.yaml
   ```

2. **Create the EFS Persistent Volume**:

   ```bash
   kubectl apply -f 02-efs-pv.yaml
   ```

3. **Create the EFS Persistent Volume Claim**:

   ```bash
   kubectl apply -f 03-efs-pvc.yaml
   ```

4. **Create MySQL Persistent Volume**:

   ```bash
   kubectl apply -f 04-mysql-pv.yaml
   ```

5. **Create MySQL Persistent Volume Claim**:

   ```bash
   kubectl apply -f 05-mysql-pvc.yaml
   ```

6. **Deploy MySQL**:

   ```bash
   kubectl apply -f 06-mysql-deployment.yaml
   ```

7. **Create MySQL Service**:

   ```bash
   kubectl apply -f 07-mysql-service.yaml
   ```

8. **Deploy WordPress**:

   ```bash
   kubectl apply -f 08-wordpress-deployment.yaml
   ```

9. **Create WordPress Service**:

   ```bash
   kubectl apply -f 09-wordpress-service.yaml
   ```

**Result**: WordPress will be deployed on EKS, with MySQL as the database and EFS as the persistent storage. You can access the WordPress application via the external IP of the LoadBalancer service created for WordPress.

---

### 6. Deleting All Resources

To delete the WordPress setup, remove each resource in the reverse order:

```bash
kubectl delete -f 09-wordpress-service.yaml
kubectl delete -f 08-wordpress-deployment.yaml
kubectl delete -f 07-mysql-service.yaml
kubectl delete -f 06-mysql-deployment.yaml
kubectl delete -f 05-mysql-pvc.yaml
kubectl delete -f 04-mysql-pv.yaml
kubectl delete -f 03-efs-pvc.yaml
kubectl delete -f 02-efs-pv.yaml
kubectl delete -f 01-efs-storageclass.yaml
```

**Result**: All resources related to the WordPress and MySQL deployment will be deleted from your EKS cluster.

---

## Summary

- **Helm**: Installed on AWS CloudShell for package management in Kubernetes.
- **AWS EFS CSI Driver**: Installed on EKS, allowing EFS to be used as persistent storage.
- **WordPress Deployment**: Configured to use MySQL as its database and EFS as persistent storage.

This setup enables EKS to utilize EFS for WordPress and provides a straightforward method for deploying and removing resources in your cluster.
