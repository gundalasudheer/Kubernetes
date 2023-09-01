Kubernetes is a container orchestrations tool , which eliminates manual process involved in deploying and scaling the containerized application.
Kubernetes Architecture:
![Alt text](image-2.png)

Kube - API server:

1.It is a central hub of the cluster that exposes kubernetes api.
2.End users and other cluster components talk to cluster via kube api server.
3.Internal cluster components like scheduler,controller talk to API server using gRPC via TLS to prevent unauthorised access to the cluster.
4. End users and outside components talk to kube api server using HTTP REST apis.
5. It is the only component which talk to ETCD.

ETCD:
1. It is a brain of the Kubernetes Cluster.
2. It is a nonrelational distributed database which stores the data in key-value format.
3. It stores all the configurations, state and meta data of the kubernetes objects.
4. ETCD stores all the objects under /registry directory in key-value format.
5. ETCD allows user to subscribe events using WATCH() API.
5. When user requests the kubernets object details,you will get it from ETCD. Also, when you deploy, delete and modify an object an entry will be created in ETCD.

Kube Scheduler:
1. It is responsible for scheduling the pods on worker nodes.
2. The scheduler has two phases:
      a. Scheduling cycle:
            In this cycle it will identify the worker node for Scheduling the Pod.
            For identify the node it uses Filtering and Scoring method.
      b. Binding cycle:
            In binding cycle the changes are applied to the cluster.

3. Scheduler is a controller that listens to the API Server for the creation of pod events.
4. Scheduler always place high priorty pods over the lower priorty pods for scheduling.

Kube Controller Manager:
1. Controller is a program which runs continously and watchs the actual and desired state.
2. It always ensure the actual state matchs to the desired state.
3. Kube controller manager manages all the controllers in the kubernetes cluster.i.e kube scheduler, kubelet etc.

Cloud Controller Manager:
1. Cloud controller manager acts as a bridge between the cloud platform APIs and the kubernetes cluster.
2. It allows kubernets cluster to provision cloud resources like load balancers, storage volumes, instances, IAM etc.
3. Cloud controller manager has cloud platform specific controller which ensure the desired state of cloud components.
  a. Node controller:
      It updates the node releated information by talking to the cloud provider API. ex: Node Labelling, Hostname, Node health etc.
  b. Route Controller:
     It is respondible for configuring the network routes in the cloud platform. So, that pods in different nodes can talk to each other.
  c. Service Controller:
     It takes care of deploying services like load balancers for kubernetes services , assigning IP address etc.


Kubelet:
1. It is a worker node compnent which runs on every node.
2. Kubelet is responsible for registering the node with Kube- API server.
3. It primarily gets the PodSpec file from the API server which defines the container that should run inside the pod and the resource details etc.
4. Other than PodSpec from API server the kubelet will get the PodSpecs from file, Http endpoint and Http servers as well.
5. Good example of PodSpecs from file are kubernetes static pods. While bootstrapping the control plane , kubelet start the API-server,Kube Scheduler and controller manager as static pods from the PodSpecs located at /etc/kubernetes/manifests.
6. These static pods are controlled by Kubelet, not by API server.
7. Kubelet uses CRI(container runtime interface) gRPC interface to talk to the container runtime.
8. Kubelet uses CNI(container network interface) plugin configured in the cluster to allocate the IP address and setup any necessary network routes and fairewall rules for the Pod.
9. kubelet gets all the information about the containers from the container runtime and provides it to the control plane.

Kube-Proxy:
1. Kube proxy is a daemon that runs on every node as daemonset.
2. Its is a proxy component that implements the services concept for pods(Single DNS for a set of pods with load balancing).
   Services:
      a.Services in kubernetes is a way of exposing a set of Pods internally or to external traffic.
      b.When you create a service object it get virtual IP assigned to it(ClusterIP). It is only accessible within the kubernets.
      c. ClusterIp is not pingable and it is only used for service discovery.
      d. Service controller is responsible for configuring endpoints for service.
   EndPoints:
      a. The endpoint object is contains all the IP address and ports of pod groups under the service object.
      b. The endpoint controller is responsible for maintaining the list of IP address(endpoint).   
      c. Endpoints are pingable.
3. Kube-Proxy creates the network rules to send the traffic to the backend pods(endpoint) grouped under service object.
4. Kube-proxy talks to the API server to get the details about the service(clusterIP) and endpoint(PodsIP and Ports).
5. Kube-proxy also monitors for changes in services and endpoints.
6. Kube-proxy uses following modes to create/update the network rules to route the traffic to endpoints(pods) behind the services.
   a. IPTABLE -- it is default mode.
       In this mode kube-proxy chose the backend pod randomly for load balancing and connection is established.
       Then the requests will go to the same pod until the connection is terminated.
   b. IPVS:
        For Cluster with services exceeding 1000, this will improve the performance.
        It supports following load balancing algorithms for the backend.
          1. RR - round robin -- it is default mode.
          2. LC -- least connections -- smallest number of open connections.
          3. DH -- destination hashing
          4. SH -- Source hashing
          5. SED -- Shortest expected delay
          6. NQ -- Never queue
   c. Userspace -- Legacy & Not recommended.
   d. Kernelspace -- this is only for windows.

Container Runtime:
1. Container runtime is a software component that required to run the containers.
2. Container runtime is responsible for pulling the images from contaier registry, running containers, allocating and isolating resources for the containers. Also manages the lifecycle of the containers.
3. Container runtime runs on all the nodes.
