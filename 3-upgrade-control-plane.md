# Upgrade Control Plane

In this lab, we will upgrade the Control Plane of our Amazon EKS in AWS console.

Please note that if we run `kubent` now, it will show a PodSecurityPolicy called "eks.privileged". For example,
```
__________________________________________________________________________________________
>>> Deprecated APIs removed in 1.25 <<<
------------------------------------------------------------------------------------------
KIND                NAMESPACE     NAME             API_VERSION      REPLACE_WITH (SINCE)
PodSecurityPolicy   <undefined>   eks.privileged   policy/v1beta1   <removed> (1.21.0)
```

As stated in [Pod security policy (PSP) removal FAQ](https://docs.aws.amazon.com/eks/latest/userguide/pod-security-policy-removal-faq.html), Amazon EKS automatically migrates this PSP to a PSS-based enforcement. *No action is needed on your part*. 

![assets](/assets/cp-0-psp-removal-700w.jpg)

1. Start the Control Plan upgrade by navigating to Amazon EKS console 

   ![assets](/assets/cp-1-eks-console.jpg)

2. Click **Clusters** on the left-pane, view the **Cluster name**, and verify the **Kubernetes version**. Then, click "Update now" next to the version number of the Kubernetes version. 

   ![assets](/assets/cp-2-view-cluster-and-version.jpg)
   
3. A new window will pop up, showing the version that it will upgrade to. Click "Update" to confirm.

   ![assets](/assets/cp-3-update-confirm.jpg)

4. The **Status** will display "Updating". The time to update the Control Plan depends on the size of the cluster. For this lab, it takes approximately 10 minutes. You may click the refresh button to refresh the page for the latest status.

   ![assets](/assets/cp-4-during-update.jpg)

5. After the upgrade is finish, the **Status** will change to "Active". The **Kubernetes version** will display the upgraded version. The process of the Control Plan upgrade has been completed.

   ![assets](/assets/cp-5-update-complete.jpg)

6. Please note that if we run `kubectl get psp eks.privileged` now, 
   ```sh
   kubectl get psp eks.privileged
   ```
   
   the psp no longer exists in our cluster. For example,
   ```
   error: the server doesn't have a resource type "psp"
   ```
   
## Congratulations!!!!

You are ready for our next step to upgrade EKS managed node group. 
Click [HERE](4-upgrade-data-plane.md) to go to our next step.
