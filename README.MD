# Serverless AWS Lambda with API Gateway and CI/CD

## Project Overview
This project demonstrates how to build and deploy a serverless application using **AWS Lambda** and **API Gateway**. It also integrates **CI/CD** with **Jenkins** for continuous deployment.

### Features
- **AWS Lambda**: Serverless function to process requests.
- **API Gateway**: Exposes the Lambda function via HTTP endpoint.
- **Jenkins CI/CD**: Automates the deployment of the Lambda function and API Gateway.
- **CloudFormation**: Infrastructure as Code to provision Lambda and API Gateway.

## Setup

### Prerequisites
1. AWS Account and IAM Role/Access for Lambda and API Gateway.
2. Jenkins installed (or use [Jenkins in the cloud](https://www.jenkins.io/cloud/)).
3. AWS CLI installed and configured on your local system.

### Steps to Deploy

1. **Lambda Function**:
    - Clone the repository to your local machine.
    - Navigate to the `lambda_function` directory.
    - Install dependencies: `npm install`.
    - Zip the Lambda code: `zip -r function.zip .`.
    - Upload the Lambda function using the AWS CLI:
      ```bash
      aws lambda update-function-code --function-name MyLambdaFunction --zip-file fileb://function.zip
      ```

2. **Deploy API Gateway**:
    - Deploy the API Gateway using AWS Console or CloudFormation template:
      ```bash
      aws cloudformation deploy --template-file cloudformation/serverless.yaml --stack-name MyApiGatewayStack
      ```

3. **Jenkins CI/CD**:
    - Import the Jenkinsfile into Jenkins.
    - Create a pipeline job and link the repository.
    - Trigger the job to automate the deployment process.

### Testing the API
Once the deployment is complete, access the API endpoint provided by API Gateway:
