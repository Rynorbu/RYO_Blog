---
title: "Jenkins"
published: 2026-03-06
description: "Core concepts about Jenkins, CI/CD, and automation in software development."
# image: "../../assets/images/my_photo.jpg"
tags: ["Jenkins", "CI-CD", "DevOps", "Automation", "Pipeline", "DSO101"]
category: DevOps
draft: false
---

## Introduction

Jenkins is an open-source automation server used mainly for continuous integration and continuous delivery/deployment in software projects. It helps teams automatically build, test, and deploy code whenever changes are made, which makes the development process faster and more reliable.

---

## What is Jenkins?

### Overview

Jenkins is written in Java and runs on major operating systems:

- Windows
- Linux
- macOS

It is widely used because it is:

- **Free** — Open-source software
- **Flexible** — Highly customizable
- **Extensible** — Supported by a huge plugin ecosystem

### Core Purpose

At its core, Jenkins is an automation tool. Instead of manually running build commands, test scripts, and deployment steps, Jenkins can do those tasks automatically based on events like code commits or scheduled triggers.

---

## Why Jenkins is Used

Jenkins is used to reduce manual work and catch errors early:

- **Automatic Testing** — When developers push code into a repository, Jenkins can automatically start a pipeline that builds the project, runs tests, and reports the result
- **Quick Feedback** — This gives quick feedback and helps teams fix problems before they grow into bigger issues
- **Team Collaboration** — Everyone can work with the same automated process
- **Wide Compatibility** — Useful in web apps, mobile apps, containerized applications, and infrastructure automation workflows

---

## Core Idea: CI/CD

### Continuous Integration (CI)

Continuous Integration means code changes are merged often and tested automatically. Every commit triggers automated tests to ensure nothing is broken.

### Continuous Delivery (CD)

Continuous Delivery means the software is always kept in a deployable state. Code is tested and packaged automatically, ready to be deployed at any time.

### Continuous Deployment (CD)

Continuous Deployment means code can be deployed automatically after passing all checks, with minimal or no human intervention.

### Why Jenkins Excels at CI/CD

Jenkins is popular because it supports these workflows very well through **pipelines**. A Jenkins pipeline is a defined sequence of stages such as:

- Build
- Test
- Scan
- Package
- Deploy

---

## Main Components

### Controller (Master)

The controller manages the entire Jenkins system:

- Schedules jobs
- Stores configurations
- Manages plugins
- Provides the web interface

### Agents (Workers)

Agents run the actual work like builds and tests. They can be:

- Physical machines
- Virtual machines
- Docker containers
- Cloud instances

Multiple agents allow parallel execution and distribute load.

### Jobs

Jobs are the basic units of work in Jenkins. A job may:

- Compile code
- Run a test suite
- Generate reports
- Deploy an application
- Run custom scripts

### Plugins

Plugins extend Jenkins and make it very flexible. Through plugins, Jenkins can integrate with:

- **Version Control** — Git, GitHub, GitLab, Bitbucket
- **Containerization** — Docker, Kubernetes
- **Cloud Platforms** — AWS, Azure, Google Cloud
- **Communication** — Slack, email, Teams
- **Build Tools** — Maven, Gradle, Ant
- **Testing** — JUnit, pytest, SonarQube
- **Deployment** — Ansible, Terraform, CloudFormation

---

## Jenkins Pipelines

### What is a Pipeline?

Pipelines are one of the most important Jenkins features. They let you define the entire software delivery process as code, which makes it easier to:

- Version control
- Review changes
- Reuse across projects

### Pipeline Code

This code is often stored in a **Jenkinsfile** inside the project repository, making it part of the source control.

### Pipeline Stages

A pipeline usually contains stages such as:

```
┌──────────────────────────────────────────┐
│           Jenkins Pipeline                │
├──────────────────────────────────────────┤
│ 1. Build      → Compile source code      │
│ 2. Test       → Run unit & integration   │
│ 3. Scan       → Code quality analysis    │
│ 4. Package    → Create artifacts         │
│ 5. Deploy     → Release to environment   │
└──────────────────────────────────────────┘
```

### Advanced Pipeline Features

Jenkins also supports:

- **Parallel Stages** — Run multiple stages simultaneously
- **Parameterized Builds** — Pass variables to customize behavior
- **Shared Libraries** — Reusable pipeline logic across projects
- **Conditional Steps** — Execute steps based on conditions

This is especially useful in larger projects where many similar jobs must follow the same workflow.

### Example Jenkinsfile

```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'mvn test'
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                sh './deploy.sh'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
```

---

## How Jenkins Works

### Typical Workflow

1. **Developer Commits** — A developer commits code to a source control system like Git
2. **Jenkins Detects Change** — Jenkins detects the change through:
   - **Polling** — Regularly checking the repository
   - **Webhook** — Instant notification from Git server
3. **Pipeline Starts** — Jenkins starts the pipeline automatically
4. **Stages Execute** — Jenkins executes each stage in sequence
5. **Results Stored** — Results are stored in build history
6. **Notifications Sent** — Jenkins sends notifications (email, Slack, etc.)

### Failure Handling

If a stage fails:

- Jenkins stops the pipeline
- Shows the error in console output
- Developer can debug the issue
- Fix is committed and pipeline runs again

### Success Handling

If everything passes:

- Jenkins marks the build as successful
- Sends success notifications
- May trigger downstream jobs
- Artifacts are stored for deployment

---

## Installation and Setup

### Prerequisites

- Java Development Kit (JDK) 8 or higher
- 256 MB+ RAM
- 1 GB+ disk space

### Installation Options

#### Option 1: Direct Installation

```bash
# Linux (Ubuntu/Debian)
sudo apt-get update
sudo apt-get install openjdk-11-jdk
wget https://pkg.jenkins.io/debian-stable/binary/jenkins_2.x.x_all.deb
sudo dpkg -i jenkins_2.x.x_all.deb

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

#### Option 2: Docker Installation

```bash
# Run Jenkins in Docker
docker run -d -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  --name jenkins \
  jenkins/jenkins:lts
```

### Initial Setup

1. **Access Jenkins** — Open `http://localhost:8080`
2. **Unlock Jenkins** — Enter the initial admin password
3. **Install Plugins** — Choose recommended or custom plugins
4. **Create Admin User** — Set up your admin account
5. **Configure System** — Set up agents, tools, and notifications

### Creating Your First Job

1. Click "New Item"
2. Enter a job name
3. Choose job type (Freestyle or Pipeline)
4. Configure source repository
5. Define build steps
6. Save and run

---

## Common Features

### Build Automation

Jenkins automatically compiles code and generates builds without manual intervention.

### Test Automation

Jenkins runs test suites automatically and reports results:

- Unit tests
- Integration tests
- UI tests
- Performance tests

### Deployment Automation

Jenkins automatically packages and deploys applications to:

- Development environments
- Staging servers
- Production systems

### Notifications

Jenkins sends build status updates via:

- Email
- Slack
- Microsoft Teams
- Custom webhooks

### Detailed Logs and Monitoring

| Feature | Purpose |
|---------|---------|
| **Console Output** | Detailed build logs for debugging |
| **Build History** | View all past builds and results |
| **Stage View** | Visual representation of pipeline progress |
| **Artifacts** | Download build outputs |
| **Test Results** | See detailed test reports |

### Useful Jenkins Features

```bash
# Webhooks
- Instant build triggers when code is pushed

# Scheduled Builds
- Use cron syntax for scheduled jobs
  Example: H H * * * (run daily at midnight)

# Console Output
- Full logs for debugging failures

# Stage View
- Visual pipeline tracking and progress

# Plugins
- Extend functionality for any tool or language
```

---

## Advantages of Jenkins

### Cost-Effective

- Open source — No licensing costs
- Free for individuals and organizations
- Can run on modest hardware

### Highly Extensible

- Huge plugin ecosystem (1000+ plugins)
- Adapt to many different programming languages
- Works with any tool or cloud environment
- Support for custom scripts

### Automation Benefits

Jenkins reduces human error by:

- **Automation** — Reduces human error
- **Speed** — Speeds up delivery cycles
- **Consistency** — Improves consistency across development, testing, and deployment
- **Feedback** — Better code quality through continuous feedback
- **Reliability** — Faster release cycles with fewer bugs

---

## Limitations

### Complexity

- Can become complex to manage in large environments
- Many plugins and jobs need maintenance
- Poorly designed Jenkins may have performance issues

### Security and Planning

A poorly designed Jenkins system may require:

- Careful security hardening
- Backup strategy planning
- Scaling and resource planning
- User and credential management

### Learning Curve

Jenkins is powerful, but it is not always the simplest option for beginners. To use it well, you need to understand:

- Pipelines and Jenkinsfile syntax
- Credentials and secrets management
- Agents and distributed builds
- Plugins and their configuration
- Source control integration

---

## Example Workflow

### Basic Jenkins Workflow

Here's a common workflow for deploying a web application:

```
1. Developer pushes code to Git
        ↓
2. Jenkins detects the change via webhook
        ↓
3. Jenkins pulls the latest code from repository
        ↓
4. Jenkins builds the project (compile, package)
        ↓
5. Jenkins runs automated tests (unit, integration, E2E)
        ↓
6. Jenkins performs code quality scanning (SonarQube)
        ↓
7. Jenkins packages the application (Docker image, JAR, etc.)
        ↓
8. Jenkins deploys to staging environment for testing
        ↓
9. Upon approval, Jenkins deploys to production
        ↓
10. Jenkins sends success notification to team
```

### Why This Workflow is Valuable

This is the reason Jenkins is so valuable in modern DevOps practices because:

- Developers get immediate feedback on code quality
- Bugs are caught early before reaching production
- Deployment is faster and more reliable
- Teams can release multiple times per day
- Reduces manual, repetitive work

---

## Important Terms

### CI/CD Terms

| Term | Definition |
|------|-----------|
| **CI** | Continuous Integration — automatic merging and testing of code |
| **CD** | Continuous Delivery/Deployment — automated release process |
| **Pipeline** | A sequence of automated build and deployment stages |
| **Jenkinsfile** | Code defining a pipeline, stored in repository |

### Jenkins Components

| Term | Definition |
|------|-----------|
| **Job** | A task performed by Jenkins (build, test, deploy) |
| **Plugin** | An extension that adds new features to Jenkins |
| **Agent** | A machine that executes build steps |
| **Controller** | Master Jenkins server that schedules and manages jobs |
| **Build** | One execution of a job |
| **Artifact** | Output file from a build (JAR, Docker image, etc.) |

### Workflow Terms

| Term | Definition |
|------|-----------|
| **Stage** | A phase in a pipeline (Build, Test, Deploy) |
| **Step** | An individual command or action within a stage |
| **Webhook** | Automatic trigger sent by Git server |
| **Polling** | Jenkins checking repository for changes |
| **Build History** | Record of all past builds |

---

## Quick Reference: Common Jenkins Commands

### Via Web Interface

```
1. Create new job: Dashboard → New Item
2. Configure job: Job page → Configure
3. Build manually: Job page → Build Now
4. View logs: Build page → Console Output
5. Manage plugins: Manage Jenkins → Manage Plugins
6. Create credentials: Manage Jenkins → Manage Credentials
```

### Via Jenkins CLI

```bash
# Get Jenkins version
java -jar jenkins-cli.jar -s http://localhost:8080 version

# List jobs
java -jar jenkins-cli.jar -s http://localhost:8080 list-jobs

# Build a job
java -jar jenkins-cli.jar -s http://localhost:8080 build job-name

# Get job info
java -jar jenkins-cli.jar -s http://localhost:8080 get-job job-name
```

---

## Summary

Jenkins is a powerful, open-source automation server that:

- **Reduces Manual Work** — Automates build, test, and deployment
- **Catches Errors Early** — Provides rapid feedback on code quality
- **Supports CI/CD** — Implements continuous integration and deployment
- **Highly Extensible** — Works with almost any tool via plugins
- **Improves Quality** — Consistent, repeatable processes reduce errors

By mastering Jenkins pipelines, plugins, and configuration, you can:

- Accelerate software delivery
- Improve code quality
- Reduce deployment risks
- Enable team collaboration
- Support modern DevOps practices
