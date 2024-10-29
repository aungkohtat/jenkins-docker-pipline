# jenkins-docker-pipline
# Jenkins CI/CD Pipeline Setup Guide

This guide walks through setting up Jenkins with Docker for continuous integration and deployment. Follow these steps to get your pipeline up and running.

## Prerequisites

- Ubuntu/Debian-based system
- Sudo privileges
- Git installed

## Installation Steps

### 1. Install Jenkins

First, install Jenkins by following the official installation guide for Linux:
[Jenkins Installation Guide](https://www.jenkins.io/doc/book/installing/linux/)

### 2. Install Docker

Install Docker using apt:

```bash
sudo apt update
sudo apt install docker.io
```

Verify Docker installation:
```bash
docker --version
```

### 3. Configure Jenkins-Docker Permissions

Jenkins needs proper permissions to run Docker commands. Follow these steps:

1. Check current groups for Jenkins user:
```bash
groups jenkins
```

2. Add Jenkins user to Docker group:
```bash
sudo usermod -aG docker jenkins
```

3. Restart Jenkins service:
```bash
sudo systemctl restart jenkins
```

4. Verify group addition:
```bash
groups jenkins
```

### 4. Jenkins Pipeline Setup

1. Create a new Pipeline job in Jenkins
2. Add the following Jenkinsfile to your repository:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/aungkohtat/react_django_demo_app.git'
            }
        }
        
        stage('Testing') {
            steps {
                echo "Testing"
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build --no-cache -t react_django_demo_app ."
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    sh "docker run -p 8001:8001 -d react_django_demo_app"
                }
            }
        }
    }
}
```

## Pipeline Stages

The pipeline consists of four stages:

1. **Checkout**: Clones the repository
2. **Testing**: Currently outputs a test message (customize as needed)
3. **Build Docker Image**: Builds the application's Docker image
4. **Run Docker Container**: Deploys the container on port 8001

## Troubleshooting

### Common Issues

1. **Docker Permission Denied**
   ```bash
   # Check if Jenkins user is in Docker group
   groups jenkins
   
   # If not listed, add to group and restart
   sudo usermod -aG docker jenkins
   sudo systemctl restart jenkins
   ```

2. **Port Already in Use**
   ```bash
   # Check if port 8001 is in use
   sudo lsof -i :8001
   
   # Stop existing container
   docker ps
   docker stop <container_id>
   ```

### Verification Steps

1. Check Jenkins service status:
   ```bash
   sudo systemctl status jenkins
   ```

2. Verify Docker service:
   ```bash
   sudo systemctl status docker
   ```

3. Test Docker permissions:
   ```bash
   sudo -u jenkins docker ps
   ```

## Security Considerations

- Regularly update Jenkins and Docker to their latest versions
- Use credentials management in Jenkins for sensitive information
- Consider implementing additional security measures like:
  - SSH keys for Git authentication
  - Docker image scanning
  - Network segmentation

## Additional Resources

- [Jenkins Documentation](https://www.jenkins.io/doc/)
- [Docker Documentation](https://docs.docker.com/)
- [Jenkins Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)

