## Overview

This CloudFormation template provisions Amazon S3 buckets with optional lifecycle rules for managing object expiration. It is designed to create S3 buckets based on the specified environment, product, and service, and includes a bucket policy for access control.

## Template Structure

- **AWSTemplateFormatVersion**: Specifies the version of the CloudFormation template format.
- **Description**: A brief description of what the template does.
- **Parameters**: Input parameters for customizing the stack.
- **Conditions**: Conditions to determine whether to apply certain resources or properties.
- **Resources**: The AWS resources that will be created.
- **Outputs**: Information about the resources created by the stack.

## Parameters

The following parameters can be specified when creating the stack:

- **environment**: 
  - Type: String
  - Allowed Values: `dev`, `stage`, `qa`, `prod`, `pre-prod`
  - Description: The environment for which the resources are being created.

- **product**: 
  - Type: String
  - Description: The product name associated with the resources.

- **service**: 
  - Type: String
  - Description: The service name associated with the resources.

## Conditions

- **AddLifecycleRule**: 
  - This condition checks if the `service` parameter is equal to `backend-logs`. If true, a lifecycle rule will be added to the S3 bucket.

## Resources

### S3Bucket

- **Type**: `AWS::S3::Bucket`
- **Properties**:
  - **PublicAccessBlockConfiguration**: Configures public access settings for the bucket.
  - **BucketName**: The name of the bucket, constructed from the environment, product, and service parameters.
  - **AccessControl**: Set to `Private`.
  - **WebsiteConfiguration**: Configures the bucket for static website hosting (index and error documents).
  - **LifecycleConfiguration**: 
    - If the `AddLifecycleRule` condition is true, a lifecycle rule is created to expire objects after 30 days.

### S3BucketPolicy

- **Type**: `AWS::S3::BucketPolicy`
- **Properties**:
  - **Bucket**: References the S3 bucket created.
  - **PolicyDocument**: Defines the bucket policy allowing access based on specific conditions.

## Outputs

- **S3Bucket**: 
  - Description: The S3 bucket created by the stack.
  - Value: The reference to the S3 bucket.
  - Export Name: Exports the bucket name for use in other stacks.

- **LifecycleRule**: 
  - Condition: Only included if the `AddLifecycleRule` condition is true.
  - Description: The lifecycle rule ID.
  - Value: The ID of the lifecycle rule.
  - Export Name: Exports the lifecycle rule ID for use in other stacks.

## Usage

1. **Create a Stack**:
   - Use the AWS Management Console, AWS CLI, or AWS SDKs to create a CloudFormation stack using this template.
   - Provide values for the required parameters (`environment`, `product`, and `service`).

2. **Monitor Stack Creation**:
   - Monitor the stack creation process through the CloudFormation console or CLI.

3. **Access the S3 Bucket**:
   - Once the stack is created, you can access the S3 bucket using the bucket name provided in the outputs.

4. **Lifecycle Management**:
   - If the `service` parameter is set to `backend-logs`, the lifecycle rule will automatically expire objects after 30 days.
