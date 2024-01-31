---
title: "Terraform dynamic blocks"
last_modified_at: 2023-12-22
categories:
    - Infra as code
tags:
    - learning
    - iac
    - terraform
    - aws
    - basics
toc: true
excerpt: "Using dynamic blocks helps to avoid repeat code and be more concise in logic"
---

## Introduction
In this part I am going to introduce you to some advanced features of terraform. We will understand the use of `variable types`, constructs like `for_each` and use `dynamic` blocks to write terraform code that is not verbose.

## Introduction to `variables`
Just like other programming languages, __HCL__ has the construct of data-types, and we can create variables using them. For example, we now know that modules or even resource block accept arbitary inputs from user. The best practice is to supply these variables by creating a `terraform.tfvars` file which is used by terraform to instantiate the variables to the user supplied values.

We will start with a simple example.

### Example usage
In this example, we will use terraform to create a S3 bucket. We will need 3 files, `main.tf`, `variables.tf` and `.tfvars` inside the demo-project directory.
~~~bash
.
├── backend.tf
├── main.tf
├── terraform.tfvars
└── variables.tf
~~~

#### Main.tf
This file houses the code to create our `s3` resource. As you can see, we have used variables using the syntax `var.variableName` to use the value of variable in resource and provider blocks.
~~~hcl
provider "aws" {
  profile = var.profile
  region  = var.region
}

resource "aws_s3_bucket" "testbucket" {
  bucket = var.bucket_name
}
~~~

#### Variables.tf
This is the file where we are going to declare the variables that we need to use in code.
~~~hcl

variable "region" {
  type    = string
  default = "us-east-1"
}

variable "profile" {
  type    = string
  default = "profile-name-to-use"
}

variable "bucket_name" {
  type    = string
  description = "Name of S3 bucket to create"
}
~~~ 

Along with basic parameters, you can use other parameters like sensitive, and add condition checks to validate the variables passed by user.

#### Terraform.tfvars
Finally, we can supply the value of variables using the `.tfvars` file.
~~~hcl
bucket_name = "test_bucket"
~~~

Running the program is quite simple. I used the below sequence in every terraform project
~~~bash
# Initialize the backend and provider
terraform init

# Format the code
terraform fmt

# Validate for syntax errors
terraform validate

# Create plan
terraform plan

# Apply the changes
terraform apply

# Decommission 
terrafrom destroy
~~~

## Types of `variables`
There are two types or variables in terraform - 
* Primitives - String, Boolean, Numeric
* Complex - Set, Map, Object, Tuple, List, Any

You have already seen example of __string__ variable when we introduced the idea of types in HCL. Let's look at a complex variable declaration to make the idea more concrete.

~~~hcl
variable "igress_params" {
  type = map(object({
    port  = number
    proto = string
    cidr  = list(string)
  }))
  default = {
    "rule1" = {
      port  = 22
      proto = "tcp"
      cidr  = ["0.0.0.0/0"]
    },
    "rule2" = {
      port  = 80
      proto = "tcp"
      cidr  = ["1.2.3.4/32"]
    }
  }
}
~~~

In the above block of code, we create a variable of type `map(object)` and use it to carry all the rules for a AWS security group that we can use later when creating VPC and Security Group.

## Introduction to `for_each`
The `for_each` blocks are very good utility blocks when you want to create multiple resources of same type but different variable values. Such as, you have to create three S3 buckets for dev, uat and prod. 

Below is an example use of how to use these blocks.

~~~hcl
provider "aws" {
  profile = "aws-profile-to-use"
  region  = "us-east-1"
}

variable "s3_buckets" {
  type        = set(string)
  description = "Name of S3 buckets to create"
  default     = ["test-dev", "test-uat", "test-prod"]
}

resource "aws_s3_bucket" "testbucket" {
  for_each = var.s3_buckets
  bucket   = each.value
}
~~~

The above code iterates through each value of the `s3_buckets` variable and create 3 buckets.

## Dynamic `keyword`
When creating a VPC followed by a security group, we often need to specify the inbound and outbound rules. It is often the case that the list of these rules and be length and hence not easy to maintain. Example of defining a security group without dynamic keyword. 

~~~hcl
resource "aws_security_group" "my-sg" {
  vpc_id = aws_vpc.my-vpc.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
~~~

As you can see, this approach is very verbose, plus to add to this issue the rules are intermingled with the intra code. Whenever possible, the best practice is the seperate variable like structures from actual logic. 

Below is how we achieve the same using dynamic keyword.

~~~hcl
variable "igress_params" {
  type = map(object({
    port  = number
    proto = string
    cidr  = list(string)
  }))
  default = {
    "rule1" = {
      port  = 22
      proto = "tcp"
      cidr  = ["0.0.0.0/0"]
    },
    "rule2" = {
      port  = 80
      proto = "tcp"
      cidr  = ["1.2.3.4/32"]
    }
  }
}

resource "aws_vpc" "my-vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_security_group" "my-sg" {
  vpc_id = aws_vpc.my-vpc.id

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  dynamic "ingress" {
    for_each = var.igress_params

    content {
      from_port   = ingress.value["port"]
      to_port     = ingress.value["port"]
      protocol    = ingress.value["proto"]
      cidr_blocks = ingress.value["cidr"]
    }

  }
}
~~~

Using the __dynamic__ block, terraform will create multiple ingress blocks while iterating through the list of values.