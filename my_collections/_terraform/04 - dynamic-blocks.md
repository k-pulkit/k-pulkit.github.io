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
In this part I am going to introduce you to some advanced features of terraform. The purpose of having many pieces is to work our way up to master the use of `variable types`, constructs like `for_each` and use `dynamic` blocks to write terraform code in neat and efficient ways.

## Types of `variables`
Just like other programming languages, __HCL__ has the construct of data-types, and we can create variables using them. For example, we now know that modules or even resource block accept arbitary inputs from user. The best practice is to supply these variables by creating a `.tfvars` file which is used by terraform to instantiate the variables to the user supplied values. This may be seeming more complex that it is so let's break it down!

### Example usage
We will create 3 files, `main.tf`, `variables.tf` and `.tfvars`. 

## Introduction to `for_each`

## Dynamic `keyword`

## Using `dynamic` blocks for Ingress