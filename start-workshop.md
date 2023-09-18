Before proceeding with Planning and Upgrade , please follow workshop set up instruction from <strong>Option 1 (Preferred): Running the workshop using Workshop Studio for AWS events</strong> in https://catalog.workshops.aws/eks-immersionday/en-US/introduction. Next follow below steps :


### [1] Check Running EKS version 
- Verify EKS cluster in version 1.24 from console https://us-east-1.console.aws.amazon.com/eks/home?region=us-east-1#/clusters
  
    ![Alt text](/assets/00_start_eks_version.png "a title")

- (Optional) Add the “k” alias for the command kubectl. This will allow user to type only "k" instead of kubectl everytime.

    ```
    echo "alias k=kubectl" | tee -a ~/.bash_profile
    .  ~/.bash_profile
    ```
    
    **Check alias setting** 

    k get node

    **Output**
    ```
    NAME                              STATUS   ROLES    AGE   VERSION
    ip-192-168-116-204.ec2.internal   Ready    <none>   19m   v1.24.16-eks-8ccc7ba
    ip-192-168-145-136.ec2.internal   Ready    <none>   19m   v1.24.16-eks-8ccc7ba
    ip-192-168-163-67.ec2.internal    Ready    <none>   19m   v1.24.16-eks-8ccc7ba
    ```

### [2] Deploy the microservices application using Helm

- From Cloud 9 Console , Install helm using step below :

    ```
    cd ~/environment/eks-app-mesh-polyglot-demo
    helm install workshop ~/environment/eks-app-mesh-polyglot-demo/workshop/helm-chart/

    NAME: workshop
    LAST DEPLOYED: Mon Sep 11 00:56:04 2023
    NAMESPACE: default
    STATUS: deployed
    REVISION: 1
    NOTES:
    ```


- Get the application URL by running these commands:

    ```
    export LB_NAME=$(kubectl get svc --namespace workshop frontend -o jsonpath="{.status.loadBalancer.ingress[*].hostname}")
    
    echo http://$LB_NAME:80
    ```


    **NOTE:** The command may take a few minutes for the LoadBalancer to be available. You can monitor the status of LoadBalancer by using command
    
    ```
    kubectl get --namespace workshop svc -w frontend
    ```
### [3] Deploy test application on EKS Cluster

- Deploy Cronjob

    ```
    cd ~/environment 
    cat << EoF > ${HOME}/environment/my-cj.yaml


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
            name: hello
                image: busybox:1.28
                imagePullPolicy: IfNotPresent
                command:
                  /bin/sh
                  -c
                  date; echo Hello from the Kubernetes cluster
            restartPolicy: OnFailure
    EoF
    k apply -f ~/environment/my-cj.yaml


    Warning: batch/v1beta1 CronJob is deprecated in v1.21+, unavailable in v1.25+; use batch/v1 CronJob
    cronjob.batch/hello created
    ```

- Check cronjob schedule and log kubernetes pod to verify that Cront running normally

    ```
    k get cj

    NAME    SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
    hello   * * * * *   False     0        22s             84s


    k get po

    NAME                   READY   STATUS      RESTARTS   AGE
    hello-28239919-72hvv   0/1     Completed   0          103s
    hello-28239920-w7w85   0/1     Completed   0          43s


    k logs hello-28239920-w7w85

    Mon Sep 11 01:20:00 UTC 2023
    Hello from the Kubernetes cluster
    ```

    
<!--By participating in this workshop you will be provided with an AWS account to use to complete the lab material. Connect to the portal by browsing to https://catalog.workshops.aws/. Click on <strong>Get Started.</strong>

![Https catalog](https://www.eksworkshop.com/assets/images/workshop-studio-home-ee08e612fd0a646451211731ad813b7f.png)

### Workshop Studio Home

You will be prompted to sign in. Select the option Email One-Time Password(OTP).

![Https OTP](https://www.eksworkshop.com/assets/images/ws-studio-login-51632e8052f5f148284b88a20770dfbd.png)

### Workshop Studio Sign in

Enter your email address and press Send passcode, which will send a one-time passcode to your inbox. When the email arrives you can enter the passcode and log-in.

Your instructor should have provided you with an Event access code prior the starting these exercises. Enter the provided hash in the text box and hit Next.

![Https Signon](https://www.eksworkshop.com/assets/images/event-code-e952a875ef4ac6300550c28fe7ef7ccc.png)


Event Code

Read and accept the Terms and Conditions and click Join event to continue.

https://www.eksworkshop.com/assets/images/review-and-join-e68eb60861dc6b67dc4ec75deb5307bb.png


Review and Join

You will be presented with your personal dashboard. Select the Open AWS Console button to be taken to your AWS account console:

![Https console](https://www.eksworkshop.com/assets/images/openconsole-3df798bbfb5475407f71c552d09c94c4.png)


Open Console

Next return to the personal dashboard page and scroll down to the Event Outputs section to get a quickstart link to your Cloud9 IDE. Open this in a new browser tab:

![Https browser](https://www.eksworkshop.com/assets/images/cloud9-2c554c978c7b41b25864558666aeef89.png)

### Cloud9 Link

Press Get started to access the workshop splash page:

![Https page](https://www.eksworkshop.com/assets/images/workshop-event-page-7391a20bc4599267ffb82643b0b3f3fc.png)

You can now proceed to the <strong>Navigating</strong> the labs section.

-->

