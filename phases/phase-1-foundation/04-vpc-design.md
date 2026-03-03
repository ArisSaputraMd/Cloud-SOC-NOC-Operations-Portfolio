<!-- >Restrict bastion SG to your IP only"Bastion for lab access; in production, I'd use SSM to eliminate public SSH."
> "Designed for multi-AZ; implemented single AZ to save costs/time."
>App → DB (e.g., 3306/5432) — ensure SG allows only from app SG -->

## VPC Design

![vpc-design-diagram.png](../../assets/diagrams/vpc-design-diagram.png)

To simulate both SOC and NOC operation, I've created secure VPC custom with proper network segmentation and core component. VPC is Designed for multi-AZ but implemented single AZ to save costs/time. Below is VPC and it's feature I've created:

**SOC Lab VPC**

I've created VPC with tag name= SOC-Lab-VPC and IPv4 CIDR 10.0.1.0/16. To support Production environment I choose to use wide range of CIDR block, with /16 block this VPC can hold up to 65534 Host/Net. It will give enough ip for scalilbility and future defelopment.

![soc-lab-vpc](../../assets/screenshot/phase-1/SOC-lab-vpc.png)

**Subnets**

For poper segmentation I've seperated Public and Private subnet Public subnet will be used to hold internet-facing instances. Pivate subnets will hold instances without internet indound accesses. With detail:

- Public subnet (10.0.1.0/24) - (ALB and NAT gateway).
- Privete Web/App subnets (10.0.2.0/24) - responsible to hold Web/App server. **Recomended to seperate Web and App layer for production**. I've simplified it to save costs/time.
- Private Database subnet (10.0.3.0/24) - Dedicated subnet to hold database instances.
- Monitoring subnet (10.0.4.0/24) - private subnet for central Monitoring and Incident Response instance.

![subnets.png](../../assets/screenshot/phase-1/subnes.png)

**Internet Gateway**

Internet Gateway is AWS instance that handle igreess/outgreess of an VPC traffic. Without internet gateway all instances within VPC wouln't be able to communicate with the internet. I've created IGW and attach it to SOC-Lab-VPC.

![internet-gateway.png](../../assets/screenshot/phase-1/internet-gateway.png)

**NAT Gateway**

I've created NAT Gateway in my public subnet, add my privte IPv4 address (10.0.1.20) and Elastic IP. NAT gateway will allow my instances in my private subnets to access the internet as outbound only, usefull for downloading or update update.

![nat-gateway](../../assets/screenshot/phase-1/nat-gateway.png)
