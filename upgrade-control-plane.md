# Upgrade Control Plane

In this lab, we will upgrade the Control Plane of our Amazon EKS in AWS console.

1. Navigate to Amazon EKS console 

   ![assets](/assets/cp-1-eks-console.jpg)

2. Click **Clusters** on the left-pane, view the **cluster name**, and verify the **Kubernetes version** 

   ![assets](/assets/cp-2-view-cluster-and-version.jpg)
   
3. Click "Update now" next to the version number of the Kubernetes version. A new window will pop up, showing the minor version we will upgrade to. Click "Update" to confirm the update.

   ![assets](/assets/cp-3-update-confirm.jpg)

4. The time to update the Control Plan depends on the size of the cluster. This lab takes approximately 10 minutes. The **Status** will display "Updating". You may click on the refresh button to fetch the page for the latest status.

   ![assets](/assets/cp-4-during-update.jpg)



   
    Run this command `kubectl get po`

   Get this block
   ```
   notde
   noasdfsd
   lsadfsda
   ```

