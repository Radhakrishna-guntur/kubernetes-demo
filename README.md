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

