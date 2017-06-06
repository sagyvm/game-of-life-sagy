
This demo will show how to set up a Docker-based build and deploy pipeline in Jenkins.
- Master in AWS running Jenkins 2.7.2
- Agent is an AWS EC2 instance with Docker, Maven, Git, and Java 8 installed on Ubuntu connected to Jenkins with an SSH Connector
- Staging server is running in ECS
- Deployment server is running in ECS

# Required Plugins
- CloudBees Docker Pipeline Plugin
- Pipeline Plugin
- Docker Plugin
- Pipeline Stage View Plugin
- CloudBees Docker Build and Publish Plugin
- CloudBees Custom Build Environments Plugin
- CloudBees AWS CLI Plugin - CJE proprietary plugin

# Required agent tools
- Maven
- Java
- Git
- Docker w/ running Docker daemon

# Actual Flow
- A Jenkins pipeline job builds this project
- Any push to the source code will trigger pipeline to:
- Pull the source code from this GitHub repostiory
- Run the Maven tests
- Build the application
- Package the application in a Docker image using this repo's Dockerfile
- Deploy the application's Docker image to DockerHub with a "docker-demo" tag
- Deploy the image to a staging server in Amazon's container service (ECS) and prompt for manual approval
- Kill the previously running production container in ECS
- Deploy the latest image into ECS

# Additional Reading
- [How to point to a custom registry (e.g. local)](http://documentation.cloudbees.com/docs/cje-user-guide/docker-workflow.html)
- [Setting up Jenkins slaves on AWS](https://www.cloudbees.com/blog/setting-jenkins-ec2-slaves)
- [Game of Life pipeline deployment to ECS](https://github.com/cyrille-leclerc/game-of-life/blob/amazon-ecs-pipeline/Jenkinsfile)
- [Alternative approach to build pipeline with Jenkins and ECS](https://blogs.aws.amazon.com/application-management/post/Tx32RHFZHXY6ME1/Set-up-a-build-pipeline-with-Jenkins-and-Amazon-ECS)
- [Creating an ECS cluster with ELB instead of static instances](http://www.ybrikman.com/writing/2015/11/11/running-docker-aws-ground-up/)
- [Building multiple containers with Docker Compose and pipeline](https://www.cloudbees.com/blog/docker-flow-proxy-%E2%80%93-demand-haproxy-service-discovery-and-reconfiguration-jenkins-pipeline)
