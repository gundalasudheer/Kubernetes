Kubernetes is a container orchestrations tool , which eliminates manual process involved in deploying and scaling the containerized application.


Kubernetes Architecture:

![Alt text](image-2.png)

Kube - API server:

1.It is a central hub of the cluster that exposes kubernetes api.

2.End users and other cluster components talk to cluster via kube api server.

3.Internal cluster components like scheduler,controller talk to API server using gRPC via TLS to prevent unauthorised access to the cluster.

4. End users and outside components talk to kube api server using HTTP REST apis.

5. It is the only component which talk to ETCD.
6. If we setup the cluster hardway  we need to configure the kube-apiserver.service for connecting and communicating with the other components.
   Here we use the parameter --etcd-servers to specify the etcd details.
    /etc/systemd/system/kube-apiserver.service -- location of kube-apiserver.service file for non kubeadm setup.
7. If we setup the cluster using kubeadm then the kube-apiserver is deployed as pod in kube-system namespace. You can find the kube-apiserver pod yaml file under /etc/kubernetes/manifests/ location.

ETCD:
1. It is the brain of the Kubernetes Cluster.
2. It is a nonrelational distributed database which stores the data in key-value format. It is simple , secure and fast.
3. It stores all the configurations, state and meta data of the kubernetes objects. Every info you see when you use kubectl get is from etcd.

Every change you make to the cluster is recorded in the etcd server. Once it is updated in the etcd server, then only the change is considered as complete. 

4. ETCD stores all the objects under /registry directory in key-value format.

ex: /registry/*

    /registry/pods/*

    /registry/namespaces/*

    /registry/daemonsets/*

    /registry/events/*   .... etc

5. ETCD allows user to subscribe events using WATCH() API.
6. When user requests the kubernets object details,you will get it from ETCD. Also, when you deploy, delete and modify an object , an entry will be created in ETCD.
7. To install the ETCD. a. Download the binaries from the github. b. extract and c. Run the etcd services.
8. By default it runs/listens on the port 2379. we can attach any clients to etcd to store and retrive any information.
9. The default client comes with etcd is etcd control client.
10. we can use ./etcdctl set(for v2)/put(for v3) key1 value1 to store the value into etcd.
11. ./etcdctl get key1 to get the details of key1.
12. for any help we can use ./etcdctl --- this will display all the options which will support that particular api version of the etcd.
13. ./etcdctl -- version  --- this command will list both the etcd and api versions.
14. To set the API version for ETCD we can use below commands.
    ETCDCTL_API=3 ./etcdctl version --- this will set the api version for that particular command only.
    export ETCDCTL_API=3
      ./etcdctl version --- by doing the export we set env varaible for the api. so it will be applicable for the entire session.
15. If we setup the cluster hardway then we need to download the binaryfiles of the ETCD and run it as service. You can get/set the required details from/in etcd.service files. Advertise-client-url is where the etcd listens. This is the url you need to configure in kubeapi server to reach to etcd.
16. In high availability env we hve multiple nodes of masters in the cluster where we have multiple ETCDs running. So to know abt each other we need to set the initial-cluster parameter of each instance.
  ex: --initial-cluster controller-0=https://${controller0-ip}:2380,controller-1=https://${controller1-ip}:2380 
17. If we deploy the cluster using the kubeadm then it will deploy the components of the cluster as pods in the kube-system namespace. These pod definition files are stored under /etc/kubernets/manifests/ location.


Kube Scheduler:
1. It is responsible for scheduling the pods on worker nodes.
2. The scheduler has two phases:
      a. Scheduling cycle:
            In this cycle it will identify the worker node for Scheduling the Pod.
            For identify the node it uses Filtering and Scoring method(Here it uses the priority fucntion to score the nodes).
      b. Binding cycle:
            In binding cycle the changes are applied to the cluster.

3. Scheduler is a controller that listens to the API Server for the creation of pod events.
4. Scheduler always place high priorty pods over the lower priorty pods for scheduling.

Kube Controller Manager:
1. Controller is a program which runs continously and watchs the actual and desired state.
2. It always ensure the actual state matchs to the desired state.
3. Kube controller manager manages all the controllers in the kubernetes cluster.i.e kube scheduler, kubelet etc.
4. we have many controllers in the cluster. examples are a. node controller and b. replication controller as well
   a. node controller:
     this will monitor the nodes for every 5s through kube-apiserver.
     it will wait 40s to mark that node as unreachable and also waits for 5min for the node to comeback.
     if the node is still non responsive then the pods running on the node will be moved to other nodes if the pods are part of replicas.

   b. replication controller:
    This will monitor the replicasets and always matchs the actual state to desired state.  

5. If you setup the cluster hardway then you need to download the binaries and extract it and run it as a service.
   In the kube-controller-manager.service file we will specify what are the controllers need to be enabled (by default all the controllers are enabled) under the --controllers parameter. Also we can specify the details like --node-monitor-period , --node-moitor-grace-period , --pod-eviction-timeout etc here.
   /etc/systemd/system/kube-controller-manager.service is where the nonkubeadm setups will store the service file.
6. If we set up the cluster using the kubeadm then the Kube-controller-manager is deployed as a pod.
   we can see the details of the pod under /etc/kubernetes/manifests/ location.


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
  If you want to set the custom path other than the /etc/kubernetes/manifests .. then we need to specify those pod location under the --pod-manifest-path field in the kubelet.service file. or instead of the configuring the location directly in the kubelet.service file just config the config yaml file in which the static pod location will be present.
    i.e --config=kubeconfig.yaml
      in the kubeconfig.yaml we will give the static pods location. i.e staticPodPath: /etc/kubernetes/manifests. Kubeadm will use this kind of approch while settingup the cluster.
6. These static pods are controlled by Kubelet, not by API server.
7. Kubelet uses CRI(container runtime interface) gRPC interface to talk to the container runtime.
8. Kubelet uses CNI(container network interface) plugin configured in the cluster to allocate the IP address and setup any necessary network routes and fairewall rules for the Pod.
9. kubelet gets all the information about the containers from the container runtime and provides it to the control plane.
10. If we use kubeadm tool to setup the cluster it wont deploy the kubelet. WE MUST ALWAYS INSTALL KUBELET MANUALLY ON THE WORKER NODES. 
 So download the binaries ,extract it and run it as the service (kubelet.service).
 

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