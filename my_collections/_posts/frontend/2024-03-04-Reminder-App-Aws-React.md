---
title: "Email Reminders - React & AWS"
last_modified_at: 2024-04-03
categories:
    - project
tags:
    - project
    - react
    - aws
    - development
    - modern
header: 
  image: https://github.com/k-pulkit/EmailReminders-React_Aws_IaC/assets/71238192/386d3778-eedc-4a33-b50f-ed5a44b63b62
  caption: "Screenshot of application frontend"
  teaser: /assets/images/collections/posts/2024-03-04-Reminder-App-Aws-React/thumb.png
toc: true
excerpt: "AWS Reminder application built using React and AWS services like Lambda, cognito etc."
---

[Checkout the project repo!](https://github.com/k-pulkit/EmailReminders-React_Aws_IaC){: .btn .btn--info}

Every big project is done one step at a time. Slowly and steadily you can dig at new concepts, learn and so can build any complex application
{: .notice--warning}

<!-- <figure class="">
    <a href="https://github.com/k-pulkit/EmailReminders-React_Aws_IaC/assets/71238192/386d3778-eedc-4a33-b50f-ed5a44b63b62"><img src="https://github.com/k-pulkit/EmailReminders-React_Aws_IaC/assets/71238192/386d3778-eedc-4a33-b50f-ed5a44b63b62"></a>
</figure> -->

## Objective

<!-- ![image-center](https://github.com/k-pulkit/EmailReminders-React_Aws_IaC/assets/71238192/386d3778-eedc-4a33-b50f-ed5a44b63b62){: .align-center} -->

In this blog post, we'll explore the development of a Reminder App using a modern tech stack, including various AWS services for the backend and React for the frontend. This application provides a seamless authentication flow and a robust reminder management system.

## Tech Stack

The application uses serverless architecture. The front is a modern SPA (Single Page Application) written in React, and the backend consists of AWS services like `Cognito, Lambda, API Gateway`.

<figure class="">
    <a href="https://github.com/k-pulkit/EmailReminders-React_Aws_IaC/assets/71238192/4614ccaa-bf9b-4762-9c8e-675540150664"><img src="https://github.com/k-pulkit/EmailReminders-React_Aws_IaC/assets/71238192/4614ccaa-bf9b-4762-9c8e-675540150664" alt="Reminder app screenshot"></a>
</figure>

### Frontend
- **React**: A JavaScript library for building user interfaces, React allows developers to create reusable UI components that can update efficiently when the data changes.
- **JavaScript**: The programming language used for implementing the app's logic and interactions.
- **Tailwind CSS**: A utility-first CSS framework used for styling the app, providing a customizable and responsive design.

### Backend (AWS)
- **Cognito User Pools**: AWS Cognito provides user authentication and authorization for the app. User Pools allow you to manage user sign-up and sign-in processes.
- **API Gateway**: AWS API Gateway is used to create, publish, maintain, monitor, and secure APIs. It acts as a front door for the app's backend services.
- **IAM (Identity and Access Management)**: IAM is used to securely control access to AWS services and resources for the app's users and services.
- **Lambda Functions**: AWS Lambda is used for executing serverless functions that respond to events triggered by the app, such as setting reminders and sending emails.
- **Simple Notification Service (SNS)**: SNS is used for sending notifications to users' email addresses when reminders are due.
- **Step Functions**: AWS Step Functions coordinate multiple AWS services into serverless workflows, allowing for the orchestration of reminder creation and management processes.

### Infrastructure as Code (IaC)
- **Serverless Framework**: The Serverless Framework is used for defining and deploying AWS infrastructure as code, enabling the app's infrastructure to be managed and deployed easily.

### Deployment
- **GitHub Pages**: The app is deployed using GitHub Pages, providing a simple and free hosting solution for static websites.

## How it Works

### Authentication Flow with Cognito
- When a user signs up for the app, their information is stored securely in a user pool managed by AWS Cognito.
- Upon successful sign-in, the app receives an authentication token that is used to authenticate subsequent API requests.

### Reminder Creation and Management
- Users can set reminders through the app's user interface, specifying the delay after which reminders are sent to their email.
- Lambda functions are triggered to create reminders and schedule them on Step functions for delivery using SNS.
- Users can view a list of active reminders and have completed reminders automatically deleted. They can also manually abort reminders.

### Deployment with Serverless Framework and GitHub Pages
- The app's infrastructure is defined using the Serverless Framework, which allows for easy deployment and management of AWS resources.
- GitHub Pages is used to host the frontend of the app, providing a simple and cost-effective hosting solution.

## How to Run the App

To run the application, clone the repository and execute the following commands:

```bash
# Deploy infrastructure
npm init -y
sls deploy

# Run locally
cd ./frontend/reminder-all
npm run dev
```

## Conclusion

The Reminder App demonstrates the power and flexibility of modern tech stacks, leveraging AWS services and React to create a robust and user-friendly application. By understanding the underlying technologies and their interactions, developers can build similar applications with ease.


