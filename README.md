# EKS Upgrade Workshop
<!--- # In-place EKS Upgrade workshop -->
<!--
** P'Jade **
-->

Welcome to the **EKS Upgrade** workshop. This workshop aims to share recommended practice and hands-on step for EKS upgrade operation. Customer will learn how to upgrade EKS cluster from EKS version 1.24 to 1.25 with example Kubernetes application to demonstrate step that need to check before performing the upgrade. 

The workshop is structured into two parts: Planning and preparation, and Implement Amazon EKS upgrade.

### Planning

The first part of our workshop provides ...

### Implement EKS Upgrade

This part of our workshop will ...

## Takeaways

By the end of this workshop you will have learned the following:

<!--
- Container basics and essential _Docker_ commands
- How to create a _Dockerfile_ to containerize your application
- How to push your container to Amazon Elastic Container Registry (ECR)
- How to deploy a container to App Runner
- How to deploy an App Runner service using a custom VPC connector
- How to use observability tools such as AWS X-Ray and CloudWatch to see application metrics
-->


## Table of contents

0. [Start the workshop](/start-workshop.md)
<!--
** P'Jade **
- Workshop Studio instructions 
- Setting up Apps 
- Install the Cron manifest
- Install EBS CSI Add-on
- Install HPA Add-on
- Checking that the app works
-->
1. [Upgrade planning and preparations](/prepare.md)
<!-- 
** Yo **
- Install Kube no trouble + show PSP & Cron will be obsolete
- Use Kubeconvert to convert the Cron manifest
  * Dump manifest for Cron & HPA
  * Run kubeconvert for Cron & HPA
  * Apply update for Cron manifest (v1.25 does not support Cron Beta API)
- Check add-ons (use EBS CSI AWS Managed Add-on as an example
  * Open EBS CSI add-on release note to verify that the target upgrade version will be supported
- Check MNG worker node version
  * kubectl get node
-->
2. [EKS Upgrade](/eks-upgrade.md)
<!-- 
** Pup ** Jade comment > Note sure that step should be according to this : https://aws.github.io/aws-eks-best-practices/upgrades/ under topics :               Under topic : Upgrade your control plane and data plane in sequence¶
           Deprecated API Using KubeNT > Control Plane > Add on > Data Plane
- Note: to backup before performing the upgrade (e.g. use Velero)
- Upgrade control plane
- Upgrade data plane
  * Show configure PDB
  * Upgrade data plane
  * Show app zero downtime: 1/ at least one pod still available 2/ app can respond to traffic
** P'Joe **
- Check/verify that add-ons are still working & upgrade add-ons
  * Show add-on functional
  * Can upgrade add-ons in this step
- Upgrade applications
  * Candidate to show applying HPA manifest update
- Post-check
  * Check cluster version
  * Check data plane
  * Check application working as expected
-->
