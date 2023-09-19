# EKS Upgrade Workshop
<!--- # In-place EKS Upgrade workshop -->
<!--
** P'Jade **
-->

Welcome to the **EKS Upgrade** workshop. This workshop aims to share recommended practice and hands-on step for EKS upgrade operation. Customer will learn how to upgrade EKS cluster from EKS version 1.24 to 1.25 with example Kubernetes application to demonstrate step that need to check before performing the upgrade. 

The workshop is structured into three parts: Start the workshop , Upgrade planning and preparations , and Implement EKS Upgrade.

### Start the workshop

In this section , New EKS Cluster need to be created and deploy with sample application that will be used throughout the workshop.

### Upgrade planning and preparations

The first part of our workshop provides the procedure before perform EKS Upgrade such as Deprecated API , AWS Managed AddOn Version compatibility and helper tools that can be used to shorten the preparation process.


### Implement EKS Upgrade

After the planning phase , this section will provider customer about actual step and command that need to be performed including checkpoint guideline to check the upgrade completeness in each step.

## Takeaways

By the end of this workshop you will have learned the following:

 - Using `kubent` to verify deprecated API object
 - Using `kubectl-convert` to convert deprecated API object to new version
 - Upgrading your EKS control plane.
 - Upgrading your managed node group.

<!--
- Container basics and essential _Docker_ commands
- How to create a _Dockerfile_ to containerize your application
- How to push your container to Amazon Elastic Container Registry (ECR)
- How to deploy a container to App Runner
- How to deploy an App Runner service using a custom VPC connector
- How to use observability tools such as AWS X-Ray and CloudWatch to see application metrics
-->


## Table of contents

1. [Start the workshop](/start-workshop.md)

 
<!--
** P'Jade/Pup **
- Workshop Studio instructions 
- Setting up Apps 
- Install the Cron manifest
- Install HPA Add-on
- Checking that the app works
-->
2. [Upgrade planning and preparations](/prepare.md)
<!-- 
** Bo **
- Install kubectl +/- 1 from cluster's
- Install Kube no trouble + show PSP & Cron will be obsolete
- Use Kubeconvert to convert the Cron manifest
  * Dump manifest for Cron & HPA
  * Run kubeconvert for Cron & HPA
  * Apply update for Cron manifest & (HPA) (v1.25 does not support Cron/HPA Beta API)
- Check MNG worker node version
  * kubectl get node
- Check k8s versions (both client and cluster)  
  * kubectl version short
-->
3. EKS Upgrade
   1. [Upgrade control plane](/upgrade-control-plane.md)
   2. [Upgrade data plane](/upgrade-data-plane.md)
<!-- 
** Joe ** Jade comment > Note sure that step should be according to this : https://aws.github.io/aws-eks-best-practices/upgrades/ under topics :               Under topic : Upgrade your control plane and data plane in sequence¶
           Deprecated API Using KubeNT > Control Plane > Add on > Data Plane
- Note: to backup before performing the upgrade (e.g. use Velero)
- Upgrade control plane
- Upgrade data plane
  * Show configure PDB
  * Upgrade data plane
  * Show app zero downtime: 1/ at least one pod still available 2/ app can respond to traffic
- Post-check
  * Check cluster version
  * Check data plane
  * Check application working as expected
-->
