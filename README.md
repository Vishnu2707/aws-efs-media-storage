## Scalable Media Storage Solution with AWS EFS and EC2 Instances

This repository demonstrates a scalable, secure, and highly available media storage solution using **AWS Elastic File System (EFS)** and **Amazon EC2** instances. The solution is designed to cater to businesses requiring seamless file sharing, high availability, and encryption for secure data transmission.

## 🛠️ Business Scenario
A media production firm needed:
- A centralized, scalable storage solution for large video files and media assets.
- Shared access for multiple EC2 instances across different Availability Zones (AZs).
- High availability, fault tolerance, and top-tier performance.
- Secure data transmission for compliance.

AWS EFS was chosen to meet these requirements due to its elasticity, high availability, and easy integration with EC2 instances.

---

## 🏗️ Solution Overview

### 1. Launching EC2 Instances in Multiple AZs
I created two EC2 instances in different AZs for high availability and redundancy.

#### Steps:
1. **Create a Security Group** for secure communication:
    ```bash
    aws ec2 create-security-group --group-name StorageLabs --description "SG for EFS storage"
    aws ec2 authorize-security-group-ingress --group-name StorageLabs --protocol tcp --port 22 --cidr 0.0.0.0/0
    ```

2. **Launch EC2 Instances** in different AZs:
    ```bash
    # Instance in us-east-1a
    aws ec2 run-instances --image-id <copy-ami-imageid> --instance-type t2.micro --placement AvailabilityZone=us-east-1a --security-group-ids <copy-sg-id>

    # Instance in us-east-1b
    aws ec2 run-instances --image-id <copy-ami-imageid> --instance-type t2.micro --placement AvailabilityZone=us-east-1b --security-group-ids <copy-sg-id>    ```

---

### 2. Creating and Configuring AWS EFS
The **AWS EFS** was set up to allow shared access between the EC2 instances.

#### Steps:
1. **Create the EFS** using the AWS console.
2. **Configure NFS Rule**:
    ```bash
    aws ec2 authorize-security-group-ingress --group-id <copy-sg-id> --protocol tcp --port 2049 --source-group <copy-sg-id>
    ```

3. **Mount the EFS** on both EC2 instances:
    ```bash
    # Install NFS Client
    sudo yum -y install nfs-utils

    # Create a Mount Point
    mkdir ~/efs-mount-point

    # Mount the EFS
    sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport <copy-dns-id-efs>:/ ~/efs-mount-point
    ```

---

### 3. Enabling Encryption in Transit
We enforced encryption for secure data transmission between EC2 instances and EFS.

#### Steps:
1. **Set File System Policy** to enforce encryption in transit.
2. **Use Amazon EFS Utils** for TLS-enabled mounting:
    ```bash
    sudo yum install -y amazon-efs-utils
    sudo mount -t efs -o tls :/ ~/efs-mount-point
    ```

---

## 🎯 Outcome
- **High Availability**: Fault tolerance through multi-AZ setup.
- **Scalable Storage**: Elastic scaling with AWS EFS.
- **Secure Transmission**: Encryption in transit using EFS Utils.
- **Effortless Collaboration**: Shared access for production teams across AZs.

---

## 📚 Resources
- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS EFS Documentation](https://docs.aws.amazon.com/efs/)
- [Amazon EFS Utils GitHub](https://github.com/aws/efs-utils)

---

## 🏗️ Project Structure
