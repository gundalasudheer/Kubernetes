Pods are the smallest deployable unit in the Kubernetes.

In kubernetes Pods are used to encapsulate and run the containers using the container runtime.

Pods represent a single instance of application.

we can run multiple containers in a pod.

Pod has the unique IP address. Pods comminicate with each others using IP address.

In a multi-container pod , the containers will communicate using the local host on different PORTs as they reside in the same network. Also they share the same volume mount.

we can set CPU and memory resources for each container running inside the pod.

Pods are ephmeral in nature. They can be created , deleted and updated.

In Kubernetes there are kubernetes objects, Kubernetes resources and custom resources.

Kubernetes Objects:
    * These are items that user creates and retains. These include Pod, deployment, services, replicaset, daemonsets, volumes, namespaces,configmaps and secrets etc. 
    * ETCD stores all these objects under the key named /registry.
         ex: /registry/*
             /registry/pods/*
             /registry/namespaces/*
             /registry/daemonsets/*
             /registry/events/*   .... etc

    * To create these objects we describe what we want in a file using YAML or JSON called as object specification file.
    * There are many object types in kubernetes. So for writing these object files we have few common and necessary parameters which we use to write in them.

    Common Parameter are :	
 
        apiVersion :-
        	The Kubernetes API version for the objects 
             - Pod: v11
             - Service: v1
             - Deployment: apps/v1
             - ReplicaSet: apps/v1
        Kind :-
        	The type of object being defined. 
             - Pod 
             - deployment 
             - Services etc
        Metadata :-
        	Metadata is used to identify and  describe the Kubernetes object. 
            Some common key metadata that can be added to Kubernetes objects are
            - name 
            - labels 
            - namespaces
            - annotations
            - finalizers etc
        Spec :-	
            Under the spec section we declare the desired sate and characteristics of the object we want to create.       

Kubernetes Resources:

 In Kubernetes everythig is accepted through APIs.
 To create different objects, kubernetes API server provides API endpoints. These obejct specific endpoints are called API resources or resources.
  In simpler terms , a resource is a API URL used to acess the object.

