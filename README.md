# kubernetes-demo
RK Kubernetes Practical Demos for Learning 

Best Practices:


kubectl run: 


Using the kubectl run command can help in generating a YAML template. And sometimes, you can even get away with just the kubectl run command without having to create a YAML file at all. For example, if you were asked to create a pod or deployment with a specific name and image, you can simply run the kubectl run command.

Use the below set of commands and try the previous practice tests again, but this time, try to use the below commands instead of YAML files. Try to use these as much as you can going forward in all exercises.

Reference (Bookmark this page for the exam. It will be very handy):

https://kubernetes.io/docs/reference/kubectl/conventions/

Create an NGINX Pod

kubectl run nginx --image=nginx


Generate POD Manifest YAML file (-o yaml). Don’t create it(–dry-run)

kubectl run nginx --image=nginx --dry-run=client -o yaml


Create a deployment

kubectl create deployment --image=nginx nginx


Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run)


kubectl create deployment --image=nginx nginx --dry-run=client -o yaml


Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run) and save it to a file.


kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml


Make necessary changes to the file (for example, adding more replicas) and then create the deployment.


kubectl create -f nginx-deployment.yaml

OR

In k8s version 1.19+, we can specify the –replicas option to create a deployment with 4 replicas.


kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml

Key Nodes:


Case1:   Node Affinity     


To find which nodes can the pods for the blue deployment be placed on?


Check if controlplane and node01 have any taints on them that will prevent the pods to be scheduled on them. If there are no taints, the pods can be scheduled on either node.

So run the following command to check the taints on both nodes.

kubectl describe node controlplane | grep -i taints

kubectl describe node node01 | grep -i taints


Case02:

Edit Deployments:


With Deployments, you can easily edit any field/property of the POD template. Since the pod template is a child of the deployment specification, with every change the deployment will automatically delete and create a new pod with the new changes. So if you are asked to edit a property of a POD part of a deployment you may do that simply by running the command


kubectl edit deployment my-deployment.


Case:3 

Kubernetes Components:


An overview of the key components that make up a Kubernetes cluster.

This page provides a high-level overview of the essential components that make up a Kubernetes cluster.


Components of Kubernetes:

The components of a Kubernetes cluster


Core Components:

A Kubernetes cluster consists of a control plane and one or more worker nodes. Here's a brief overview of the main components:

Control Plane Components:

Manage the overall state of the cluster:

kube-apiserver:

The core component server that exposes the Kubernetes HTTP API.

etcd:

Consistent and highly-available key value store for all API server data.


kube-scheduler:


Looks for Pods not yet bound to a node, and assigns each Pod to a suitable node.
kube-controller-manager:


Runs controllers to implement Kubernetes API behavior.
cloud-controller-manager (optional)


Integrates with underlying cloud provider(s).


Node Components:


Run on every node, maintaining running pods and providing the Kubernetes runtime environment:

kubelet:

Ensures that Pods are running, including their containers.


kube-proxy (optional)


Maintains network rules on nodes to implement Services.


Container runtime:


Software responsible for running containers. Read Container Runtimes to learn more.



Case:4  A note on Secrets:


Remember that secrets encode data in base64 format. Anyone with the base64 encoded secret can easily decode it. As such the secrets can be considered not very safe.


The concept of safety of the Secrets is a bit confusing in Kubernetes. The Kubernetes documentation page and a lot of blogs out there refer to secrets as a “safer option” to store sensitive data. They are safer than storing in plain text as they reduce the risk of accidentally exposing passwords and other sensitive data. In my opinion, it’s not the secret itself that is safe, it is the practices around it.


Secrets are not encrypted, so it is not safer in that sense. However, some best practices around using secrets make it safer. As in best practices like:


Not checking in secret object definition files to source code repositories.
Enabling Encryption at Rest for Secrets so they are stored encrypted in ETCD.
Also, the way Kubernetes handles secrets. Such as:


A secret is only sent to a node if a pod on that node requires it.


Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.


Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.


Read about the protections and risks of using secrets here.


Having said that, there are other better ways of handling sensitive data like passwords in Kubernetes, such as using tools like Helm Secrets, and HashiCorp Vault.


**Case5:  Self-Healing Applications**


Kubernetes supports self-healing applications through ReplicaSets and Replication Controllers. The replication controller helps ensure that a POD is re-created automatically when the application within the POD crashes. It helps in ensuring enough replicas of the application are running at all times.


Kubernetes provides additional support to check the health of applications running within PODs and take necessary actions through Liveness and Readiness Probes.

**Case6: Autoscaling:**

Kubernetes is designed to dynamically scale containerized applications. Two primary scaling strategies in Kubernetes are:

Scaling workloads – adding or removing containers (Pods) in the cluster.

Scaling the underlying cluster infrastructure – adding or removing nodes (servers) in the cluster.

To clarify:


**For the cluster infrastructure:
**

Horizontal scaling: Add more nodes to the cluster.

Vertical scaling: Increase resources (CPU, memory) on existing nodes.

**For workloads:**

**Horizontal scaling:** Create more Pods.

**Vertical scaling:** Increase resource limits and requests for existing Pods.


There are two approaches to scaling in Kubernetes: manual and automated.

**Manual vs. Automated Scaling**

Manual scaling and automated scaling both have their place. Manual scaling involves direct intervention and command execution, while automated scaling leverages Kubernetes controllers for dynamic adjustments.

Automated Scaling
Automated scaling in Kubernetes simplifies operations:


**Cluster Infrastructure:**
Managed by the Kubernetes Cluster Autoscaler.


**Workload Horizontal Scaling:**
Managed by the Horizontal Pod Autoscaler (HPA).


**Workload Vertical Scaling:**
Managed by the Vertical Pod Autoscaler (VPA).

**Case6: Cluster Upgrade Process Overview**


Consider a production cluster with master and worker nodes running version 1.10. The upgrade process generally involves two major steps:

**Upgrading the worker nodes.
**
During the master node upgrade, control plane components (such as the API server, scheduler, and controller managers) experience a brief interruption. Although management functionality (like kubectl commands or scaling deployments) is paused, the worker nodes continue to run and deliver applications. However, keep in mind that if any pods fail during this period, they might not be restarted automatically. Once the master upgrade is complete, normal control plane operations resume.


After the master nodes are upgraded (for example, moving from version 1.10 to 1.11 while the worker nodes are still at 1.10), the next step is to upgrade the worker nodes. There are several strategies for this:


Upgrade all worker nodes simultaneously (which may result in downtime).


Upgrade one worker node at a time, allowing workloads to be shifted and ensuring continuous service.

Add new nodes with the updated software version, migrate workloads to these new nodes, and then decommission the older nodes.


**Case07: CRDs - Custom Resource Definitions**

CRDs allow you to define your own custom resources, extending the Kubernetes API to fit your specific needs.

**Why Use CRDs?**

CRDs are incredibly powerful because they enable you to represent and manage application-specific resources in a Kubernetes-native way. Here are some reasons why you might want to use CRDs:

**Abstraction: **
CRDs allow you to abstract away complex logic into custom resources, making it easier to manage and interact with your applications.


**Consistency: **
By defining custom resources, you ensure that your applications are consistently managed across different environments.


**Automation:**
CRDs enable automation by allowing you to define custom controllers that can watch and reconcile the state of custom resources.


**About Kustomize**

Kustomize provides a scalable solution to avoid excessive duplication. Instead of maintaining separate configurations for each environment, you can create a single Base configuration and apply environment-specific Overlays for adjustments.

Kustomize effectively addresses the challenges of managing configurations across multiple environments by allowing you to maintain a single Base configuration and apply minimal, environment-specific Overlays. This strategy ensures consistency, reduces maintenance overhead, and scales well as your Kubernetes deployments grow.

**Key Takeaways**

Kustomize requires a kustomization.yaml file that lists the resources to manage and the transformations to apply.
The kustomize build command processes these resource files and outputs the final configuration without deploying it.


Additional steps, such as piping the output to kubectl apply, are necessary to deploy the resources to your Kubernetes cluster.
By understanding and using the kustomization.yaml file properly, you can efficiently manage and transform your Kubernetes resources using Kustomize.







