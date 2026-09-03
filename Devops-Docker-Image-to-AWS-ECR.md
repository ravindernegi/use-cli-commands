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

For a web application, we will also need to configure the appropriate **networking, security group, container port, and optionally a public IP**.

Yes. I checked your existing document, and your ECR section already ends exactly where the ECS deployment should begin. Your document itself identifies the next flow as **ECR → ECS Cluster → Task Definition → Fargate → ECS Service → Running Container**. ([GitHub][1])

Since your application listens on **port 3000**, I would add the following section directly after your current **“What We Have Achieved”** section.

## Part 11 — Deploy ECR Image to ECS Fargate

### Architecture

```text
Local Docker Image
       |
       | docker tag / docker push
       ↓
Amazon ECR
ravinder/react-app:latest
       |
       | ECS pulls image
       ↓
ECS Cluster
       |
       ↓
Task Definition
       |
       ↓
Fargate Task
       |
       ↓
React Application
Container Port: 3000
       |
       ↓
Public IP
       |
       ↓
Browser
```

> **Important:** In this guide, the application inside the Docker container is listening on **port 3000**.

---

# Step 13 — Open Amazon ECS

Go to:

**AWS Console → ECS**

Make sure your region is:

```text
Asia Pacific (Mumbai)
ap-south-1
```

You should see the Amazon ECS dashboard.

---

# Step 14 — Create an ECS Cluster

Go to:

```text
ECS
 ↓
Clusters
 ↓
Create cluster
```

Enter:

```text
Cluster name:
react-app-cluster
```

For the infrastructure, use:

```text
AWS Fargate (Serverless)
```

Then click:

**Create**

Your architecture is now:

```text
ECS
 |
 └── react-app-cluster
```

### What is an ECS Cluster?

A cluster is a logical grouping where your ECS tasks/services run.

It does **not** mean that you are creating an EC2 server.

With Fargate, AWS manages the underlying infrastructure for you.

---

# Step 15 — Create an ECS Task Definition

A **Task Definition** tells ECS how your Docker container should run.

Go to:

```text
ECS
 ↓
Task definitions
 ↓
Create new task definition
```

Choose:

```text
Create new task definition
```

### Task definition family

Use:

```text
react-app-task
```

### Launch type / infrastructure

Choose:

```text
AWS Fargate
```

---

# Step 16 — Configure Task CPU and Memory

For a simple React application, you can start with:

```text
CPU:
0.25 vCPU

Memory:
0.5 GB
```

This is sufficient for a basic learning/demo application.

You can increase these values later if required.

---

# Step 17 — Configure Task Execution Role

ECS needs permission to pull the private Docker image from ECR.

For:

```text
Task execution IAM role
```

select/create:

```text
ecsTaskExecutionRole
```

The role should have:

```text
AmazonECSTaskExecutionRolePolicy
```

This allows ECS/Fargate to perform operations such as pulling the container image from ECR.

The flow is:

```text
ECS Fargate
     |
     | assumes
     ↓
ecsTaskExecutionRole
     |
     | permission
     ↓
Amazon ECR
     |
     ↓
ravinder/react-app:latest
```

---

# Step 18 — Add Container

Under **Container details**, add a container.

Use:

```text
Container name:
react-app
```

For **Image URI**, use your ECR image:

```text
YOUR_AWS_ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app:latest
```

For example:

```text
123456789012.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app:latest
```

Replace:

```text
123456789012
```

with your actual AWS Account ID.

---

# Step 19 — Configure Container Port

This is important for your application.

Your Docker container runs the application on:

```text
3000
```

Therefore configure the container port as:

```text
Container port:
3000

Protocol:
TCP
```

So:

```text
Container
┌──────────────────────┐
│                      │
│   React Application  │
│                      │
│   Listening: 3000    │
│                      │
└──────────────────────┘
```

Then create the task definition.

---

# Step 20 — Verify Task Definition

After creation, you should have something similar to:

```text
Task Definition:
react-app-task

Infrastructure:
AWS Fargate

CPU:
0.25 vCPU

Memory:
0.5 GB

Container:
react-app

Image:
YOUR_AWS_ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com/ravinder/react-app:latest

Container Port:
3000
```

---

# Part 12 — Create ECS Service

Now we need an **ECS Service**.

A task is a running instance of your container.

A service ensures that the desired number of tasks stays running.

Go to:

```text
ECS
 ↓
Clusters
 ↓
react-app-cluster
 ↓
Create
 ↓
Create service
```

---

# Step 21 — Configure Service

Select:

```text
Compute options:
Launch type

Launch type:
Fargate
```

Select your task definition:

```text
Family:
react-app-task
```

Service name:

```text
react-app-service
```

Desired tasks:

```text
1
```

Meaning:

```text
ECS Service
     |
     └── 1 Fargate Task
             |
             └── react-app container
```

---

# Part 13 — Configure Networking

This is one of the most important ECS steps.

Under **Networking**, select your VPC.

For this learning exercise, you can use your **default VPC**.

For example:

```text
VPC:
Default VPC

Subnets:
Select a subnet
```

---

# Step 22 — Create Security Group

Create a new security group.

For example:

```text
Security group name:
react-app-sg
```

Add an inbound rule:

```text
Type:
Custom TCP

Port range:
3000

Source:
0.0.0.0/0
```

So the rule is:

```text
Custom TCP | 3000 | 0.0.0.0/0
```

### Why port 3000?

Because your application is listening inside the container on:

```text
3000
```

The traffic flow is:

```text
Internet
    |
    | TCP 3000
    ↓
Security Group
    |
    | TCP 3000
    ↓
Fargate Task
    |
    | TCP 3000
    ↓
React Container
```

---

# Step 23 — Enable Public IP

For this first learning deployment, enable:

```text
Auto-assign public IP:
ENABLED
```

This allows your Fargate task to receive a public IPv4 address.

> For production, I recommend using an Application Load Balancer rather than exposing the Fargate task directly.

---

# Step 24 — Create the Service

Review the configuration:

```text
Cluster:
react-app-cluster

Service:
react-app-service

Launch:
Fargate

Desired tasks:
1

Container:
react-app

Container port:
3000

Security group:
react-app-sg

Public IP:
Enabled
```

Click:

**Create**

---

# Part 14 — Verify Fargate Task

Go to:

```text
ECS
 ↓
Clusters
 ↓
react-app-cluster
 ↓
Services
 ↓
react-app-service
 ↓
Tasks
```

You should eventually see:

```text
Desired:
1

Running:
1

Pending:
0
```

You want:

```text
Running = 1
```

---

# Step 25 — Find the Public IP

Click the running task.

Go to the **Networking** section.

You should see something like:

```text
Private IP:
172.31.x.x

Public IP:
13.xxx.xxx.xxx
```

Copy the **Public IP**.

---

# Step 26 — Open the Application

Because your application is running on port `3000`, open:

```text
http://YOUR_PUBLIC_IP:3000
```

For example:

```text
http://13.201.xxx.xxx:3000
```

Your application should now be accessible from your browser.

---

# Important — Check Your Docker Application

There is one common issue with applications running inside Docker.

Your application must listen on:

```text
0.0.0.0
```

and **not only**:

```text
localhost
```

For example, if your Node.js application uses:

```javascript
app.listen(3000, '0.0.0.0');
```

that's correct.

If you're using Vite, it should similarly bind to:

```text
0.0.0.0
```

Otherwise the application may work inside the container but not be accessible from the Internet.

---

# Complete Architecture

After completing these steps, your AWS architecture will be:

```text
                         Internet
                            |
                            |
                       Public IP
                            |
                            | TCP 3000
                            ↓
                  ┌───────────────────┐
                  │ Security Group    │
                  │                   │
                  │ Allow TCP 3000    │
                  └─────────┬─────────┘
                            |
                            ↓
                  ┌───────────────────┐
                  │   ECS Fargate     │
                  │                   │
                  │   Task            │
                  │      |            │
                  │      ↓            │
                  │  React Container  │
                  │      :3000        │
                  └─────────┬─────────┘
                            ↑
                            |
                       Pull image
                            |
                            |
                  ┌───────────────────┐
                  │       ECR         │
                  │                   │
                  │ ravinder/react-app│
                  │      :latest      │
                  └───────────────────┘
```

---

# What each ECS component does

This is worth putting in your DevOps notes:

| Component           | Purpose                                               |
| ------------------- | ----------------------------------------------------- |
| **ECR**             | Stores Docker images                                  |
| **ECS Cluster**     | Logical grouping for ECS workloads                    |
| **Task Definition** | Blueprint for running a container                     |
| **Task**            | Actual running container workload                     |
| **Fargate**         | Serverless compute that runs the task                 |
| **Service**         | Maintains the desired number of tasks                 |
| **Security Group**  | Controls network traffic                              |
| **Public IP**       | Allows direct Internet access for this learning setup |

---

# Complete Flow

Your document can now have this complete flow:

```text
Part 1
Create ECR Repository
        ↓
Part 2
Install AWS CLI
        ↓
Part 3
Configure AWS Credentials
        ↓
Part 4
Validate AWS Configuration
        ↓
Part 5
Login Docker to ECR
        ↓
Part 6
Check Local Docker Image
        ↓
Part 7
Tag Docker Image
        ↓
Part 8
Verify Docker Tag
        ↓
Part 9
Push Image to ECR
        ↓
Part 10
Verify Image in ECR
        ↓
Part 11
Create ECS Cluster
        ↓
Part 12
Create Task Definition
        ↓
Part 13
Configure Container
        ↓
Part 14
Create ECS Service
        ↓
Part 15
Configure Networking
        ↓
Part 16
Configure Security Group
        ↓
Part 17
Enable Public IP
        ↓
Part 18
Run Fargate Task
        ↓
Part 19
Get Public IP
        ↓
Part 20
Open Application
        ↓
http://PUBLIC-IP:3000
```

# ⚠️ IMPORTANT — Stop/Delete AWS Resources After Demo

> **AWS can charge you for resources that continue running after your demo.**
>
> If you created AWS resources only for learning or testing, **stop or delete them after completing the demo**.
>
> **Do not assume that deleting the ECS cluster automatically deletes everything you created.** Some resources can continue running independently and may generate charges.

## 🛑 AWS Demo Cleanup Checklist

After completing the ECS/Fargate demo, check the following resources.

| Resource           | What to do                       | Why                                     |
| ------------------ | -------------------------------- | --------------------------------------- |
| ECS Service        | **Delete**                       | Can keep Fargate tasks running          |
| Fargate Tasks      | **Stop**                         | Fargate compute can incur charges       |
| ECS Cluster        | **Delete**                       | Clean up demo environment               |
| Load Balancer      | **Delete** if created            | Can continue generating charges         |
| NAT Gateway        | **Delete** if created            | Can be a significant ongoing cost       |
| Elastic IP         | **Release** if unused            | Public IPv4 addresses can incur charges |
| ECR Image          | **Delete** if no longer needed   | ECR storage can incur charges           |
| ECR Repository     | **Delete** if no longer needed   | Removes stored images                   |
| CloudWatch Logs    | **Delete/expire** if appropriate | Log storage can incur charges           |
| EC2 instances      | **Terminate** if created         | Running instances incur charges         |
| RDS database       | **Delete** if demo-only          | Database instances can be expensive     |
| S3 objects/buckets | **Delete** if demo-only          | Storage can incur charges               |

---

# Part 21 — Stop/Delete ECS Resources

## Step 1 — Delete ECS Service

Go to:

```text
AWS Console
    ↓
ECS
    ↓
Clusters
    ↓
react-app-cluster
    ↓
Services
```

Select:

```text
react-app-service
```

Choose:

```text
Delete service
```

Confirm deletion.

---

## Step 2 — Stop Fargate Tasks

Go to:

```text
ECS
 ↓
Clusters
 ↓
react-app-cluster
 ↓
Tasks
```

Check the task status.

You want:

```text
Running tasks: 0
```

If a task is still running:

```text
Select Task
    ↓
Stop
    ↓
Confirm
```

### ⚠️ Important

**Stopping the task is more important than deleting the cluster.**

Fargate charges are associated with the compute resources used by running tasks.

---

# Step 3 — Delete ECS Cluster

Once the service and tasks are gone:

```text
ECS
 ↓
Clusters
 ↓
react-app-cluster
 ↓
Delete
```

Confirm:

```text
Delete cluster
```

Your ECS environment should now be removed.

---

# Part 22 — Check Load Balancer

If you created an **Application Load Balancer**, deleting the ECS service/cluster does not necessarily mean the ALB is gone.

Go to:

```text
EC2
 ↓
Load Balancers
```

Look for a load balancer created for this demo.

If you don't need it:

```text
Select Load Balancer
        ↓
Actions
        ↓
Delete load balancer
```

### ⚠️ Important

Load Balancers can continue generating charges even when your application is no longer running.

---

# Part 23 — Check NAT Gateway

This is one of the **most important resources to check**.

Go to:

```text
VPC
 ↓
NAT Gateways
```

If you created a NAT Gateway specifically for your ECS demo and don't need it anymore:

```text
Select NAT Gateway
        ↓
Delete NAT Gateway
```

### ⚠️ Important

A NAT Gateway can generate charges while it exists, even if your ECS application is stopped.

**Always check NAT Gateways when cleaning up AWS demo environments.**

---

# Part 24 — Check Elastic IP Addresses

Go to:

```text
EC2
 ↓
Elastic IPs
```

Look for unused Elastic IP addresses.

If you don't need one:

```text
Select Elastic IP
       ↓
Release Elastic IP address
```

Don't release an IP that is being used by another application.

---

# Part 25 — Clean Up ECR

You created:

```text
ravinder/react-app
```

with:

```text
latest
```

If you want to keep the image for your next ECS exercise, **you can keep ECR**.

If this was only a temporary demo:

```text
ECR
 ↓
Private repositories
 ↓
ravinder/react-app
 ↓
Delete
```

This removes the repository and its images.

### My recommendation

For your DevOps learning:

```text
ECS Cluster       → DELETE
ECS Service       → DELETE
Fargate Tasks     → STOP/REMOVE
ALB               → DELETE if created
NAT Gateway       → DELETE if created
Unused Elastic IP → RELEASE
ECR               → KEEP
```

Keep the ECR image because you'll probably use it again for your next ECS deployment.

---

# Part 26 — Check CloudWatch Logs

ECS can send container logs to CloudWatch.

Go to:

```text
CloudWatch
 ↓
Logs
 ↓
Log groups
```

You may see something like:

```text
/ecs/react-app-task
```

If this was only a demo and you don't need the logs:

```text
Select Log Group
       ↓
Delete
```

Alternatively, configure a **retention period** instead of keeping logs indefinitely.

---

# Part 27 — Check EC2

Even though you're using Fargate, always check EC2 because you may have accidentally created other resources.

Go to:

```text
EC2
 ↓
Instances
```

Make sure you don't have unnecessary instances in:

```text
Running
```

If you created one only for testing:

```text
Instance
 ↓
Terminate instance
```

> **Stop** and **Terminate** are different. For a demo-only EC2 instance you no longer need, **Terminate** is normally the cleanup action.

---

# Part 28 — Check RDS

If you created a database for another demo:

```text
RDS
 ↓
Databases
```

Check whether any database is running.

For a demo-only database you no longer need:

```text
Select database
 ↓
Delete
```

Be careful: deleting an RDS database can permanently remove data depending on the deletion/snapshot options you choose.

---

# Part 29 — Check S3

Go to:

```text
S3
 ↓
Buckets
```

If you created a bucket only for the demo:

1. Delete the objects.
2. Delete the bucket.

You cannot delete a non-empty S3 bucket.

---

# Part 30 — Check AWS Billing

After cleanup, verify your account.

Go to:

```text
Billing & Cost Management
```

Check:

```text
Bills
```

and:

```text
Cost Explorer
```

Also check:

```text
Free Tier
```

Look for unexpected usage.

---

# ⭐ Final AWS Cleanup Checklist

I would put this at the **very end of your document** as a prominent warning:

```text
╔══════════════════════════════════════════════════════╗
║              ⚠️ AWS DEMO CLEANUP                    ║
╠══════════════════════════════════════════════════════╣
║                                                      ║
║  After completing the demo, check:                  ║
║                                                      ║
║  ☐ ECS Service              → DELETE                ║
║  ☐ Fargate Tasks             → STOP / 0 RUNNING     ║
║  ☐ ECS Cluster               → DELETE                ║
║  ☐ Load Balancer             → DELETE if unused      ║
║  ☐ NAT Gateway               → DELETE if unused      ║
║  ☐ Elastic IP                → RELEASE if unused     ║
║  ☐ EC2 Instances             → TERMINATE if unused   ║
║  ☐ RDS Database              → DELETE if demo-only   ║
║  ☐ ECR Images                → DELETE if unnecessary ║
║  ☐ S3 Buckets                → DELETE if demo-only   ║
║  ☐ CloudWatch Logs           → DELETE/RETENTION      ║
║                                                      ║
║  Finally:                                            ║
║  ☐ Check AWS Billing                             ║
║  ☐ Check Cost Explorer                          ║
║  ☐ Check Free Tier usage                        ║
║                                                      ║
╚══════════════════════════════════════════════════════╝
```

### 🚨 Important Note for AWS Beginners

> **Creating an AWS resource and stopping your application are not the same thing as deleting the resource.**
>
> Always check the AWS resources you created after finishing a demo. Some resources—especially **NAT Gateways, Load Balancers, public IPv4 addresses, databases, and running compute**—can continue to incur charges.
>
> **Before finishing an AWS learning session, review your resources and Billing dashboard.**

For your specific **ECR → ECS Fargate** tutorial, the minimum cleanup is:

```text
ECS Service
     ↓
Delete

Fargate Task
     ↓
0 Running

ECS Cluster
     ↓
Delete

ALB
     ↓
Delete (if created)

NAT Gateway
     ↓
Delete (if created)

Elastic IP
     ↓
Release (if unused)
```

**ECR can be kept** because it is where your Docker image lives and you'll likely reuse `ravinder/react-app:latest` in the next ECS exercise.


