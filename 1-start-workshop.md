# Start the Workshop

Before proceeding with Planning and Upgrade , please follow workshop set up instruction from <strong>Option 1 (Preferred): Running the workshop using Workshop Studio for AWS events</strong> in https://catalog.workshops.aws/eks-immersionday/en-US/introduction. Next set up a workload by following below steps:
 - Check Running EKS version 
 - Deploy the microservices application using Helm
 - Deploy deprecated k8s resources: CronJob and HPA on EKS Cluster for upgrading purpose


## 1. Check Running EKS version  

Verify EKS cluster in version 1.24 from console https://us-east-1.console.aws.amazon.com/eks/home?region=us-east-1#/clusters
  
![Alt text](/assets/00_start_eks_version.png "a title")

## 2. Deploy the microservices application using Helm  

  From Cloud 9 Console , Install helm using step below :


  ```
  cd ~/environment/eks-app-mesh-polyglot-demo
  helm install workshop ~/environment/eks-app-mesh-polyglot-demo/workshop/helm-chart/
  ```
  **Output**
  ```
  NAME: workshop
  LAST DEPLOYED: Mon Sep 11 00:56:04 2023
  NAMESPACE: default
  STATUS: deployed
  REVISION: 1
  NOTES:
  ```

  Get the application URL by running these commands:

  ```
  export LB_NAME=$(kubectl get svc --namespace workshop frontend -o jsonpath="{.status.loadBalancer.ingress[*].hostname}")
    
  echo http://$LB_NAME:80
  ```


  **NOTE:** The command may take a few minutes for the LoadBalancer to be available. You can monitor the status of LoadBalancer by using command
    
  ```
  kubectl get --namespace workshop svc -w frontend
  ```

## 3. Deploy deprecated k8s resources: CronJob and HPA on EKS Cluster for upgrading purpose  

  - First of all, we are going to deploy our `Cronjob`

    ```bash 
    cd ~/environment 
    cat << EoF > ~/environment/my-cj.yaml
    apiVersion: batch/v1beta1
    kind: CronJob
    metadata:
      name: hello
    spec:
      schedule: "* * * * *"
      jobTemplate:
        spec:
          template:
            spec:
              containers:
              - name: hello
                image: busybox:1.28
                imagePullPolicy: IfNotPresent
                command:
                - /bin/sh
                - -c
                - date; echo Hello from the Kubernetes cluster
              restartPolicy: OnFailure
    EoF
    kubectl apply -f ~/environment/my-cj.yaml
    ```
    Dismiss the output below

    **Output**
    ```
    Warning: batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob
    cronjob.batch/hello created
    ```

    Check cronjob schedule and log in kubernetes pod to verify that job run successfully  

    ```bash
    kubectl get cj
    ```
    **Output**
    ```
    NAME    SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
    hello   * * * * *   False     0        22s             84s
    ```
    To check if a job ran successfully, list the pod(s)
    ```
    kubectl get po
    ```    
    **Output**
    ```bash
    NAME                   READY   STATUS      RESTARTS   AGE
    hello-28239920-w7w85   0/1     Completed   0          43s
    ```
    Fetch the log from the *above pod*
    ```
    kubectl logs hello-28239920-w7w85
    ```
    **Output**
    ```bash
    Mon Sep 11 01:20:00 UTC 2023
    Hello from the Kubernetes cluster
    ```
    
    Now, we deploy Metric Server before applying HPA

    ```
    kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
    ```
    
    Verify Status the status of the metrics-server APIService (This can take few minutes).

    ```
    kubectl get apiservice v1beta1.metrics.k8s.io -o json | jq '.status'
    ```
    **Output**
    ```json
    {
    "conditions": [
        {
        "lastTransitionTime": "2020-11-10T06:39:13Z",
        "message": "all checks passed",
        "reason": "Passed",
        "status": "True",
        "type": "Available"
        }
    ]
    }
    ```

    Next step, we shall verify that Metric server run successfully, CPU and Memory will be displayed from below command:  

    ```
    kubectl top node
    ```
    **Output**
    ```bash
    NAME                                                     CPU(cores)   CPU%        MEMORY(bytes)   MEMORY%     
    ip-10-0-23-233.ap-southeast-1.compute.internal           645m         33%         964Mi           13%         
    ip-10-0-43-103.ap-southeast-1.compute.internal           610m         31%         1039Mi          14%   
    ```

    Now, we apply Horizontal Pod Autoscaler for proddetail deployment:  

    ```bash
    cd ~/environment 
    cat << EoF > ${HOME}/environment/hpa_proddetail.yaml

    apiVersion: autoscaling/v2beta1
    kind: HorizontalPodAutoscaler
    metadata:
      name: proddetail
      namespace: workshop
    spec:
      maxReplicas: 3
      metrics:
      - resource:
          name: cpu
          targetAverageUtilization: 40
        type: Resource
      minReplicas: 1
      scaleTargetRef:
          apiVersion: apps/v1
          kind: Deployment
          name: proddetail
    EoF
    kubectl apply -f ~/environment/hpa_proddetail.yaml
    ```    
    You will see a warning about deprecated `autoscaling/v2beta1 HorizontalPodAutoscaler` mentioned as below.


    Output
    ```bash
    Warning: autoscaling/v2beta1 HorizontalPodAutoscaler is deprecated in v1.22+, unavailable in v1.25+; use autoscaling/v2 HorizontalPodAutoscaler
    horizontalpodautoscaler.autoscaling/proddetail created
    ```
    Verify if HPA is set up properly
    ```
    kubectl get hpa proddetail -n workshop
    ```
    Output
    ```
    NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
    proddetail   Deployment/proddetail   1%/40%    1         3         1          18m
    ```

## Congratulations!!!!

You are ready for our next step to prepare your upgrade. 
Click [HERE](2-prepare.md) to go to our next step.

