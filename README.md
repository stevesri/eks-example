---
pre: <b>6. </b>
title: Deploy Fargate
weight: 10
---
*## 1. Create Fargate Profile*
The Fargate profile allows an administrator to declare which pods run on Fargate. Each profile can have up to five selectors that contain a namespace and optional labels. You must define a namespace for every selector. The label field consists of multiple optional key-value pairs. Pods that match a selector (by matching a namespace for the selector and all of the labels specified in the selector) are scheduled on Fargate.
It is generally a good practice to deploy user application workloads into namespaces other than kube-system or default so that you have more fine-grained capabilities to manage the interaction between your pods deployed on to EKS. In this chapter we will create a new Fargate profile named fargate-productcatalog that targets all pods destined for the workshop namespace with label app: prodcatalog.
Create a file clusterconfig.yaml with below contents
```
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: eksworkshop-eksctl
  region: ${AWS_REGION}
fargateProfiles:
  - name: fargate-productcatalog
    selectors:
      - namespace: workshop
        labels:
          app: prodcatalog
```
Run the below command to create the Fargate Profile
```bash
envsubst < ./clusterconfig.yaml | eksctl create fargateprofile -f -
```
::alert[The creation of the Fargate Profile will take about 5 - 7 minutes.]{type="info"}
Execute the following command after the profile creation is completed and you should see output similar to what is shown below.
```bash
eksctl get fargateprofile --cluster eksworkshop-eksctl -o yaml
```
```
- name: fargate-productcatalog
  podExecutionRoleARN: arn:aws:iam::$ACCOUNT_ID:role/eksctl-eksworkshop-eksctl-FargatePodExecutionRole-1P7JZFG2UCE8J
  selectors:
  - labels:
      app: prodcatalog
    namespace: workshop
  status: ACTIVE
  subnets:
  - subnet-0ea33XXXXXXXXXXXX
  - subnet-09c54XXXXXXXXXXXX
  - subnet-0e3faXXXXXXXXXXXX
  ```
*## 2. Redeploy Application*
Let's first check if pods are running on node group instances with below command
```bash
kubectl get pods -n workshop -o wide
```
```
NAME                           READY   STATUS    RESTARTS   AGE   IP               NODE                                          NOMINATED NODE   READINESS GATES
frontend-695f9f586f-xxxxx      1/1     Running   0          16d   192.168.XX.XX    ip-192-168-XX-XX.us-west-2.compute.internal   <none>           <none>
prodcatalog-6cc8ff6db9-xxxxx   1/1     Running   0          16d   192.168.XX.XX     ip-192-168-XX-XX.us-west-2.compute.internal   <none>           <none>
proddetail-6dbddf7fd7-xxxxx    1/1     Running   0          40h   192.168.XX.XX   ip-192-168-XX-XX.us-west-2.compute.internal   <none>           <none>
```
Delete the deployment name prodcatalog for Product Catalog Backend Microservice
```bash
kubectl delete deploy prodcatalog -n workshop
```
Let's redeploy the application and this time Product Catalog Backend Microservice should use fargate profile to be spun up in fargate
```bash
cd ~/environment/eks-app-mesh-polyglot-demo
helm upgrade workshop ~/environment/eks-app-mesh-polyglot-demo/workshop/helm-chart/
```
You should see response like below
```
Release "workshop" has been upgraded. Happy Helming!
NAME: workshop
LAST DEPLOYED: Fri Apr  1 17:45:08 2022
NAMESPACE: default
STATUS: deployed
REVISION: 4
NOTES:
1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer to be available.
           You can watch the status of by running 'kubectl get --namespace workshop svc -w frontend'
  export LB_NAME=$(kubectl get svc --namespace workshop frontend -o jsonpath="{.status.loadBalancer.ingress[*].hostname}")
  echo http://$LB_NAME:80
```
Let's verify if the Product Catalog Backend Microservice is deployed on fargate.
```bash
kubectl get pods -n workshop -o wide
```
In few mins, you should see response for prodcatalog pod running on NODE having name fargate like below
```
NAME                           READY   STATUS    RESTARTS   AGE     IP                NODE                                                    NOMINATED NODE   READINESS GATES
frontend-695f9f586f-xxxxx      1/1     Running   0          17d     192.168.XX.XX     ip-192-168-XX-XX.us-west-2.compute.internal             <none>           <none>
prodcatalog-6cc8ff6db9-xxxxx   1/1     Running   0          4m17s   192.168.XX.XX   fargate-ip-192-168-XX-XX.us-west-2.compute.internal   <none>           <none>
proddetail-6dbddf7fd7-xxxxx    1/1     Running   0          45h     192.168.XX.XX    ip-192-168-XX-XX.us-west-2.compute.internal             <none>           <none>
```
*## 3. Test the application*
Get the Loadbalancer url
```bash
export LB_NAME=$(kubectl get svc frontend -n workshop -o jsonpath="{.status.loadBalancer.ingress[*].hostname}") 
echo $LB_NAME
```
Open the Application using the above Loadbalancer url, you should see the below screen. 
![Product Catalog Application](_/static/images/application.png_)
