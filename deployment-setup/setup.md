Step-by-step deployment guide for terraform-aws-modules
(Terraform Modules PROJECT):
--------

Create modules directory with instance, bucket, vpc.

├── main.tf
├── modules
│   ├── ec2-instances
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tfterraform-aws-modules

│   ├── s3-bucket
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   └── vpc
│       ├── main.tf
│       ├── outputs.tf
│       └── variables.tf
├── terraform.tfstate


-- yum install -y tree

-- mkdir -p modules/ec2-instances/

-- mkdir -p modules/s3-bucket/

-- mkdir -p modules/vpc/


git clone https://github.com/ReyazShaik/terraform-modules.git

cd terraform-modules

-- cd modules/ec2-instances/

vi variables.tf

variable "ami" {}
variable "instance_type" {}
variable "subnet_id" {}

 
vi main.tf

resource "aws_instance" "this" {
  ami           = var.ami
  instance_type = var.instance_type
  subnet_id     = var.subnet_id

  tags = {
    Name = "ExampleEC2"
  }
}



vi outputs.tf

output "instance_id" {
  value = aws_instance.this.id
}





-- cd modules/s3-bucket/
 
vi variables.tf

variable "bucket_name" {}


vi main.tf

resource "aws_s3_bucket" "this" {
  bucket        = var.bucket_name
  force_destroy = true
}


vi outputs.tf

output "bucket_arn" {
  value = aws_s3_bucket.this.arn
}



-- cd modules/vpc/
 

vi variables.tf

variable "cidr_block" {}


vi main.tf

resource "aws_vpc" "main" {
  cidr_block = var.cidr_block
}

resource "aws_subnet" "public" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "ap-south-1a"
}


vi outputs.tf

output "vpc_id" {
  value = aws_vpc.main.id
}

output "public_subnet_id" {
  value = aws_subnet.public.id
}


-- Be outside modules directory

vi main.tf

provider "aws" {
  region = "ap-south-1"
}

module "vpc" {
  source     = "./modules/vpc"
  cidr_block = "10.0.0.0/16"
}

module "ec2" {
  source        = "./modules/ec2-instances"
  ami           = "ami-02ddb77f8f93ca4ca"       # Amazon Linux 2023 in ap-south-1
  instance_type = "t3.micro"
  subnet_id     = module.vpc.public_subnet_id  # public_subnet_id is a output of VPC module output.tf. Output of VPC module is the input here
}

module "s3" {
  source      = "./modules/s3-bucket"
  bucket_name = "my-demo-bucket-bkt-test-1"     # Change to a unique bucket name
}

output "instance_id" {
  value = module.ec2.instance_id  #Output.tf from EC2 module. In ec2-instance module, output.tf has instance_id. As we use modules
                                  we should use output of ec2 module. if you use .id it will print id of module "ec2" as ec2.
}

output "bucket_arn" {
  value = module.s3.bucket_arn
}





-- tree

-- terraform init

-- terraform apply --auto-approve

-- terraform destroy --auto-approve
