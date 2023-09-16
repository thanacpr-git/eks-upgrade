# Upgrade control plane

In this lab, we will upgrade the Control Plane of our Amazon EKS in AWS console.

1. Navigate to Amazon EKS console 

   ![assets](/assets/cp-1-eks-console.jpg)

2. Click **Clusters** on the left-pane, view the **cluster name**, and verify the **Kubernetes version**. Then, click "Update now" next to the version number of the Kubernetes version. 

   ![assets](/assets/cp-2-view-cluster-and-version.jpg)
   
3. A new window will pop up, showing the version we will upgrade to. Click "Update" to confirm the update.

   ![assets](/assets/cp-3-update-confirm.jpg)

4. The time to update the Control Plan depends on the size of the cluster. For this lab, it takes approximately 10 minutes. The **Status** will now display "Updating". You may click on the refresh button to fetch the page for the latest status.

   ![assets](/assets/cp-4-during-update.jpg)

5. After the Control Plan upgrade is finish, the **Status** will be "Active" and the **Kubernetes version** will display the upgraded version. The process of the Control Plan upgrade has been completed.

   ![assets](/assets/cp-5-update-complete.jpg)

   
    Run this command `kubectl get po`

   Get this block
   ```
   notde
   noasdfsd
   lsadfsda
   ```

