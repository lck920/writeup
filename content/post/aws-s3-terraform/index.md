---
title: "Automating AWS S3 Bucket Creation with Terraform"
date: 2026-06-05
description: "A step-by-step guide on using Terraform as an Infrastructure as Code (IaC) tool to automate, configure, and secure Amazon S3 buckets, including custom tagging and AWS CLI credentials setup."
tags:
  - aws
  - terraform
  - s3
  - iac
  - devops
categories:
  - devops writeup
---

<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />


# Create S3 Buckets with Terraform

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-devops-terraform1)

---

![Image](http://learn.nextwork.org/charmed_gray_loyal_turtle/uploads/aws-devops-terraform1_9i0j1k2l)

---

## Introducing Today's Project!

How to use Terraform as an Infrastructure as Code (IaC) tool to automate the creation and configuration of cloud infrastructure on AWS. I will show how to write a configuration blueprint (main.tf), initialize the Terraform backend, preview deployment plans, and securely connect the local environment to AWS using the AWS CLI and IAM access keys.

### Tools and concepts

Services I used were Amazon S3 (Simple Storage Service) to create a globally unique storage bucket and host an image object, and AWS IAM (Identity and Access Management) to securely generate programmatic access keys. Key concepts I learnt include infrastructure as code (IaC) using Terraform to write human-readable declaration files (main.tf) that model cloud infrastructure as text. I also learned the core DevOps pipeline deployment workflow (terraform init, plan, and apply), how to implement a secure public access block to prevent data leaks, and how to use the AWS CLI to manage cloud environments programmatically from a local terminal.

### Project reflection

This project took me approximately 1 hour to complete. The most challenging part was shifting from a graphical user interface mindset to an Infrastructure as Code (IaC) mindset, making sure all terminal commands were executed in the exact logical order required by Terraform. It was most rewarding to unlock and successfully execute the Secret Mission, giving me the confidence to start automating cloud deployments rather than creating resources manually.

I chose to do this project today because I wanted to master the foundational DevOps lifecycle commands (init, plan, apply) and learn how to securely authenticate a local development terminal with AWS using IAM access keys. Something that would make learning with NextWork even better is providing a small library of optimization or troubleshooting challenges at the very end of the project to help us debug common terraform state mismatches or configuration errors.

---

## Introducing Terraform

Terraform is an open-source Infrastructure as Code (IaC) tool created by HashiCorp. It allows you to build, change, and version cloud infrastructure safely and efficiently using human-readable configuration files instead of clicking through a web console.

Terraform is one of the most popular tools used for infrastructure as code (IaC), ...which is the practice of describing and managing your cloud resources (like servers, storage, and networks) using plain text files instead of clicking through a web console. When you run these files, the cloud automatically builds everything exactly as you've written it, allowing you to treat your infrastructure just like software code.

Terraform uses configuration files to define and manage the desired state of your cloud infrastructure. main.tf is the central blueprint file in a Terraform project where you write the actual code to declare your resources—such as your AWS provider settings, S3 buckets, and access controls—telling Terraform exactly what you want it to build.

![Image](http://learn.nextwork.org/charmed_gray_loyal_turtle/uploads/aws-devops-terraform1_9i0j1k2l)

---

## Configuration files

The configuration is structured in an easy-to-read, human-digestible layout where cloud resources are separated into independent, logical blocks. Each block outlines the specific type of resource being requested and lists its custom arguments, like bucket names and security variables. The advantage of doing this is that your infrastructure blueprint serves as a clear piece of documentation. Anyone on your engineering team can open the file, instantly understand the architecture's design, and safely reproduce the exact same cloud setup without needing a manual guide.

### My main.tf configuration has three blocks

The first block indicates the cloud provider that Terraform needs to target, which is AWS, along with the specific geographic region (region = "your-region-code") where the infrastructure should be deployed. This block tells Terraform which plugin to download so it can translate your code into the correct AWS API calls. The second block provisions the actual Amazon S3 bucket resource (aws_s3_bucket), giving it an internal reference name (my_bucket) for Terraform to use, and a globally unique name for the bucket itself so it can be successfully created in your AWS account. The third block manages the security perimeter of that specific S3 bucket by implementing an aws_s3_bucket_public_access_block. It explicitly references the bucket created in the previous block and sets all public access flags (block_public_acls, ignore_public_acls, block_public_policy, and restrict_public_buckets) to true to prevent accidental public data exposure.

![Image](http://learn.nextwork.org/charmed_gray_loyal_turtle/uploads/aws-devops-terraform1_ljvh9876)

---

## Customizing my S3 Bucket

For my project extension, I visited the official Terraform documentation to learn how to add custom metadata to categorize my newly created resources. The documentation shows that the aws_s3_bucket resource block supports a tags argument. By passing a map of key-value pairs (like Environment = "Dev" and Project = "NextWork"), I can organize my resources inside the AWS billing console and trace resource ownership directly from my infrastructure code.

I chose to customise my bucket by adding a tags configuration block containing custom metadata (Name = "My bucket" and Environment = "Dev") directly inside my aws_s3_bucket resource. Because resource tagging is a critical DevOps best practice in production environments. It allows teams to clearly categorize cloud resources, trace project ownership, and organize billing costs within a busy AWS account without altering the resource infrastructure itself. When I launch my bucket, I can verify my customization by logging into the AWS Management Console, opening the Amazon S3 service dashboard, clicking into my specific bucket (nextwork-unique-bucket-dllm-302432775662), navigating to the Properties tab, and scrolling down to the Tags section to confirm that both custom keys and values have been applied successfully.

![Image](http://learn.nextwork.org/charmed_gray_loyal_turtle/uploads/aws-devops-terraform1_ffe757cd3)

---

## Terraform commands

I ran 'terraform init' to prepare and initialize my working directory for the project. This command instructs Terraform to scan my main.tf file, identify that I am using AWS, and automatically download the necessary cloud provider plugins so it can communicate with my AWS account.

Next, I ran 'terraform plan' to generate a safe preview of the actions Terraform will take to build my infrastructure. It reads my configuration file and displays a dry-run layout in the terminal, showing that it intends to add two new resources (the S3 bucket and its public access block) without altering or deleting anything else.

![Image](http://learn.nextwork.org/charmed_gray_loyal_turtle/uploads/aws-devops-terraform1_3g4h5i6j)

---

## AWS CLI and Access Keys

When I tried to plan my Terraform configuration, I received an error message that says "No valid credential sources found" or "Error authenticating AWS client". Because Terraform runs locally on my machine and does not natively have permission to access my AWS account. Until I install the AWS CLI and configure my IAM access keys, Terraform cannot verify who I am or securely call the AWS APIs to preview my infrastructure resources.

To resolve my error, first I installed AWS CLI, which is a command-line tool that lets me control and manage my AWS services directly from my local terminal. Instead of manually navigating and clicking through the web-based AWS Management Console, it allows my computer's terminal to send direct commands to AWS, enabling tools like Terraform to securely communicate with my account.

I set up AWS access keys to grant the AWS CLI and Terraform programmatic access to my AWS account. Because the terminal does not share my web browser's login session, these keys act as a secure username and password that allow my local machine to authenticate with AWS and build resources directly from my code.

![Image](http://learn.nextwork.org/charmed_gray_loyal_turtle/uploads/aws-devops-terraform1_7j8k9l0m)

---

## Lanching the S3 Bucket

I ran 'terraform apply' to finalize the deployment of my infrastructure code. Running 'terraform apply' will affect my AWS account by triggering the creation of my globally unique Amazon S3 bucket and instantly attaching the strict public access block settings to it, bringing my actual cloud infrastructure in line with my main.tf configuration blueprint.

The sequence of running terraform init, plan, and apply is crucial because each command builds directly on the previous one to create a safe, predictable deployment pipeline. init prepares your workspace by downloading the necessary cloud provider plugins; plan creates a risk-free blueprint simulation so you can preview exactly what will change; and apply finalizes the process by executing those approved changes on your live cloud account. Skipping this order increases the risk of deploying blind syntax errors or making accidental modifications to your active infrastructure.

![Image](http://learn.nextwork.org/charmed_gray_loyal_turtle/uploads/aws-devops-terraform1_1q2w3e4r)

---

## Uploading an S3 Object

I created a new resource block to define an aws_s3_object named image. This block points directly to my local image.png file and specifies that it should be uploaded and stored directly inside the S3 bucket I provisioned in the previous steps.

We need to run terraform apply again because any changes made to your main.tf file—such as adding the new aws_s3_object resource block—exist only locally on your machine. Running the apply command tells Terraform to compare your updated configuration against the live cloud infrastructure and securely push the new file upload to AWS.

To validate that I've updated my configuration successfully, I navigated to the Amazon S3 console, opened my newly created bucket, and verified that the image.png file was listed inside the objects tab. I then selected the file, downloaded it back to my local machine, and opened it to confirm it perfectly matched the original image I targeted in my code.

![Image](http://learn.nextwork.org/charmed_gray_loyal_turtle/uploads/aws-devops-terraform1_9o0p1a2s)

---

---
