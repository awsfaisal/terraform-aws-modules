# Terraform AWS Modules Project

This project demonstrates how to use Terraform modules to provision AWS infrastructure.

## Modules Included
- VPC Module
- EC2 Instance Module
- S3 Bucket Module

## Project Structure
.
├── main.tf
├── terraform.tf
├── modules
│ ├── ec2-instances
│ ├── s3-bucket
│ └── vpc

markdown
Copy code

## Prerequisites
- Terraform >= 1.5.0
- AWS CLI configured
- AWS Account

## How to Run
```bash
terraform init
terraform plan
terraform apply
Outputs
EC2 Instance ID

S3 Bucket ARN

yaml
Copy code

---

## 4️⃣ Small Best-Practice Improvements (Optional)

### In `modules/ec2-instances/variables.tf`
```hcl
variable "ami" {
  description = "AMI ID for EC2"
}

variable "instance_type" {
  description = "EC2 instance type"
}

variable "subnet_id" {
  description = "Subnet ID for EC2"
}

