<!-- >Restrict bastion SG to your IP only"Bastion for lab access; in production, I'd use SSM to eliminate public SSH."
> "Designed for multi-AZ; implemented single AZ to save costs/time."
>App → DB (e.g., 3306/5432) — ensure SG allows only from app SG -->

## VPC Design

![vpc-design-diagram.png](../../assets/diagrams/vpc-design-diagram.png)

To simulate both SOC and NOC operation, I've created secure VPC custom with proper network segmentation and core component. VPC is Designed for multi-AZ but implemented single AZ to save costs/time. Below is VPC and it's feature I've created:

**SOC Lab VPC**

I've created VPC with tag name= SOC-Lab-VPC and IPv4 CIDR 10.0.1.0/16. To support Production environment I choose to use wide range of CIDR block, with /16 block this VPC can hold up to 65534 Host/Net. It will give enough ip for scalilbility and future defelopment.

![soc-lab-vpc](../../assets/screenshot/phase-1/SOC-lab-vpc.png)
