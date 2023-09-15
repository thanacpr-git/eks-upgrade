# Containers Zero to One Workshop
<!--- # docker-conprehensive-lab-guide -->

Welcome to the **Containers Zero to One** workshop. This workshop aims to provide developers and DevOps engineers with _little or no containers experience_ some hands-on practice from the ground up. We start from containerizing an applicaiton, to deploying a practical containerized application on AWS.

The workshop is structured into two parts: Containers & Docker, and Deploying your container service on AWS App Runner.

### Containers & Docker

The first part of our workshop provides an introduction to _containers_ and _Docker_. This two-part lab begins with Docker basics, covering how to use the _Docker cli_ to manipulate container instances and images. The second part focuses on building a container image using a _Dockerfile_ and creating a production-grade container image.

### AWS App Runner

This part of our workshop will launch our example containerized hotel applicaiton from part one as a service on AWS App Runner. We will configure a _VPC Connector_ for App Runner to allow our application to communicate with our RDS database in our VPC, experiment with autoscaling, and observability for our application in App Runner.

## Takeaways

By the end of this workshop you will have learned the following:

- Container basics and essential _Docker_ commands
- How to create a _Dockerfile_ to containerize your application
- How to push your container to Amazon Elastic Container Registry (ECR)
- How to deploy a container to App Runner
- How to deploy an App Runner service using a custom VPC connector
- How to use observability tools such as AWS X-Ray and CloudWatch to see application metrics


## Table of contents

0. [Start the workshop](/start-workshop.md)
1. [Docker basics](/docker-basics.md)
2. [Practical Dockerfile](/dockerfile-practice.md)
3. [ECR](/ecr.md)
4. [Deploy to App Runner](/apprunner-service.md)
5. [Auto Scaling](/autoscaling.md)
6. [Observability](/observability.md)
