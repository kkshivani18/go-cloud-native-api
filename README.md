# Cloud Native API with Go and AWS

A serverless user authentication API built with Go, deployed on AWS using AWS CDK. This project demonstrates cloud-native development patterns including infrastructure as code, serverless functions, and secure user authentication.

## Overview

This project implements a RESTful API for user registration and authentication using:
- **AWS Lambda** - Serverless compute for API handlers
- **Amazon DynamoDB** - NoSQL database for user storage
- **API Gateway** - RESTful API endpoint management
- **AWS CDK (Go)** - Infrastructure as Code for automated deployment
- **bcrypt** - Secure password hashing

## Architecture

![Project Architecture](<../Cloud-Native API with Go & AWS/images/architecture.png>)

```
API Gateway → Lambda Function → DynamoDB
    ↓              ↓
/register      Handler Logic
/login         Password Hashing
```

### Components

- **API Gateway**: Manages HTTP endpoints with CORS configuration
- **Lambda Function**: Processes registration and login requests
- **DynamoDB Table**: Stores user credentials with username as partition key
- **IAM Roles**: Manages permissions for API Gateway CloudWatch logging

## Features

### Current Implementation

✅ **User Registration** (`POST /register`)
- Validates username and password
- Checks for existing users
- Hashes passwords using bcrypt
- Stores user credentials in DynamoDB

✅ **User Login** (`POST /login`)
- Validates credentials
- Compares hashed passwords
- Returns success/failure response

### Left to Implement

**JWT Authentication**
- Token generation upon successful login
- Token-based authorization for protected routes
- Refresh token mechanism
- Token expiration and validation

## 📁 Project Structure

```
├── go_cdk_aws.go           # CDK stack definition (infrastructure)
├── cdk.json                # CDK configuration
├── lambda/
│   ├── main.go             # Lambda entry point & routing
│   ├── api/
│   │   └── api.go          # API handlers (register, login)
│   ├── app/
│   │   └── app.go          # Application initialization
│   ├── database/
│   │   └── database.go     # DynamoDB client & operations
│   └── types/
│       └── types.go        # User types & password utilities
```

## Technology Stack

- **Language**: Go 1.x
- **Cloud Provider**: AWS
- **IaC Tool**: AWS CDK v2
- **Database**: Amazon DynamoDB
- **Compute**: AWS Lambda (AL2023 runtime)
- **API**: Amazon API Gateway
- **Security**: bcrypt password hashing

## Dependencies

```go
// Lambda dependencies
github.com/aws/aws-lambda-go
github.com/aws/aws-sdk-go
golang.org/x/crypto/bcrypt

// CDK dependencies
github.com/aws/aws-cdk-go/awscdk/v2
github.com/aws/constructs-go/constructs/v10
github.com/aws/jsii-runtime-go
```

## Getting Started

### Prerequisites

- Go 1.19 or later
- AWS CLI configured with credentials
- AWS CDK CLI installed (`npm install -g aws-cdk`)
- Make (for Lambda build automation)

### Installation

1. **Clone the repository**
```bash
git clone <repository-url>
cd "Cloud-Native API with Go & AWS"
```

2. **Install Go dependencies**
```bash
go mod download
cd lambda && go mod download && cd ..
```

3. **Build Lambda function**
```bash
cd lambda
make
cd ..
```

4. **Deploy infrastructure**
```bash
cdk bootstrap  # First time only
cdk deploy
```

## API Endpoints

### Register User

```bash
POST /register
Content-Type: application/json

{
  "username": "alice_smith",
  "Password": "securePassword123"
}

# Success Response (200)
"Successfully Registered user"

# Error Responses
400 - Invalid request (missing username/password)
409 - User already exists
500 - Internal server error
```

### Login User

```bash
POST /login
Content-Type: application/json

{
  "username": "john_doe",
  "password": "securePassword123"
}

# Success Response (200)
"Successfully logged in"

# Error Responses
400 - Invalid request
502 - Invalid user credentials
500 - Internal server error
```

## 🔐 Security Features

- **Password Hashing**: Passwords are hashed using bcrypt with cost factor 10
- **No Plain Text Storage**: Only hashed passwords stored in database
- **CORS Configuration**: Configured for secure cross-origin requests
- **IAM Roles**: Least-privilege access for Lambda and API Gateway

## Development

### Build Lambda locally

```bash
cd lambda
make
```

### Run CDK commands

```bash
cdk diff        # Compare deployed stack with current state
cdk synth       # Generate CloudFormation template
cdk deploy      # Deploy to AWS
cdk destroy     # Remove all resources
```

### Run tests

```bash
go test ./...
```

## Infrastructure Resources

The CDK stack creates:
- 1 DynamoDB Table (`userTable`)
- 1 Lambda Function (Go runtime)
- 1 API Gateway REST API
- 2 API Routes (`/register`, `/login`)
- IAM Roles for CloudWatch logging
- CORS configuration

## 📝 Notes

- Table removal policy is set to `DESTROY` for development
- Lambda uses custom runtime (PROVIDED_AL2023) with Go binary
- API Gateway CloudWatch logging is enabled (INFO level)
- CORS is configured to allow all origins (*)


## Useful CDK Commands

- `cdk deploy`      Deploy this stack to your default AWS account/region
- `cdk diff`        Compare deployed stack with current state
- `cdk synth`       Emits the synthesized CloudFormation template
- `go test`         Run unit tests
