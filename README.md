# Todo DynamoDB Table

This repository contains an AWS Serverless Application Model (SAM) template (`template.yaml`) to deploy a DynamoDB table named **TodoTable** for storing todo items. The table is configured with pay-per-request billing and server-side encryption for added security.

## Overview


The SAM template provisions the following AWS resource:

### DynamoDB Table: `TodoTable`
- **Primary Key:** `id` (string, hash key)
- **Billing Mode:** Pay-per-request (no provisioned capacity)
- **Server-Side Encryption:** Enabled for data security

This is a simple, serverless setup ideal for a **Todo Application** backend.

## Prerequisites

Before deploying this template, ensure you have:
- **AWS CLI:** Installed and configured with appropriate credentials.
- **AWS SAM CLI:** Installed for deploying SAM templates (`pip install aws-sam-cli`).
- **IAM Permissions:** Permissions to create DynamoDB tables.
- **AWS Account:** Access to your target region (the default region will be used unless specified).

## Deployment Instructions

### 1. Clone the Repository
```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. Validate the Template (Optional)
You can validate the SAM template using the following command:
```bash
sam validate --template-file template.yaml
```

### 3. Build the SAM Application
To build the SAM application, run:
```bash
sam build --template-file template.yaml
```

### 4. Deploy the Stack
Run the following command to deploy the SAM application:
```bash
sam deploy \
  --stack-name todo-dynamodb-stack \
  --capabilities CAPABILITY_IAM \
  --region <aws-region>
```
- Replace `todo-dynamodb-stack` with your desired stack name.
- Replace `<aws-region>` with your target AWS region (e.g., us-east-1).
- The `CAPABILITY_IAM` flag is included as a precaution, though this template doesn't require IAM resources.

### 5. Monitor Deployment
The SAM CLI will guide you through the deployment process. Alternatively, check the status in the AWS Management Console under CloudFormation or via the AWS CLI:
```bash
aws cloudformation describe-stacks --stack-name todo-dynamodb-stack --region <aws-region>
```

### 6. Retrieve the Table Name
After deployment, retrieve the table name from the stack outputs:
```bash
aws cloudformation describe-stacks \
  --stack-name todo-dynamodb-stack \
  --region <aws-region> \
  --query "Stacks[0].Outputs[?OutputKey=='TodoTableName'].OutputValue" \
  --output text
```
The output will be `TodoTable`, which you can use in your application.

## Infrastructure Details

### DynamoDB Table
- **Name:** TodoTable
- **Partition Key:** id (string)
- **Billing Mode:** Pay-per-request (scales automatically with usage)
- **Encryption:** Server-side encryption enabled (AWS-managed keys)

### Outputs
After deployment, the stack provides the following output:
- **TodoTableName:** The name of the DynamoDB table (TodoTable).

## Usage

To interact with the TodoTable in your application, use the AWS SDK or CLI with the table name (TodoTable) and the `id` attribute as the primary key.

Example (AWS CLI):
```bash
aws dynamodb put-item \
  --table-name TodoTable \
  --item '{"id": {"S": "1"}, "task": {"S": "Buy groceries"}}' \
  --region <aws-region>
```

## Cleanup

To delete the stack and the DynamoDB table:
```bash
aws cloudformation delete-stack --stack-name todo-dynamodb-stack --region <aws-region>
```

## Notes
- The table uses pay-per-request billing, which is cost-effective for variable workloads but may incur higher costs under heavy, sustained usage compared to provisioned capacity.
- No additional indexes are defined. Add secondary indexes to the template if your application requires them.
- Ensure your application has appropriate IAM permissions to read/write to the TodoTable.
