---
title: "What is terraform state"
last_modified_at: 2023-12-13
categories:
    - IaC (Infrastructure as code)
tags:
    - learning
    - iac
    - terraform
    - aws
    - basics
toc: true
excerpt: "Understand the meaning of state in terraform projects"
---

## Objective
In this section, we want to spend some time discussing the concept of terraform state. What is `terraform state`? Since terraform is responsible to create/alter resources on some platform, say we can configure it to deploy pods to kubernetes, or create/modidy resource on AWS, it needs some sort of memory to remember the current state of resources at every point in time. How is this useful ? Well, based on what we ask terraform to do, it can lookup the current state of resources from its state file and come up with the so called `terraform-plan`. 

So, state along with the current resource requirements act as the inputs for terraform to produce a plan to create/modify resources.
{: .notice--info}

## Where is terraform state stored
In the very basic form, when you create a terraform project using `terraform init`, the state is often stored away in a `.tfstate` file on local system. Well, that's great isn't it ?

Not so much, because loosing the state file will mean loosing memory and if that happens then terraform can no longer make changes to your resources as you would expect. This makes it necessary to store terraform state file securely and prevent accidental deletions. The ideal alternative is - 
1. Store the state file in a shared repository such as S3, so that your team can make changes to the infrastructure in a shared manner. Other than that versioning and locking of state file object can provide additional level of security for you infrastrcuture deployment workflow.
2. Store the statefile in HashiCorp's cloud which provides additional benefits like fine grained security.

## How to store statefile in S3 ?
To store terraform `state` file on a remote backend such as S3, we first have to create a `bucket` to hold the terraform state file, and make sure the terraform service provider has get and put permissions to the S3 bucket. In addition, you will need to create a `dynamo-db` table which is used to lock the file everytime modifications are being done to the state file. This helps make sure that there are no conflicts and all changes are made in isolated manner.

So, before proceding, we will create the below two resource
* S3 bucket - tf-state-bucket
* DynamoDb table - A table, say `tf-state-table`, with key `LockID`

Then, we create a file `backend.tf` (Always remember that your terraform project can have multiple files, like for providers, backends etc.)
Code to set backend to S3 will be -
~~~hcl
terraform {
    backend "s3" {
        bucket         = "tf-state-bucket"
        key            = "key-for-deployment"
        region         = "us-east-2"
        dynamodb_table = "tf-state-table"
    }
}
~~~

This will configure the backend to S3, and any changes to resources will be stored away in S3. This is also useful when you are using git actions, as GIT workflows are just ephmeral runs and you need some persistent means to store the terraform state to make the GIT+Terraform workflow useful.



