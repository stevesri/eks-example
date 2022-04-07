---
pre: <b>6. </b>
title: Cleanup
weight: 20
---
*## 1. Delete Fargate Profile*
Create a file clusterconfig.yaml with below contents
```bash
eksctl delete fargateprofile --name fargate-productcatalog --cluster eksworkshop-eksctl
```
*## 2. Redeploy Application*
Let's redeploy the application and this time Product Catalog Backend Microservice should use fargate profile to be spun up in fargate
```bash
cd ~/environment/eks-app-mesh-polyglot-demo
helm upgrade workshop ~/environment/eks-app-mesh-polyglot-demo/workshop/helm-chart/
```
Let's check if pods are running back on node group instances with below command
```bash
kubectl get pods -n workshop -o wide
```
```
NAME                           READY   STATUS    RESTARTS   AGE   IP               NODE                                          NOMINATED NODE   READINESS GATES
frontend-695f9f586f-xxxxx      1/1     Running   0          16d   192.168.XX.XX    ip-192-168-XX-XX.us-west-2.compute.internal   <none>           <none>
prodcatalog-6cc8ff6db9-xxxxx   1/1     Running   0          16d   192.168.XX.XX     ip-192-168-XX-XX.us-west-2.compute.internal   <none>           <none>
proddetail-6dbddf7fd7-xxxxx    1/1     Running   0          40h   192.168.XX.XX   ip-192-168-XX-XX.us-west-2.compute.internal   <none>           <none>
```
