# terraform vpc with 2 public subnet
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.59.0"
    }
  }
}

provider "aws" {
  region = "us-east-2"
  access_key = ""
  secret_key = ""    
}

resource "aws_vpc" "vpc" {
    cidr_block = "192.168.0.0/16"
    tags = {
        Name = "vpc"
    }
}

resource "aws_subnet" "public1" {
    vpc_id  = aws_vpc.vpc.id
    cidr_block = "192.168.1.0/24"
    tags = {
        Name = "public1"
    }
}

resource "aws_subnet" "public2" {
    vpc_id  = aws_vpc.vpc.id
    cidr_block = "192.168.2.0/24"
    tags = {
        Name = "public2"
    }
}

resource "aws_internet_gateway" "igw" {
    vpc_id  = aws_vpc.vpc.id
    tags = {
        Name = "igw"
    }
}

resource "aws_route_table" "route1" {
    vpc_id      = aws_vpc.vpc.id

    route {
      cidr_block  = "0.0.0.0/0"
      gateway_id  = aws_internet_gateway.igw.id
    }
}

resource "aws_route_table" "route2" {
    vpc_id      = aws_vpc.vpc.id

    route {
      cidr_block  = "0.0.0.0/0"
      gateway_id  = aws_internet_gateway.igw.id
    }
}    

resource "aws_route_table_association" "a" {
    subnet_id      = aws_subnet.public1.id
    route_table_id = aws_route_table.route1.id
}

resource "aws_route_table_association" "b" {
    subnet_id      = aws_subnet.public2.id
    route_table_id = aws_route_table.route2.id
}  
