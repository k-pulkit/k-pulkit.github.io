---
title: "Make terraform projects easy to maintain"
last_modified_at: 2023-12-17
categories:
    - Infra as code
tags:
    - learning
    - iac
    - terraform
    - aws
    - basics
toc: true
excerpt: "Learn how to make your terraform project modular and easy to maintain"
---

## Modules, what are they
If you are familiar with any programming languages, or even to the basic principles of software development then you know the operational benefits of modular programming approach when it comes to maintainability and extensibility. Breaking down terraform code into smaller pieces called __modules__ allow you to write very complex infrastructure logic by breaking down the problems, and it also allows for reusability and indepedendent development of different modules of your infrastructure modules.

> Modular terraform code is easy to maintain and develop 

## Sample modular terraform project
Below is an example of a modular terraform project tree
~~~bash
.
├── backend.tf
├── main.tf
├── modules                 <- Container of terraform modules in this example 
│   ├── dynamodb
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── ....
├── outputs.tf
├── terraform.tfstate
├── terraform.tfvars
└── variables.tf
~~~

Well, now you know where modules written by you can reside. But how about modules written by others.
Just like we have maven in java as a repository for public and private java code, we have terraform registry where public modules have been made available.

So, in summary, you can reference terraform modules from -
1. Terraform public registry
2. A private registry
3. Local system (like the example above)

## How to reference a module
~~~hcl
// main.tf
module "my-dynamodb-module" {
    source   = "./modules/dynamodb"
    version  = "0.0.1"
    region   = var.aws_region 
}
~~~
In the above code, `module` is a reserved keyword that is used to declare and instantiate the module, we provide infomation such as where this module lives, the version to use and pass any variables that the module expects, such as in this case we pass the aws region.

Modules can be made to take arbitary number of inputs and produce outputs that can be consumed someplace else in code
{: .notice}

## A simple module
Let's write a simple module to spin up a dynamodb table
1. Create a folder `modules/dynamodb` which will house out module logic. 
2. Create 3 files - `main.tf`, `outputs.tf` and `variables.tf`

Now, start writing these files
### Variables.tf
This is the file where we define the inputs to out module
~~~hcl
//variables.tf
variable "aws_region" {
  type    = string
  default = "us-east-1"
}

variable "table_name" {
  description = "Name of dynamodb table"
  type        = string
}

variable "resource_tags" {
  description = "Tags used for this project for the resources I will create"
  type        = map(string)
}
~~~
Our module takes `aws_region`, `table_name` (name of dynamo table) and `tags` as the inputs. As you may notice, while defining each variable we also mention the type of input to expect for that variables. 

There are many types available in HCL such as `string`, `boolean` which are primitives and many complex types like `list`, `set`, `map` and `objects`
{: .notice--info}

### Main.tf
Next, we will define the resource that the module is responsible for. Always remember, even within module you can have seperate files, like one file just to define the IAM policies etc.
~~~hcl
// main.tf
provider "aws" {
  region = var.aws_region
}

resource "aws_dynamodb_table" "this" {
  name         = var.table_name
  hash_key     = "messageid"
  billing_mode = "PAY_PER_REQUEST"

  attribute {
    name = "messageid"
    type = "S"
  }

  tags = var.resource_tags
}
~~~
As you can notice, each module has its own provider. You can pass a different provider using the provider parameter when calling the module.
In this use case, we create a __AWS DynamoDB table__ resource, and use the inputs for the table name. How to use a particular resource and what are the options available can be looked up on [terraform registry online](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/dynamodb_table).

### Outputs.tf
Once your resources are spun-up, you may require to use the outputs somewhere else, display Public IP of a EC2 instance to user, or supply ARN of DynamoDB table to a IAM policy.

In our case to exemplify the use of outputs our module outputs basic information of resource created.
~~~hcl
output "DynamoARN" {
  value = aws_dynamodb_table.this.arn
}

output "BillMode" {
  value = aws_dynamodb_table.this.billing_mode
}

output "TableName" {
  value = aws_dynamodb_table.this.name
}
~~~

So, as you can notice above, the module we have created outputs DynamoARN for the arn of table created. When we call the modules somewhere else using `module "mytable"`, then output can be referenced as `module.mytable.DynamoARN`. Not only this, we can share the module we have created across teams for reusability. 

In conclusion, using modules makes life of developers easy. By using modules, you can break down your infrastructure problem and also unit test individual pieces before stringing them all together!

## Bonus
When you use modules, in the modules you also get access to some special functionality of HCL, this is completely optional but knowing that it exists will come in handy in many scenarios.

When using a piece of modules, you can use the below parameters in the module block - 
1. __count and for_each__ - This is mostly used when you want to say iterate over a set of variables and create multiple instances of Module resource based on the variable value.
2. __depends_on__ - This is useful to declare relationship, so that module code is executed in a particular order, and all module and code that it depends on runs before the module starts executing.
3. __provider__ - You can supply the provider if you want to supply a specific one.