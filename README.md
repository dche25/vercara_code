# Deploying a Scalable Web Application on AWS ECS using Terraform

## Overview

This guide explains how to deploy a web application on AWS ECS using Terraform. This will set up the infrastructure, deploy a Dockerized web application, and implement auto-scaling and CI/CD.

## Pre-requisites

- AWS Account
- AWS CLI installed and configured
- Terraform installed
- GitHub repository with your web application code

## Terraform Scripts Explanation

### AWS CodeBuild

The `aws_codebuild_project` resource sets up a CodeBuild project, which will build our Docker image and push it to ECR.

- `source`: Specifies the source repository. In this case, GitHub.
- `artifacts`: Output type.
- `environment`: Specifies the environment in which the build will run.

### AWS CodePipeline

The `aws_codepipeline` resource sets up a CI/CD pipeline. This will automatically pull from your GitHub repository, build the project with CodeBuild, and deploy it to the ECS service.

- `stage`: Each stage represents a phase in your CI/CD pipeline. We have "Source", "Build", and "Deploy" stages.

### Amazon CloudWatch and Auto-scaling

The `aws_cloudwatch_metric_alarm` and `aws_appautoscaling_policy` resources set up auto-scaling based on CPU utilization.

- `aws_cloudwatch_metric_alarm`: Sets up an alarm for high CPU utilization.
- `aws_appautoscaling_policy`: Specifies how the service should scale when an alarm is triggered.

### ECS Service with Blue/Green Deployment

The `aws_ecs_service` sets up your ECS service and includes blue/green deployment configurations.

- `deployment_circuit_breaker`: Automatically rolls back if the new deployment fails.
- `lifecycle`: Ensures the latest task definition is used, enabling blue/green deployments.

## Deployment Steps

1. Clone your GitHub repository containing the Terraform scripts to your local machine.

    ```bash
    git clone https://github.com/your-repo.git
    ```

2. Navigate to the directory and initialize Terraform.

    ```bash
    cd your-repo
    terraform init
    ```

3. Apply the Terraform scripts. This will create all the resources on AWS.

    ```bash
    terraform apply
    ```

    Confirm by typing `yes` when prompted.

4. After all resources are created, your CI/CD pipeline will automatically deploy the web application whenever there is a change in the main branch of your GitHub repository.

## Auto-Scaling Explained

Auto-scaling is implemented using CloudWatch alarms and ECS scaling policies.

1. **Scale Out**: When the CPU utilization exceeds 75% (as per our CloudWatch alarm), ECS triggers the scaling policy to add more tasks to the service.
  
2. **Scale In**: You can set up another CloudWatch alarm to scale in when CPU usage is low.

This ensures that your application can handle increased load and scale down when the load decreases, all while optimizing costs.

## Summary

By following this guide, you will deploy a scalable, reliable, and continuously updated web application on AWS ECS using Terraform.