Namespaces are a way to isolate the group of resources in the single cluster. By using the namespaces we can also divide the cluster resources to use between the groups/users via resource quota. 
Each namespace has thier own set of policies i.e which namespace can do what.

By default the kubernetes creates 4 namespaces.
   1. Default namespace
        This default namespace helps you to use the cluster without creating any new namespace.
        If there are few or tens of users, then this namespace is enough to use. You no need to create any other namespace.
   2. Kube-system namespace
         This namespace is used by the kubernetes for the objects created by kubernetes.

   3. Kube-public namespace
        This the namespace which is available for all the users(nonauhtorized users as well). So , the resources in this namespace is also available for all.
   4. Kube-node-lease
     This namespace holds the lease objects in the node. Node lease helps kubelet to send the hearbeat of the node to controlplane to detect the failures. 

To connect/use resources in other namespaces you use the DNS in the below format:
  syntax:- <servicename>.<namespace>.svc.cluster.local
      here cluster.local is default domain for kubernetes cluster.
           svc is subdomain for service.
   
    example: mysql.connect("db-service.dev.svc.cluster.local")