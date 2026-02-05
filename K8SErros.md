## 𝑼𝒏𝒅𝒆𝒓𝒔𝒕𝒂𝒏𝒅𝒊𝒏𝒈 𝑲𝒖𝒃𝒆𝒓𝒏𝒆𝒕𝒆𝒔 𝑷𝒐𝒅 𝒇𝒂𝒊𝒍𝒖𝒓𝒆𝒔

Pods can have startup and runtime errors.

**Startup errors include:**

✅ ImagePullBackoff 

✅ ImageInspectError 

✅ ErrImagePull 

✅ ErrImageNeverPull 

✅ RegistryUnavailable 

✅ InvalidImageName 

**Runtime errors include:**

📌
✅ CrashLoopBackOff

✅ RunContainerError

✅ KillContainerError

✅ VerifyNonRootError

✅ RunInitContainerError

✅ CreatePodSandboxError

✅ ConfigPodSandboxError

✅ KillPodSandboxError

✅ SetupNetworkError

✅ TeardownNetworkError

**𝑰𝒎𝒂𝒈𝒆𝑷𝒖𝒍𝒍𝑩𝒂𝒄𝒌𝑶𝒇𝒇**

❗
✍ This error appears when #k8s isn’t able to retrieve the image for one of the #containers of the Pod.
There are three common culprits:

✅ The image name is invalid

✅ You specified a non-existing tag for the image.

✅ The image that you’re trying to retrieve belongs to a private registry and the cluster doesn’t have credentials to access it.
The first two cases can be solved by correcting the image name and tag.

For the last, one should add the credentials to your private registry in a Secret and reference it in the Pods

**𝑹𝒖𝒏𝑪𝒐𝒏𝒕𝒂𝒊𝒏𝒆𝒓𝑬𝒓𝒓𝒐𝒓**

❗
✍ The error appears when the container is unable to start before application

**Common causes:**

✅ Mounting a not-existent volume such as ConfigMap or Secrets

✅ Mounting a read-only volume as read-write

More detailed aspect can be found by describing the ‘failed’ pod

**𝑪𝒓𝒂𝒔𝒉𝑳𝒐𝒐𝒑𝑩𝒂𝒄𝒌𝑶𝒇𝒇**

❗
✍ If the container can’t start, then #Kubernetes shows the CrashLoopBackOff message as a status.
Usually, a container can’t start when:

✅ There’s an error in the application that prevents it from starting.

✅ You misconfigured the container.

✅ The Liveness probe failed too many times.


**𝑷𝒐𝒅𝒔 𝒊𝒏 𝒂 𝑷𝒆𝒏𝒅𝒊𝒏𝒈 𝒔𝒕𝒂𝒕𝒆**

❗
✍ Assuming that the scheduler component is running fine, here are the causes:

✅ The cluster doesn’t have enough resources such as CPU and memory to run the Pod.

✅ The current Namespace has a ResourceQuota object and creating the Pod will make the Namespace go over the quota.

✅ The Pod is bound to a Pending PersistentVolumeClaim.

**Note: The best option is to inspect the Events section in the “kubectl describe”**



# Common Kubernetes Real Time Challenges | Production Scenarios

**1. Effectively Sharing Cluster Resources Among Multiple Development Teams**

**Challenge:** Multiple teams often deploy workloads on the same cluster, leading to resource contention.

**Issues:**

One team’s application could consume excessive CPU/memory.

Risk of "noisy neighbor" problems.

**Solutions:**

Use Namespaces to logically isolate team environments.

Set Resource Quotas and Limit Ranges to restrict CPU/memory usage per namespace.

Implement Network Policies for traffic isolation.

Monitor usage with Prometheus + Grafana or Kubernetes Metrics Server.

**2. Addressing Out-of-Memory (OOMKilled) Errors in Pods:**

Challenge: Pods are terminated by the kubelet due to exceeding memory limits.

**Causes:**

Inaccurate memory requests/limits.

Memory leaks in applications.

High traffic spikes not accounted for in resource planning.

**Solutions:**

Set appropriate resources.requests and resources.limits in pod specs.

Analyze metrics and logs with kubectl describe pod, Grafana, or ELK Stack.

Conduct load testing and profiling during CI/CD pipeline stages.

Use Horizontal Pod Autoscaler (HPA) to scale pods based on memory or CPU metrics.

**3. Complexities in Performing Kubernetes Cluster Upgrades:**

Challenge: Upgrading Kubernetes without downtime or breaking workloads is complex.

**Issues:**

Compatibility problems with older APIs/deprecated features.

Downtime if upgrades aren’t properly staged.

Risk of cluster instability.

**Solutions:**

Follow a staged upgrade process:

Backup etcd and cluster resources.

Upgrade the control plane first, then worker nodes.

Use kubeadm upgrade or cloud-managed tools (EKS, AKS, GKE upgrade features).

Validate with pre-upgrade testing environments.

Review Kubernetes deprecation notices before upgrades.

Use tools like kured for safe node reboots post-upgrade.


