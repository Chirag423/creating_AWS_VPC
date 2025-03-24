# AWS-VPC-Creation-and-Connection-

### Here we are going to see how to create a VPC(Virtual Private Cloud) and then how to connect it to your on premise personal computer using a site to site VPN
---

## Step 1 : Creating VPC
- Open your AWS account and navigate to VPC section and click on create VPC. It will open a window as shown below, we will opt for VPC only option since we are here to learn (VPC and more will create all the requirements for VPC automatically)
  ![Screenshot from 2025-03-11 16-08-04](https://github.com/user-attachments/assets/41d5b5c1-769e-4c12-a49a-a3d36e357c58)
- Give it a name via name tag option
- You can type in your own CIDR block or you can go ahead with the default CIDR that AWS gives (for our purpose we will go ahead with the default CIDR 10.0.0.0/24, which is a private IPv4 address) and click on create VPC

## Step 2 : Creating Subnets
- Navigate to subnets section and click on create subnet
- For learning purpose We are going to create 4 subnets in 2 availability zones as follows:
  - 1st Availability zone: 1 private subnet and 1 public subnet
  - 2nd Availability zone: 1 private subnet and 1 public subnet
    ![Screenshot from 2025-03-11 16-25-47](https://github.com/user-attachments/assets/ad3ba440-3fc8-4ce2-b957-3d151e946467)
- Create 4 sub-netwroks from your VPC CIDR block as follows:
  - VPC CIDR : 10.0.0.0/24, to create 4 sub networks out of this we wil use a /26 Subnet Mask creating 4 subnets with 64 IPs each
  - Subnet CIDR Blocks : 10.0.0.0/26, 10.0.0.64/26, 10.0.0.128/26, 10.0.0.192/26
    ![Screenshot from 2025-03-11 16-22-31](https://github.com/user-attachments/assets/8a9999e6-5384-400a-a408-64afb2cdecc7)

## Step 3 : Creating Route Tables and an Internet Gateway (Igw)
- Now we need to create a route for the traffic to travel on in our VPC for which we will create a route table.
- Navigate to route tables section and create a route table for each type of subnet we created in the previous step
  ![Screenshot from 2025-03-11 16-29-33](https://github.com/user-attachments/assets/f72ffab3-f533-4d0a-8ca6-d9230e85a8f4)
- We need to edit the routes on both the table according to our network's need
- We are going to make sure that the our public subnets can exchnage traffic with the Internet and the private subnets do not have direct exchange with the internet
- To give VPC internet connectivity we need an Internet gateway so we navigate to Igw section and create one. Once created click on it and attach it to our vpc
- Now we are going to edit our route tables:
 - Route tables follow the Longest Prefix Match rule(longest subnet mask match), i.e. among multiple routes the one with smallest subnet(longest prefix) will take priority.
 - Therefore public subnet wil have the following two rules:
    - 10.0.0.0/24 --> local # anything addressed to local ip stays within VPC(24 bit prefix)
    - 0.0.0.0/0   --> Igw   # everything goes to internet via Igw(No prefix)
      ![Screenshot from 2025-03-11 16-42-27](https://github.com/user-attachments/assets/53035d07-ed66-4c24-a64c-dccfce9c540c)
 - For security reasons private subnet are given indirect access to internet through NAT or VGW, we will use VGW(though the more general method is to use NAT). 
 - we will edit private route table once again when we create vgw in the next step for now private subnet rules are :
    - 10.0.0.0/24 --> local
- Now we associate these route tables with appropriate subnets, the public and private subnets in both availability zone will be associated to the public and private route tables respectively
    ![Screenshot from 2025-03-11 16-46-12](https://github.com/user-attachments/assets/68f61d0c-8360-4716-95cc-83b39c336795)
- Once done the VPC resource map will look like:
    ![Screenshot from 2025-03-11 16-47-47](https://github.com/user-attachments/assets/5e56f78d-faad-4860-a8c6-a463d5828677)

## Step 4 : Creating Virtual Private Gateway (Vgw)
- Vgw is one of the end points of site to site VPN & is used to connect VPC to an on premise data center but for this tutorial we will be using it to connect the VPC to our personal computer
- We will make sure the whenever any instance or service in private subnet wants to access internet it can do so securely through the on premise data center(in this case our laptop) that has internet access rather than directly accessing the internet. Hence our laptop will act as bi-directional NAT or default gateway to private subnet of VPC.
- Navigate to Virtual Private Gateways section and click on create virtual private gateway and create it with default ASN
- Once created attach it to the VPC(notice that it will not appear in VPC resource map even after it's attached to the VPC)
    ![Screenshot from 2025-03-11 16-58-26](https://github.com/user-attachments/assets/31a7f252-9568-4892-ade0-08b42eb1343f)
- Now we edit the private route table again to route the traffic intended for exchange with other networks to Vgw
  - New rule : 0.0.0.0/0 --> vgw
    ![Screenshot from 2025-03-11 17-00-06](https://github.com/user-attachments/assets/49901449-8af5-47be-8f8f-bccd862f88c2)

## Step 5 : Connecting to VPC using Site to Site VPN
- With our VPC setup it's time we start on the connection part, for which we are going to use AWS site to site VPN service.
- A site to site VPN is used to securely connect two networks, in our case our AWS VPC and our pc sitting on our private LAN.
- It has seeveral components engaged in it, the main components are:
   - AWS side : VPC(along with it's components) & Vgw
   - On-prem side : Customer gateway & VPN Connection

## Step A : Creating a Customer Gateway
- Navigate to customer gateways section and click on create customer gateway, use the default value for BGP ASN, insert you PC's public IPv4 address in IP adress space (you can find it by searching "my public ip" on google and make sure it's static once customer gateway is connected to it)
  ![Screenshot from 2025-03-11 18-17-16](https://github.com/user-attachments/assets/1588a869-fca7-4e86-a834-8d61f43df08e)
- 


