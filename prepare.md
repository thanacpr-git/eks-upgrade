# [2. Upgrade planning and preparations](/prepare.md)

Before we upgrade our control plane and worker node. We need to upgrade the below component to make sure that we can work with our new cluster version.
  - Install new **`kubectl`** version
  - Install **Kube no trouble** a.k.a **`kubent`**
  - Install **`kubeconvert`** to convert our manifest to use with new cluster version.
  - Recheck everything before we upgrade our cluster.

## 2.1 Install new `kubectl` version

Firstly, we need to determine whether you already have `kubectl` installed on your device. you can simply run the below command to show your `kubectl` client version.
```bash
kubectl version --short --client
```
You will get the current `kubectl` version. 

Output
```bash no-copy
Client Version: v1.20.4-eks-6b7464
```
The best version is **+/- 1 version**. This is not the version what we need. We want the version to match our new cluster version which is `1.25` at least.

Therefore we can install new `kubectl` by downloading it using curl.

```bash
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.25.12/2023-08-16/bin/linux/amd64/kubectl
```
> [!IMPORTANT]
> The command to download kubectl is different depends on your OS version, your CPU architecture. Pleas follow the guid in this link in your real world cluster scenario:
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html 

Now, run this command to grant execution permission to downloaded binary file.

```bash
chmod +x ./kubectl
```

Then use this command to copy the executable file to binary folder so you can run them regardless for directory you are in.
```bash
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH
```
Add the ```$HOME/bin``` path to your shell initialization file so that it is configured when you open a shell.
```shell
echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
```
Now, Verify your newly installed `kubectl` version
```shell
kubectl version --short --client
```
You will get a result like this.

Output
```bash no-copy
WSParticipantRole:~/environment $ kubectl version --short --client

Flag --short has been deprecated, and will be removed in the future. The --short output will become the default.
Client Version: v1.25.12-eks-8ccc7ba
Kustomize Version: v4.5.7
```
We can try running ...
```bash
kubectl get node
```
To check if our new `kubectl` is working as the result below.


Output
```bash no-copy
WSParticipantRole:~/environment $ kubectl get node
NAME                              STATUS   ROLES    AGE   VERSION
ip-192-168-118-21.ec2.internal    Ready    <none>   30h   v1.24.16-eks-8ccc7ba
ip-192-168-146-116.ec2.internal   Ready    <none>   30h   v1.24.16-eks-8ccc7ba
ip-192-168-178-210.ec2.internal   Ready    <none>   30h   v1.24.16-eks-8ccc7ba
```

Now we are happy with our new `kubectl` version. Let's move on.

## 2.2 Install Kube no trouble a.k.a `kubent` 

Kube no trouble is a tool which easily check your clusters for use of deprecated APIs. To install `kubent` run the command below.

```shell
sh -c "$(curl -sSL https://git.io/install-kubent)"
```
You will get the result below. This means `kubent` is installed completely


Output
```shell no-copy
WSParticipantRole:~/environment $ sh -c "$(curl -sSL https://git.io/install-kubent)"
>>> kubent installation script <<<
> Detecting latest version
> Downloading version 0.7.0
Target directory (/usr/local/bin) is not writable, trying to use sudo
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 11.9M  100 11.9M    0     0  21.5M      0 --:--:-- --:--:-- --:--:-- 30.5M
> Done. kubent was installed to /usr/local/bin/.
```
Now, try run...

```bash
kubent
```

You should get the below result. It states that `CronJob` will be deprecated in `1.25` and `HorizontalPodAutoScaler` will be deprecated in `1.26`. We get the quick list of API object that we should update.


Output
```bash no-copy
WSParticipantRole:~/environment/ (master) $ kubent
2:45PM INF >>> Kube No Trouble `kubent` <<<
2:45PM INF version 0.7.0 (git sha d1bb4e5fd6550b533b2013671aa8419d923ee042)
2:45PM INF Initializing collectors and retrieving data
2:45PM INF Target K8s version is 1.24.16-eks-2d98532
2:45PM INF Retrieved 90 resources from collector name=Cluster
2:45PM INF Retrieved 17 resources from collector name="Helm v3"
2:45PM INF Loaded ruleset name=custom.rego.tmpl
2:45PM INF Loaded ruleset name=deprecated-1-16.rego
2:45PM INF Loaded ruleset name=deprecated-1-22.rego
2:45PM INF Loaded ruleset name=deprecated-1-25.rego
2:45PM INF Loaded ruleset name=deprecated-1-26.rego
2:45PM INF Loaded ruleset name=deprecated-future.rego
__________________________________________________________________________________________
>>> Deprecated APIs removed in 1.25 <<<
------------------------------------------------------------------------------------------
KIND                NAMESPACE     NAME             API_VERSION      REPLACE_WITH (SINCE)
CronJob             default       hello            batch/v1beta1    batch/v1 (1.21.0)
PodSecurityPolicy   <undefined>   eks.privileged   policy/v1beta1   <removed> (1.21.0)
__________________________________________________________________________________________
>>> Deprecated APIs removed in 1.26 <<<
------------------------------------------------------------------------------------------
KIND                      NAMESPACE   NAME         API_VERSION           REPLACE_WITH (SINCE)
HorizontalPodAutoscaler   workshop    proddetail   autoscaling/v2beta1   autoscaling/v2 (1.23.0)
```
## 2.3 Install `kubectl-convert` to convert our manifest to use with new cluster version.

Kubectl Convert config files between different API versions. Both YAML and JSON formats are accepted. The default output will be printed to stdout in YAML format. One can use -o option to change to output destination.

First we need to install `kubectl-convert`, using the command below
```bash
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl-convert
```

You will see the result like this


Output
```bash no-copy
WSParticipantRole:~/environment $ curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl-convert
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                Dload  Upload   Total   Spent    Left  Speed
100   138  100   138    0     0   3267      0 --:--:-- --:--:-- --:--:--  3209
100 46.5M  100 46.5M    0     0  15.8M      0  0:00:02  0:00:02 --:--:-- 16.9M
```
Now we can grant the permission to execute the binary file so that we can run the file.

```bash
chmod +x kubectl-convert
```

Try list the file to see if you can see `rwx` permission to run the file
```bash
ll kubectl-convert
```
You will see the result like this.


Output
```bash no-copy
WSParticipantRole:~/environment $ ll kubectl-convert 
-rwxrwxr-x 1 ec2-user ec2-user 48812032 Sep 18 05:23 kubectl-convert
```

Now copy the `kubectl-convert` file to binary location, so we can execute `kubectl-convert` at any directory.

```bash
sudo install -o root -g root -m 0755 kubectl-convert /usr/local/bin/kubectl-convert
```
After we copy the file, try running
```bash
kubectl convert
```
to test if `kubectl-convert` is installed completely. You should get the below result.


Output
```bash no-copy
WSParticipantRole:~/environment $ kubectl convert
error: must specify one of -f and -k
```

Now , we can convert our CronJob and HPA's yaml file using `kubectl-convert`

### Updating our HELLO `CronJob`'s YAML definition
First we will update our CronJob **Hello** yaml file. Simply run.
```bash
kubectl convert -f my-cj.yaml
```
`kubectl-convert` will return the output in new YAML format like below.

Output
```yaml no-copy
apiVersion: batch/v1
kind: CronJob
metadata:
  creationTimestamp: null
  name: hello
spec:
  concurrencyPolicy: Allow
  failedJobsHistoryLimit: 1
  jobTemplate:
    metadata:
      creationTimestamp: null
      name: hello
    spec:
      template:
        metadata:
          creationTimestamp: null
        spec:
          containers:
          - image: busybox:1.28
            imagePullPolicy: IfNotPresent
            name: hello
            resources: {}
            terminationMessagePath: /dev/termination-log
            terminationMessagePolicy: File
          dnsPolicy: ClusterFirst
          restartPolicy: OnFailure
          schedulerName: default-scheduler
          securityContext: {}
          terminationGracePeriodSeconds: 30
  schedule: '* * * * *'
  successfulJobsHistoryLimit: 3
  suspend: false
status: {}
```

We can redirect these output to our new file, like this.
```bash
kubectl convert -f my-cj.yaml -o yaml >> my-cj-new.yaml
```
You can see the difference between **my-cj.yaml** and **my-cj-new.yaml** using **`diff`** command.
```bash
diff -U -1 my-cj.yaml my-cj-new.yaml
```
You can see the difference between 2 files. The new line are marked as Green and the removed line are marked as Red. You can see that the apiVersion has been changed and some context has been added.


Output
```diff no-copy
WSParticipantRole:~/environment $ diff -U -1 my-cj.yaml my-cj-new.yaml                                                                    
--- my-cj.yaml  2023-09-18 05:26:14.427246661 +0000
+++ my-cj-new.yaml      2023-09-18 05:30:51.489309476 +0000
@@ -1,22 +1,33 @@
-apiVersion: batch/v1beta1
+apiVersion: batch/v1
kind: CronJob
metadata:
  creationTimestamp: null
  name: hello
spec:
+  concurrencyPolicy: Allow
+  failedJobsHistoryLimit: 1
  jobTemplate:
    metadata:
      creationTimestamp: null
      name: hello
    spec:
      template:
        metadata:
          creationTimestamp: null
        spec:
          containers:
          - image: busybox:1.28
+            imagePullPolicy: IfNotPresent
            name: hello
            resources: {}
+            terminationMessagePath: /dev/termination-log
+            terminationMessagePolicy: File
+          dnsPolicy: ClusterFirst
          restartPolicy: OnFailure
+          schedulerName: default-scheduler
+          securityContext: {}
+          terminationGracePeriodSeconds: 30
  schedule: '* * * * *'
+  successfulJobsHistoryLimit: 3
+  suspend: false
status: {}
```

Now, try apply out new CronJob file.
```bash
kubectl apply -f my-cj-new.yaml
```

You will see that our CronJob **Hello** has been configure.


Output
```bash
WSParticipantRole:~/environment $ k apply -f my-cj-new.yaml
cronjob.batch/hello configured
```

### Updating our PrdDetail `HPA`'s YAML definition

Next, we will use `kubectl-convert` to convert our **HPA** to new API version by running this.

```bash
kubectl convert -f ./hpa_proddetail.yaml >> hpa_proddetail_new.yaml
```
You can see the difference between **hpa_proddetail.yaml** and **hpa_proddetail_new.yaml** using `diff` command.
```bash
diff -U -1 hpa_proddetail.yaml hpa_proddetail_new.yaml
```
You can see the difference between 2 files. The new line are marked as Green and the removed line are marked as Red. You can see that the major change has more YAML definition structure change compared to CronJob definition which is minor change.



Output
```diff no-copy
WSParticipantRole:~/environment $ diff -U -1 hpa_proddetail.yaml hpa_proddetail_new.yaml
--- hpa_proddetail.yaml 2023-09-18 14:44:50.624566138 +0000
+++ hpa_proddetail_new.yaml     2023-09-18 14:55:34.769509150 +0000
@@ -1,18 +1,23 @@
-
-apiVersion: autoscaling/v2beta1
+apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
+  creationTimestamp: null
  name: proddetail
  namespace: workshop
spec:
  maxReplicas: 3
  metrics:
  - resource:
      name: cpu
-      targetAverageUtilization: 40
+      target:
+        averageUtilization: 40
+        type: Utilization
    type: Resource
  minReplicas: 1
  scaleTargetRef:
-      apiVersion: apps/v1
-      kind: Deployment
-      name: proddetail
+    apiVersion: apps/v1
+    kind: Deployment
+    name: proddetail
+status:
+  currentMetrics: null
+  desiredReplicas: 0
```

### Revalidate using `kubent` to make sure that we have no trouble any more
This step we are going to use `kubent` again to make sure that our `CronJob` and `HPA`'s definition has been update and has no trouble

Now, we try running.


```bash
kubent
```
We will see `kubent`'s result with no `CronJob` and `HPA` shown anymore.


Output
```bash no-copy
WSParticipantRole:~/environment $ kubent
8:41AM INF >>> Kube No Trouble `kubent` <<<
8:41AM INF version 0.7.0 (git sha d1bb4e5fd6550b533b2013671aa8419d923ee042)
8:41AM INF Initializing collectors and retrieving data
8:41AM INF Target K8s version is 1.24.16-eks-2d98532
8:41AM INF Retrieved 90 resources from collector name=Cluster
8:41AM INF Retrieved 17 resources from collector name="Helm v3"
8:41AM INF Loaded ruleset name=custom.rego.tmpl
8:41AM INF Loaded ruleset name=deprecated-1-16.rego
8:41AM INF Loaded ruleset name=deprecated-1-22.rego
8:41AM INF Loaded ruleset name=deprecated-1-25.rego
8:41AM INF Loaded ruleset name=deprecated-1-26.rego
8:41AM INF Loaded ruleset name=deprecated-future.rego
__________________________________________________________________________________________
>>> Deprecated APIs removed in 1.25 <<<
------------------------------------------------------------------------------------------
KIND                NAMESPACE     NAME             API_VERSION      REPLACE_WITH (SINCE)
PodSecurityPolicy   <undefined>   eks.privileged   policy/v1beta1   <removed> (1.21.0)
```

## 2.4 Prepare for next steps.
Before we upgrade our ControlPlane and Managed Node Group. We have to make sure once again that our `kubectl` is in the correct version (`1.25`) and our EKS cluster version is still using version `1.24`

Now run.
```
kubectl get node
```
You can see that our nodes' version is still at `1.24`


Output
```bash no-copy
WSParticipantRole:~/environment $ kubectl get node
NAME                              STATUS   ROLES    AGE   VERSION
ip-192-168-118-21.ec2.internal    Ready    <none>   30h   v1.24.16-eks-8ccc7ba
ip-192-168-146-116.ec2.internal   Ready    <none>   30h   v1.24.16-eks-8ccc7ba
ip-192-168-178-210.ec2.internal   Ready    <none>   30h   v1.24.16-eks-8ccc7ba
```

Verify our `kubectl` and control plane's version by running this command.
```
kubectl version --short
```

We will see the result as below.


Output
```bash no-copy
WSParticipantRole:~/environment $ kubectl version --short
Flag --short has been deprecated, and will be removed in the future. The --short output will become the default.
Client Version: v1.25.12-eks-8ccc7ba
Kustomize Version: v4.5.7
Server Version: v1.24.16-eks-2d98532
```

## Congratulations!!!!

You are ready for our next step to upgrade EKS control Plan. 
Click [HERE](upgrade-control-plane.md) to go to our next step.

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
