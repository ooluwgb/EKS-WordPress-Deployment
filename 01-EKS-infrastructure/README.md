
# Creating an EKS Cluster on AWS Console

This guide provides step-by-step instructions to create an Amazon EKS (Elastic Kubernetes Service) cluster on the AWS Management Console. It includes the necessary IAM roles and security groups required for the EKS cluster and its nodes.

---

## Prerequisites

- AWS Management Console access.
- Permissions to create IAM roles, security groups, and EKS resources.
- AWS CLI (optional, for validation steps).

---

## Steps

### 1. Create an IAM Role for EKS Cluster

1. Go to the [IAM Console](https://console.aws.amazon.com/iam/).
2. Select **Roles** > **Create role**.
3. Choose **EKS** as the service and select **EKS - Cluster** as the use case.
4. Click **Next: Permissions**. AWS will automatically select the **AmazonEKSClusterPolicy**.
5. Click **Next: Tags** (optional), then **Next: Review**.
6. Enter a role name (e.g., `EKS-Cluster-Role`) and click **Create role**.

**Result**: This role will be used by the EKS control plane for accessing AWS resources.

---

### 2. Create an IAM Role for EKS Worker Nodes

1. Go back to the [IAM Console](https://console.aws.amazon.com/iam/).
2. Select **Roles** > **Create role**.
3. Choose **EC2** as the service, as worker nodes run as EC2 instances.
4. Click **Next: Permissions** and attach the following policies:
   - **AmazonEKSWorkerNodePolicy**
   - **AmazonEC2ContainerRegistryReadOnly**
   - **AmazonEKS_CNI_Policy**
5. Click **Next: Tags** (optional), then **Next: Review**.
6. Enter a role name (e.g., `EKS-Worker-Node-Role`) and click **Create role**.

**Result**: This role provides permissions for the worker nodes to communicate with the EKS cluster and access required AWS services.

---

### 3. Create Security Groups

#### EKS Control Plane Security Group

1. Go to the [VPC Console](https://console.aws.amazon.com/vpc/) and select **Security Groups** > **Create Security Group**.
2. Name the security group (e.g., `EKS-Control-Plane-SG`) and attach it to the VPC where you plan to create your EKS cluster.
3. Add the following inbound rules to allow control plane communication:
   - **Type**: HTTPS; **Port Range**: 443; **Source**: Your preferred IP range (or `0.0.0.0/0` for public access)
   - **Type**: Custom TCP; **Port Range**: 10250-10255; **Source**: Worker node security group ID
4. Click **Create security group**.

#### EKS Worker Node Security Group

1. Return to **Security Groups** and select **Create Security Group**.
2. Name the security group (e.g., `EKS-Worker-Node-SG`) and attach it to the same VPC.
3. Add the following inbound rules:
   - **Type**: All traffic; **Source**: EKS control plane security group ID
   - **Type**: All traffic; **Source**: EKS worker node security group ID (for inter-node communication)
   - **Type**: SSH; **Port Range**: 22; **Source**: Your preferred IP range (if you need SSH access)
4. Click **Create security group**.

---

### 4. Create the EKS Cluster

1. Go to the [EKS Console](https://console.aws.amazon.com/eks/).
2. Click **Add cluster** > **Create**.
3. **Cluster name**: Enter a name for your cluster (e.g., `my-eks-cluster`).
4. **Kubernetes version**: Select your desired Kubernetes version.
5. **Role**: Choose the IAM role you created in Step 1 (`EKS-Cluster-Role`).
6. **Networking**:
   - Select the VPC and subnets where you want the cluster to be accessible.
   - Choose the security group you created for the EKS control plane (`EKS-Control-Plane-SG`).
7. Configure any additional options as needed, then click **Create**.

**Result**: The EKS cluster will be created, and it may take a few minutes to complete.

---

### 5. Add Worker Nodes to the Cluster

1. Go to the **Compute** section in the EKS Console, under the cluster you just created.
2. Select **Add Node Group**.
3. **Name**: Enter a name for the node group (e.g., `my-node-group`).
4. **Node IAM Role**: Select the IAM role created for the worker nodes (`EKS-Worker-Node-Role`).
5. Configure the instance types, scaling configuration, and desired capacity.
6. Choose the VPC and subnets where the nodes should run (usually the same as the cluster).
7. **Security Group**: Select the security group for worker nodes (`EKS-Worker-Node-SG`).
8. Click **Create**.

**Result**: This will launch worker nodes in the selected subnets and automatically connect them to the EKS cluster.

---

## Summary of Resources Created

- **EKS Cluster**: The core Kubernetes cluster running on AWS.
- **IAM Roles**:
  - **EKS-Cluster-Role**: For the EKS control plane.
  - **EKS-Worker-Node-Role**: For worker nodes to interact with AWS services.
- **Security Groups**:
  - **EKS-Control-Plane-SG**: Allows traffic necessary for the EKS control plane.
  - **EKS-Worker-Node-SG**: Allows traffic between worker nodes and the control plane.

---

This setup ensures that your EKS cluster and worker nodes have the required permissions and network configurations to communicate securely and effectively within AWS.
