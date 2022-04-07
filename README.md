---
pre: <b>6. </b>
title: Fargate
weight: 120
---
*## 1. Introduction*
AWS Fargate is a technology that provides on-demand, right-sized compute capacity for containers. With AWS Fargate, you don't have to provision, configure, or scale groups of virtual machines on your own to run containers. You also don't need to choose server types, decide when to scale your node groups, or optimize cluster packing. You can control which pods start on Fargate and how they run with Fargate profiles. Fargate profiles are defined as part of your Amazon EKS cluster.
Amazon EKS integrates Kubernetes with AWS Fargate by using controllers that are built by AWS using the upstream, extensible model provided by Kubernetes. These controllers run as part of the Amazon EKS managed Kubernetes control plane and are responsible for scheduling native Kubernetes pods onto Fargate. The Fargate controllers include a new scheduler that runs alongside the default Kubernetes scheduler in addition to several mutating and validating admission controllers. When you start a pod that meets the criteria for running on Fargate, the Fargate controllers that are running in the cluster recognize, update, and schedule the pod onto Fargate.
*## 2. Pre-requisite* 
1. We assume that you have completed the chapter [Creating Nodegroup](_/introduction/nodegroup_) to ensure we have bigger instance type for this chapter.
2. You have cloned this repository and installed the Product Catalog Application using Helm. For more information refer to [Helm](_/helm/deploy_) chapter
***In this chapter***, we will focus on Application Deployment of Product Catalog Backend microservice on AWS Fargate:
1. How to create Fargate proflie
2. Redeploy Product Catalog Backend microservice on AWS Fargate
    - Testing - 
        - Test the application 
![Fargate](_/static/images/fargate-arch.png_)
