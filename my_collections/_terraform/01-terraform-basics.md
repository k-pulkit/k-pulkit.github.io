---
title: "Quick-Start Guide"
permalink: /learn/terraform/basics/
excerpt: "Learn basics about terraform for IAC"
last_modified_at: 2024-01-09
tags:
    - learning
    - iac
    - terraform
    - aws
    - basics
redirect_from:
  - /terraform/
  - /learn/terraform/
toc: true
---

## Introduction 
In this series, we are going to focus our attention on Terraform. This series is different in the sense that I will be learning along as I write about it.

Terraform is an open-source Infrastructure as Code (IaC) tool designed by HashiCorp. It enables users to define and provision infrastructure and resources in a declarative manner using a high-level configuration language. 

One Key thing about terraform that makes it so powerful is that it is a cloud agnostic tool that allow provisioning of infrastructure in over 100+ providers including AWS, Azure and GCP.
{: .notice}

## Basics first
Before diving into the code, let's walk through some basics first. 
Every terraform project has a fixed lifecycle, which consists of `Init -> Write -> Plan -> Apply`. This is pretty standard for every terraform project and the meaning of this can be looked up with one Google search.

### First code snippets
Any terraform project with start by defining the provider. The same looks like below.
~~~hcl
// This defines and sets the provider for the terraform project. This will help terraform download the required supporting code to interact with the provider
// APIs and plan and apply resources to the provided account
provider "google" {
   credentials = file("creds.json")
   project = "Hello-World"
   region = "us-east-1"
}
~~~

After to set the provider, you will go along and create resources using snipped like below.
~~~hcl
resource "aws_instance" "hello-instance" {
   ami = "ami-id-here"
   instance-type = "t2-micro"
}
~~~

As you can see above, we just created a `provider` and also defined a `resource` using terraform specific language called HCL. Both are treated as special keywords. In the second snipped, `aws_instance` defines the type of resource we are creating and it is followed by user-defined name of the resouce. Later, when
we need to we can address to the instance as `aws_instance.hello-instance`.

*[HCL]: Hashicorp Configuration Language

There is yet another useful block known as the `data` block, which can be used to keep data such as instance IDs, instance-types and so on. This makes sure that
out configuration that is used in multiple places is not repeated and spread across, thus making it easier to manage. We will use it later when the need arises.

## Example 1 - Create an EC2 instance
In this example, we are going to create an EC2 instance using terraform. Please follow the steps in this [video](https://www.youtube.com/watch?v=QhmFnlbbwP4) to setup the `terraform` and `aws` cli. I personally prefer using *'WSL on windows'* for my development.

### Solution code
If required, you may need to read-up about subnets and networking first.[^1][^2][^3]
{: .notice--info}

~~~hcl
// setup the provider. We also mention the profile that terraform should use for login
provider "aws" {
    region = "us-east-1"
    profile = "terraform"
}

// Step 1: Create a VPC for development
resource "aws_vpc" "vpcdev" {
    cidr_block = "10.0.0.0/16"
    tags = {
      environment = "development"
    }
}

// Step 2: Create a subnet in the VPC created above. We choose us-east-1a' as the zone where VPC is deployed
resource "aws_subnet" "devsubnet1" {
    vpc_id = aws_vpc.vpcdev.id
    cidr_block = "10.0.1.0/24"
    availability_zone = "us-east-1a" 
    depends_on = [ aws_vpc.vpcdev ]
}

// Step 2: Create an EC2 instance in the subnet
resource "aws_instance" "devvm1" {
    ami = "ami-0be2609ba883822ec"
    instance_type = "t2.micro"
    subnet_id = aws_subnet.devsubnet1.id
    depends_on = [ aws_subnet.devsubnet1 ]
}
~~~


[^1]: [AWS VPC & Subnets](https://www.youtube.com/watch?v=bGDMeD6kOz0)
[^2]: [AWS Subnets](https://registry.terraform.io/providers/-/aws/latest/docs/resources/subnet)
[^3]: [Understanding CIDR](https://www.youtube.com/results?search_query=understanding+CIDR+subnets+aws)

