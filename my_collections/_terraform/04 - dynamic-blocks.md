---
title: "Terraform dynamic blocks"
permalink: /learn/terraform/blocks/
excerpt: "How to quickly install and setup Minimal Mistakes for use with GitHub Pages."
last_modified_at: 2024-01-09
tags:
    - learning
    - iac
    - terraform
    - aws
toc: true
---

## Introduction
In this part I am going to introduce you to some advanced features of terraform. We will understand the use of `variable types`, constructs like `for_each` and use `dynamic` blocks to write terraform code that is not verbose.

## Types of `variables`
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

## Introduction to `for_each`

## Dynamic `keyword`

## Using `dynamic` blocks for Ingress