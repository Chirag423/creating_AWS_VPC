# cloud_computing_sem_VI
# AWS VPC Setup with Public and Private Subnets Across Two Availability Zones

This README file provides step-by-step instructions to set up an AWS VPC with public and private subnets across two Availability Zones (AZs), and the configuration of Internet and Virtual Private Gateways for secure communication.

---

## Architecture Overview

### VPC Configuration
- **CIDR Block**: `10.0.0.0/24`

### Subnet Configuration
- **Zone A**:
  - **Public Subnet 1**: `10.0.0.0/26`
  - **Private Subnet 1**: `10.0.0.128/26`
  
- **Zone B**:
  - **Public Subnet 2**: `10.0.0.64/26`
  - **Private Subnet 2**: `10.0.0.192/26`

### Gateways
- **Internet Gateway (IGW)**: Connects Public Subnets to the internet.
- **Virtual Private Gateway (VPG)**: Connects Private Subnets securely.

---

## Definitions

- **Virtual Private Cloud (VPC)**: A logically isolated section of AWS cloud for launching resources.
- **Subnets**: Subdivisions of VPC allowing grouping by IP range. They can be public (internet-accessible) or private (isolated).
- **Internet Gateway (IGW)**: Allows public subnets to access the internet.
- **Virtual Private Gateway (VPG)**: Facilitates secure communication between private subnets and on-premises networks.
- **Route Table**: Rules for directing traffic in a network.
- **Availability Zone (AZ)**: A physically isolated location within an AWS Region designed for high availability.

---

## Step-by-Step Instructions

### Step 1: Create the VPC

1. **Log in to AWS Management Console.**
2. **Navigate to VPC Dashboard** → Click **Create VPC**:
   - **Name**: `My-VPC`
   - **IPv4 CIDR Block**: `10.0.0.0/24`
3. **Click Create VPC**.

### Step 2: Create Subnets

#### Zone A
1. **Public Subnet 1**:
   - **Availability Zone**: `us-east-1a`
   - **CIDR Block**: `10.0.0.0/26`
   - **Click Create Subnet**.
2. **Private Subnet 1**:
   - **Availability Zone**: `us-east-1a`
   - **CIDR Block**: `10.0.0.128/26`
   - **Click Create Subnet**.

#### Zone B
1. **Public Subnet 2**:
   - **Availability Zone**: `us-east-1b`
   - **CIDR Block**: `10.0.0.64/26`
   - **Click Create Subnet**.
2. **Private Subnet 2**:
   - **Availability Zone**: `us-east-1b`
   - **CIDR Block**: `10.0.0.192/26`
   - **Click Create Subnet**.

### Step 3: Create an Internet Gateway (IGW)

1. **Navigate to Internet Gateways** → Click **Create Internet Gateway**.
2. **Name**: `My-IGW`
3. **Click Create** → Attach it to `My-VPC`.

### Step 4: Create a Virtual Private Gateway (VPG)

1. **Navigate to Virtual Private Gateways** → Click **Create Virtual Private Gateway**.
2. **Name**: `My-VPG`
3. **Leave ASN as default** → Click **Create**.
4. **Attach the VPG to `My-VPC`**.

### Step 5: Configure Route Tables

#### Public Route Table
1. **Create Route Table**: Name: `Public-Route-Table`
2. Add route for internet traffic:
   - **Destination**: `0.0.0.0/0`
   - **Target**: Select `My-IGW`
3. **Associate Public Subnets**: `Public-Subnet-1`, `Public-Subnet-2`

#### Private Route Table
1. **Create Route Table**: Name: `Private-Route-Table`
2. Enable **Route Propagation** for VPG.
3. **Associate Private Subnets**: `Private-Subnet-1`, `Private-Subnet-2`.

### Step 6: Launch EC2 Instances

#### Public Subnets
1. **Launch EC2 in Public-Subnet-1**:
   - Select an AMI (e.g., Amazon Linux 2)
   - Enable **Public IP Address**
2. Repeat the process for **Public-Subnet-2**.

#### Private Subnets
1. **Launch EC2 in Private-Subnet-1**:
   - Disable **Auto-assign Public IP**.
2. Repeat the process for **Private-Subnet-2**.

---

## Configure a Security Group for SSH Access

### Step 1: Create Security Group
1. Navigate to **Security Groups** under **Network & Security**.
2. **Click Create security group**.
3. Set **Name**: `Private-SSH-Access` and **Description**: `Allows SSH access from specific IP`.

### Step 2: Configure Inbound Rules
- **Type**: SSH
- **Port Range**: 22
- **Source**: Choose IP or CIDR block (e.g., `My IP`, `203.0.113.25/32`).

### Step 3: Attach Security Group
1. **Navigate to EC2 Dashboard**.
2. **Select EC2 instance**.
3. **Click Actions** → **Security** → **Change security groups** → Select `Private-SSH-Access` → **Save**.

---

## Testing and Verification

1. **Verify Public Instances**: Ensure they have internet access via public IP.
2. **Verify Private Instances**: Ensure communication occurs securely via the VPG.
3. **Test Connectivity**: Use **SSH** or **ping** to verify inter-instance communication.

---

By following the instructions outlined above, you should be able to create a secure and highly available AWS network with public and private subnets across two Availability Zones.
