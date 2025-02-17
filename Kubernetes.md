### Section 2 - Kubernetes overview :

- Docker containers are isolated environments that share the kernel with the Operating System
- Container orchestration is the process of automatic deployment and management of the containers
- Kubernetes architecture:
	- Node (minion) is a worker machine, where containers are run on
	- Cluster is a set of nodes
	- Master is a node that is responsible for the actual orchestration of the nodes
- Kubernetes components:
	- Api server: front end for k8s, used by various methods to interact with the cluster
	- Etcd: a key-value store used to store all data used to manage the cluster, stored in a distributed manner on all nodes, also used in implementing locks
	- Scheduler: used for distribution (assigning) of containers across nodes
	- Controller: brains behind the orchestration, monitors and acts based on data gathered by monitoring
	- Container runtime: underlying software used to run containers (docker)
	- kubelet: agent that runs on nodes, monitors containers on nodes
- Master vs worker:
	- Master:
		- Kube-apiserver: is what makes it a master node
		- etcd key store
		- Controller
		- Scheduler 
	- Worker:
		- Kubelet: agent that interacts with the master node, sends health info and carry out actions requested by the master node
		- Container runtime
- `kubectl run` : used to deploy apps on cluster
- `kubectl cluster-info`: displays info about the cluster
- `Kubectl get nodes`: displays all nodes in the cluster
- Kubernetes container runtime interface is used by other runtimes as long as they use the Open Container Initiative
- OCI consists of imagespec that specifies how images should be built, and runtimespec defines how any container runtime should be developed
- containerd is part of docker, but mow it is a separate project![[IMG_0014.png]]
- ctr is used to interact with containerd (debugging)
- nerdctl is used to interact with containerd (general purpose)
- crictl is used to interact with CRI (debugging)
-  [Docker vs crictl](https://kubernetes.io/docs/reference/tools/map-crictl-dockercli/)

### Section 3 - Kubernetes concepts:

- Pod is the smallest deployable object in Kubernetes
- Pods usually have 1:1 relationship with containers
- To scale up, new pods are created
- Helper containers can be in the same pod
- `kubectl run nginx —-image nginx`: creates a pod and deploys the container made from image nginx grabbed from repo
- `kubectl get pods`: view pods in cluster
- `kubectl describe pod nginx`: print information 

### Section 4 - YAML:

- Dictionaries are not ordered, while lists are not. If there are 2 dictionaries, they are equal regardless of the elements order given that the keys have the same values

### Section 5 - Kubernetes concepts

- Any definition file in k8s contains 4 root level properties
	- apiVersion: specifies the kubernetes API version used to create object
	- kind: type of object (in capital)
	- metadata
		- In the label dictionary, you can create any key-value pair
	- spec: specification of the object to create
- `kubectl run redis --image=redis123 --dry-run=client -o yaml > redis-definition.yaml` to create yaml definition file
- `kubectl create/apply -f pod-definition.yml` to create pod
- can use `kubectl edit pod <podname>` to change the properties of the pod, or if we used a yaml definition file we can update the file then use apply the changes
-  The replication controller provides HA by running another instances of our deployed pod whether by running another pod at the same time or creating a new one if one fails
- It ensures that the specified number of pods is running all the time, also the multiple pods are used to load balance across pods
- It can span across multiple nodes in the cluster
- Replication controller is being replaced by ReplicaSets and it is the recommended
- The definition file of both is very [similar](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/):
	- Same 4 root properties, except in ReplicaSet the apiVersion is apps/v1
	- Under spec, define a template for pod and number of replicas, and in ReplicaSet it is required to add a selector.
	- Selector property is used in ReplicaSets because we can add pods that were created before the ReplicaSet, it is a label selector used to identify potential Pods to acquire.
-  To update the ReplicaSet `kubectl replace -f ReplicaSet-definition.yml` after updating file OR `kuberctl scale --replicas=6 -f ReplicaSet-definition.yml` OR `kuberctl scale --replicas=6 ReplicaSet myapp-ReplicaSet`
-  ![image](https://github.com/user-attachments/assets/b805885e-fe4d-4eb1-964e-f8533da808db)

- The definition file for deployments is the same as ReplicaSet, with king being Deployment instead [link](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- The deployment automatically creates a ReplicaSet
- Rolling update is the default deployment strategies for updates to deployments
- Controller creates a new ReplicaSet and deploy new pods there
- Can rollback using rollout undo deployment command, this destroys new pods and bring old ones up in the old ReplicaSet
- `kubectl set image deplyment/myapp-deployment <containername>=nginx:1.9.1` update used image
- `kubectl rollout status deployment/myapp-deployment`
- `kubectl rollout history deployment/myapp-deployment`
- `kubectl rollout undo deployment/myapp-deployment`
- when creating deployments, --record options record change-cause viewed in history

### Section 6 - Networking

- Each pod gets an internal IP address
- during initial config of k8s, a private network is created on the node. Pods are assigned IPs from the network's range
- When pods are recreated they are assigned different IPs
- Network requirements by k8s:
	- All pods can communicate with each other without NAT
	- All nodes communicate with all containers and vice-versa without NAT

### Section 7 - Services

- Services are objects used to expose pods to other pods or users
- Service is like a virtual server inside the node, it has an IP address that is called the cluster IP of the service
- NodePort service is used to listen to a node's port and forward traffic to pods
- ![image](https://github.com/user-attachments/assets/3e358cf9-b0d8-475f-8d2f-a768dfc2ba97)

- 3 ports are involved in NodePort (from POV of the service):
	- TargetPort: port of the pod
	- Port: service's port (cluster IP : port). This is the only mandatory field, targetPort is assumed to be the same as this port if not specified
	- NodePort: the port on the node exposed to users (30000 -> 32767)
- Services are created with yaml files like other objects
- The yaml file is the same but under spec it has type, list of ports, selector to identify pod
- labels and selectors are used to link services to pods
- If multiple pods with same labels exist (replicas), the NodePort service acts as load balancer
- If the pods are on different nodes, services can span across multiple nodes, it uses the same configuration applied
- ![image](https://github.com/user-attachments/assets/fd7f33fa-da17-48e9-9270-f902c3684b66)

- ![image](https://github.com/user-attachments/assets/9af26da9-7ab1-4139-a0b3-c3f947adf186)

- ClusterIP  service is used as interface for pods to communicate with each other inside the cluster
- The definition file for ClusterIP is the same as NodePort, but it doesn't specify a NodePort.
- In the selector (matchLables is not used)
- **ClusterIP** can mean 2 things: a type of service which is only accessible within a Kubernetes cluster, or the internal ("virtual") IP of components within a Kubernetes cluster
- If we have 2 nodes, each one having a different pod. A service is configured to make users able to reach pods. The user can use any of the 2 nodes' IP to connect to a pod. 
	- IP1 + Port of pod1 OR IP2+ Port of pod2
	- ![image](https://github.com/user-attachments/assets/270e6ef3-5b43-4591-879e-68dfddab5703)

- LoadBalancer service type is used only with supported cloud providers, setting them outside of them will lead to the service type being a NodePort
- There is a default ClusterIP service at launch
- Kubernetes endpoints are essentially a collection of IP addresses and port numbers that define the backend pods associated with a Kubernetes service
- Endpoints are pods that were identified by the service based on the selector
- Pods expose themselves through endpoints to a service
-  **ClusterIp exposure (internal) < NodePort exposure (external to cluster) < LoadBalancer exposure**
---

### Section 8 - Microservices

- Docker containers need links to communicate with each other (deprecated method), use networks
- best practice is to use env variables in code to give apps the hostname of the required service
- You can use the `--dry-run=client` flag to preview the object that would be sent to your cluster, without really submitting it.
---
