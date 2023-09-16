# Upgrade Data Plane

In this lab, we will upgrade the Data Plane of our Amazon EKS in AWS console.

To achive zero downtime for our application, we can utilize **Pod Disruption Budgets (PDBs)**. 
[PDBs](https://kubernetes.io/docs/tasks/run-application/configure-pdb/) are used to define,
at any given time, how many replicas of a pod must be available (`minAvailable`) or can be unavailable (`maxUnavailable`).

1. View the number of replicas of our application in the cluster
   ```sh
   kubectl get deploy -n workshop
   ```

   Currently, all Deployments have only 1 replica.
   ```
   NAME          READY   UP-TO-DATE   AVAILABLE   AGE
   frontend      1/1     1            1           9h
   prodcatalog   1/1     1            1           9h
   proddetail    1/1     1            1           9h
   ```

2. Assume that each Deployment must have 1 pod available during the Data Plane upgrade.
Scale out the Deployment to have more than 1 replica, for example 3 replicas
   ```sh
   kubectl scale --replicas=3 deployment frontend -n workshop
   kubectl scale --replicas=3 deployment prodcatalog -n workshop
   kubectl scale --replicas=3 deployment proddetail -n workshop
   ```

   All Deployments now have 3 replicas.
   ```sh
   kubectl get deploy -n workshop
   ```

   For example,
   ```
   NAME          READY   UP-TO-DATE   AVAILABLE   AGE
   frontend      3/3     3            3           10h
   prodcatalog   3/3     3            3           10h
   proddetail    3/3     3            3           10h
   ```
   
3. Create PDBs for each pod type to have `minAvailable` of 1
   ```sh
   cat <<EoF | kubectl apply -f -
   apiVersion: policy/v1
   kind: PodDisruptionBudget
   metadata:
     name: frontend-pdb
     namespace: workshop
   spec:
     minAvailable: 1
     selector:
       matchLabels:
         app: frontend
   ---
   apiVersion: policy/v1
   kind: PodDisruptionBudget
   metadata:
     name: prodcatalog-pdb
     namespace: workshop
   spec:
     minAvailable: 1
     selector:
       matchLabels:
         app: prodcatalog
   ---
   apiVersion: policy/v1
   kind: PodDisruptionBudget
   metadata:
     name: proddetail-pdb
     namespace: workshop
   spec:
     minAvailable: 1
     selector:
       matchLabels:
         app: proddetail
   EoF
   ```

4. Check the status of the PDB
   ```sh
   kubectl get pdb -n workshop
   ```

   Because of 3 replicas and `minAvailable` of 1, the PDBs have "ALLOWED DISRUPTIONS" of 2
   ```
   NAME              MIN AVAILABLE   MAX UNAVAILABLE   ALLOWED DISRUPTIONS   AGE
   frontend-pdb      1               N/A               2                     11s
   prodcatalog-pdb   1               N/A               2                     11s
   proddetail-pdb    1               N/A               2                     11s
   ```

5. Set up the monitoring of the application in the Cloud9 terminals to ensure the zero-downtime

   Open a new terminal and run the command to monitor the pods
   ```sh
   kubectl get pod -n workshop -w
   ```

   Open another terminal and run the command below to cURL the application
   ```sh
   while true; do export LB_NAME=$(kubectl get svc frontend -n workshop -o jsonpath="{.status.loadBalancer.ingress[*].hostname}");  curl -s -o /dev/null ${LB_NAME};
     if [ $? -eq 0 ]; then echo "OK -- $(date)"; else echo "NOT-OK -- $(date)"; fi
   done
   ```

6. Navigate to Amazon EKS console to upgrade the Data Plane 

   ![assets](/assets/cp-1-eks-console.jpg)

7. Click **Clusters** on the left-pane and click the **Cluster name** "eksworkshop-eksctl"

   ![assets](/assets/dp-2-view-cluster.jpg)

8. Click the **Compute** tab and scroll down to the **Node groups** section
to view the version number in the **AMI release version** column

   ![assets](/assets/dp-3-compute-tab.jpg)

9. Click "Update now" next to the version number. A new window will pop up, showing the "Rolling update" strategy. Click "Update" to confirm

   ![assets](/assets/dp-4-update-confirm.jpg)

10. The **Status** will display "Updating". The time to update the Data Plan depends on the size of the cluster. For this lab, it takes approximately 17 minutes. You may click the refresh button to refresh the page for the latest status. 

    ![assets](/assets/dp-5-during-update.jpg)
 
    You can monitor the pod upgrade activities in the Cloud9 terminals, for example,
    While the pods are terminated and re-created, for example,
    ```
    ...
    frontend-765f57cd79-xvd7t      1/1     Running             0          46s
    proddetail-dc968597-rd7dk      1/1     Running             0          55s
    proddetail-dc968597-f766g      1/1     Terminating         0          10h
    proddetail-dc968597-7zx4l      0/1     Pending             0          0s
    proddetail-dc968597-7zx4l      0/1     Pending             0          0s
    proddetail-dc968597-7zx4l      0/1     ContainerCreating   0          0s
    prodcatalog-6f66c677f7-b6hnm   1/1     Terminating         0          10h
    prodcatalog-6f66c677f7-l2lmt   0/1     Pending             0          0s
    prodcatalog-6f66c677f7-l2lmt   0/1     Pending             0          0s
    prodcatalog-6f66c677f7-l2lmt   0/1     ContainerCreating   0          0s
    frontend-765f57cd79-xnr5h      1/1     Terminating         0          10h
    frontend-765f57cd79-h75cw      0/1     Pending             0          0s
    frontend-765f57cd79-h75cw      0/1     Pending             0          0s
    frontend-765f57cd79-h75cw      0/1     ContainerCreating   0          0s
    proddetail-dc968597-f766g      0/1     Terminating         0          10h
    ...
    ```

    Our application continues running without interruption, for example,
    ```
    ...
    OK -- Sat Sep 16 22:51:42 UTC 2023
    OK -- Sat Sep 16 22:51:44 UTC 2023
    OK -- Sat Sep 16 22:51:45 UTC 2023
    OK -- Sat Sep 16 22:51:46 UTC 2023
    OK -- Sat Sep 16 22:51:47 UTC 2023
    OK -- Sat Sep 16 22:51:48 UTC 2023
    OK -- Sat Sep 16 22:51:49 UTC 2023
    OK -- Sat Sep 16 22:51:50 UTC 2023
    ...
    ```

11. After the upgrade is finish, the **Status** will change to "Active". The **AMI release version** will display the upgraded version. The process of the Data Plan upgrade has been completed.

    ![assets](/assets/dp-6-update-complete.jpg)

12. Scale in the deployments to 1 pod

    ```sh
    kubectl scale --replicas=1 deployment frontend -n workshop
    kubectl scale --replicas=1 deployment prodcatalog -n workshop
    kubectl scale --replicas=1 deployment proddetail -n workshop
    ```
