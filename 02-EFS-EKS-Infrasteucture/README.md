
# Setting up EFS Storage for an EKS Cluster

This guide provides step-by-step instructions for creating Amazon EFS (Elastic File System) and configuring it for use with an Amazon EKS (Elastic Kubernetes Service) cluster. It includes instructions for creating IAM policies and roles, setting up security groups, and configuring mount targets for EFS.

## Prerequisites

- An existing Amazon EKS cluster.
- AWS CLI installed and configured.
- An IAM policy file, `Eks-Node-Access-to-EFS`, already created. This file should contain the following JSON configuration:

\`\`\`json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "elasticfilesystem:DescribeFileSystems",
                "elasticfilesystem:DescribeMountTargets",
                "elasticfilesystem:CreateAccessPoint",
                "elasticfilesystem:DeleteAccessPoint",
                "elasticfilesystem:DescribeAccessPoints"
            ],
            "Resource": "*"
        }
    ]
}
\`\`\`

## Steps

---

### 1. Create the EFS File System

1. Go to the [Amazon EFS Console](https://console.aws.amazon.com/efs).
2. Click **Create file system** and select the **VPC** where your EKS cluster resides.
3. Choose **General Purpose** for the Performance mode and configure any additional settings as required.
4. Take note of the EFS ID (e.g., \`fs-xxxxxx\`), as it will be needed for later configuration.

---

### 2. Create Mount Targets

To make the EFS accessible to your EKS nodes, create mount targets in each availability zone where your EKS nodes are deployed.

1. In the EFS console, select your file system.
2. Under **Network** settings, add **Mount targets** in each availability zone.
3. Select subnets within the same VPC as your EKS cluster.
4. For each mount target, select the appropriate security group (configured in the next section) to allow network access from the EKS nodes.

---

### 3. Create Security Group for EFS

1. Go to the [VPC Console](https://console.aws.amazon.com/vpc/) and create a new security group for EFS.
2. Allow inbound traffic on the NFS port (2049) for the security group associated with your EKS nodes.
3. Attach this security group to the EFS mount targets to enable communication between EKS nodes and EFS.

Example security group rule:

- **Type**: NFS
- **Protocol**: TCP
- **Port Range**: 2049
- **Source**: Security group associated with EKS nodes

---

### 4. Attach the IAM Policy to the EKS Node Role

To allow EKS nodes to access EFS, attach the IAM policy from the `Eks-Node-Access-to-EFS` file to the IAM role associated with your EKS nodes.

1. Open the [IAM Console](https://console.aws.amazon.com/iam/).
2. Find the IAM role associated with your EKS worker nodes.
3. Attach the `Eks-Node-Access-to-EFS` policy to this IAM role.

---

### 5. Configure EFS in EKS

Once the EFS file system is set up and accessible, you can configure Kubernetes resources like **Persistent Volumes (PV)** and **Persistent Volume Claims (PVC)** to use EFS within your EKS cluster.

---

## Summary of Required Resources

- **EFS File System**: Provides the persistent storage for applications running on EKS.
- **IAM Policy**: Grants EKS nodes access to the EFS API.
- **Security Groups**: Allow communication over NFS between EKS nodes and EFS.
- **Mount Targets**: Make EFS accessible within each availability zone where EKS nodes are running.

This setup enables Amazon EKS to utilize Amazon EFS as a scalable, persistent storage solution for applications running within the cluster.
