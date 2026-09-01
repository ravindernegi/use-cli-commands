# Deploy Docker Image to AWS ECS Fargate

This guide explains how to deploy an existing Docker image from your local Docker environment to AWS using:

* **Docker** — Build and manage the container image
* **Amazon ECR** — Store the Docker image
* **Amazon ECS** — Manage and run the container
* **AWS Fargate** — Run the ECS container without managing EC2 servers

## Architecture

```text
Local Docker Image
       |
       | docker tag
       v
Amazon ECR
       |
       | ECS pulls image
       v
Amazon ECS
       |
       | Fargate
       v
Running Container
```

---

# Prerequisites

Before starting, make sure you have:

1. An AWS account
2. AWS CLI installed locally
3. Docker installed and running
4. An existing Docker image available locally
5. Permission to access ECR, ECS, and Fargate

---

# Configuration

We will use the following example configuration.

```text
Local Docker Image:
suneelkathait/my-test-frontend:latest

AWS Region:
ap-south-1

AWS Account ID:
YOUR_AWS_ACCOUNT_ID

ECR Repository:
ravinder/react-app
```

> Replace `YOUR_AWS_ACCOUNT_ID` with your actual 12-digit AWS account ID.

Your complete ECR image URI will be:

```text
YOUR_AWS_ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app:latest
```

---

# Part 1 — Create an ECR Repository

## Step 1 — Login to AWS Console

Open the AWS Management Console and sign in.

Choose the AWS region:

```text
Asia Pacific (Mumbai)
ap-south-1
```

---

## Step 2 — Open Amazon ECR

In the AWS Console:

```text
AWS Console
    ↓
ECR
    ↓
Repositories
    ↓
Create repository
```

Create the repository:

```text
ravinder/react-app
```

After creating the repository, AWS will provide an ECR repository URI similar to:

```text
YOUR_AWS_ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app
```

---

# Part 2 — Install and Configure AWS CLI

## Step 3 — Install AWS CLI

If AWS CLI is already installed, skip this step.

Check whether AWS CLI is installed:

```bash
aws --version
```

If it is not installed, install AWS CLI for your operating system.

---

# Part 3 — Configure AWS Credentials

## Step 4 — Create AWS Access Keys

Create an IAM user with the required permissions and generate:

```text
Access Key ID
Secret Access Key
```

> For regular development work, prefer an IAM user or another short-lived credential mechanism instead of using AWS root-user access keys.

---

## Step 5 — Configure AWS CLI

Run:

```bash
aws configure
```

Enter:

```text
AWS Access Key ID:     YOUR_ACCESS_KEY
AWS Secret Access Key: YOUR_SECRET_KEY
Default region name:  ap-south-1
Default output format: json
```

If AWS CLI is already configured, you can skip this step.

---

# Part 4 — Validate AWS Configuration

## Step 6 — Check AWS Authentication

Run:

```bash
aws sts get-caller-identity
```

You should receive a response containing information similar to:

```json
{
    "UserId": "xxxxxxxxxxxx",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/your-user"
}
```

The `Account` value is your AWS Account ID.

---

# Part 5 — Login Docker to Amazon ECR

## Step 7 — Authenticate Docker with ECR

Run:

```bash
aws ecr get-login-password --region ap-south-1 | \
docker login \
--username AWS \
--password-stdin \
YOUR_AWS_ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com
```

You should see:

```text
Login Succeeded
```

This allows Docker to push images to your ECR repository.

---

# Part 6 — Check Your Local Docker Image

## Step 8 — Verify the Local Image

Run:

```bash
docker images
```

You should see your image:

```text
REPOSITORY                    TAG       IMAGE ID
suneelkathait/my-test-frontend latest   xxxxxxxxx
```

Our local image is:

```text
suneelkathait/my-test-frontend:latest
```

---

# Part 7 — Tag the Docker Image

## Step 9 — Tag the Existing Image

Docker needs the ECR repository URI as the new image tag.

Run:

```bash
docker tag \
suneelkathait/my-test-frontend:latest \
YOUR_AWS_ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app:latest
```

For example, if your AWS Account ID is:

```text
123456789012
```

then:

```bash
docker tag \
suneelkathait/my-test-frontend:latest \
123456789012.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app:latest
```

---

# Part 8 — Verify the New Tag

## Step 10 — Check Docker Images

Run:

```bash
docker images
```

You should now see both:

```text
suneelkathait/my-test-frontend
```

and:

```text
YOUR_AWS_ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app
```

Both tags point to the same Docker image.

---

# Part 9 — Push the Image to ECR

## Step 11 — Push Docker Image

Run:

```bash
docker push \
YOUR_AWS_ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app:latest
```

For example:

```bash
docker push \
123456789012.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app:latest
```

Docker will upload the image layers to Amazon ECR.

You should see output similar to:

```text
The push refers to repository
...
latest: digest: sha256:xxxxxxxx
```

---

# Part 10 — Verify Image in AWS

## Step 12 — Verify the Image in ECR

Go to:

```text
AWS Console
    ↓
ECR
    ↓
Repositories
    ↓
ravinder/react-app
```

You should see:

```text
Image tag: latest
```

Your image is now stored in Amazon ECR.

---

# Complete ECR Command Sequence

Replace:

```text
YOUR_AWS_ACCOUNT_ID
```

with your real AWS Account ID.

```bash
# ----------------------------------------
# Configuration
# ----------------------------------------

AWS_REGION="ap-south-1"
AWS_ACCOUNT_ID="YOUR_AWS_ACCOUNT_ID"
ECR_REPOSITORY="ravinder/react-app"
LOCAL_IMAGE="suneelkathait/my-test-frontend:latest"

ECR_REGISTRY="${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
ECR_IMAGE="${ECR_REGISTRY}/${ECR_REPOSITORY}:latest"


# ----------------------------------------
# 1. Check local Docker images
# ----------------------------------------

docker images


# ----------------------------------------
# 2. Check AWS authentication
# ----------------------------------------

aws sts get-caller-identity


# ----------------------------------------
# 3. Login Docker to Amazon ECR
# ----------------------------------------

aws ecr get-login-password \
  --region ${AWS_REGION} | \
docker login \
  --username AWS \
  --password-stdin ${ECR_REGISTRY}


# ----------------------------------------
# 4. Tag existing local image
# ----------------------------------------

docker tag \
  ${LOCAL_IMAGE} \
  ${ECR_IMAGE}


# ----------------------------------------
# 5. Verify images
# ----------------------------------------

docker images


# ----------------------------------------
# 6. Push image to ECR
# ----------------------------------------

docker push ${ECR_IMAGE}
```

---

# What We Have Achieved

At this point, the flow is:

```text
Local Docker
     |
     | suneelkathait/my-test-frontend:latest
     |
     v
Docker Tag
     |
     v
Amazon ECR
     |
     | ravinder/react-app:latest
     |
     v
Image successfully stored in AWS
```

**Important:** We have only completed the **Docker → ECR** part.

The Docker container is **not running yet**.

To actually deploy and run the application, the next steps are:

```text
ECR
 ↓
ECS Cluster
 ↓
ECS Task Definition
 ↓
Fargate
 ↓
ECS Service
 ↓
Running Container
```

For a web application, we will also need to configure the appropriate **networking, security group, container port, and optionally a public IP or Application Load Balancer**.
