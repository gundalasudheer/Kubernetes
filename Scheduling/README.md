Taints and Tolerations:-
   Taints are set to Nodes and Tolerations are set to pods.
So when you define the taints on node then the node wont allow any other pods except the pods which are tolerent with the node.
kubectl taint nodes <node-name> key=value:<taint-effect>
Taint-effect:- we have three taint effects
       1. NoSchedule
       2. PreferNoSchedule
       3. NoExecute

Taint and Tolerations are only meant to restrict nodes from accepting certain pods. But we cannot guarantee that the pods will be scheduled only on that particular node.
  ex: if we set the node taint to app=web-app then the node can accepts only the pods which are of app=web-app. but we cannot guarantee that the pod(app=web-app) will only go that node. because if there are any other nodes which not tainted anything then we have enough chances that the node can be placed on those nodes.

We can notice that on the master there wont be any pods scheduled even though it has all the properties same like node. This is because there is a taint set on the master.
   kubectl describe node kubemaster | grep Taints
   Taints:   node-role.kubernetes.io/master:NoSchedule

If pods are running on the node and the taint is applied with NoExecution then the pods which dont have the tolerations will be killed.
 To Untaint the node we use below command:
   kubectl taint node <node-name> key=value:<taint-effect>-
    we need to use the - at the last of command to untaint.
For untainting the master node
   kubectl taint node master node-role.kubernetes.io/master:NoSchedule-


Node Selector:-
     Here we will label the nodes and use the same labes on pods under spec section using nodeSelector as a key and provide the required value.

To label the node we use below command
     kubectl label node <nodename> <label-key>=<label-value>

In this node selector approch we cannot use the multiple options for selectors as we only can maintain one value in the key value pair.
  for example if we want to place the pod on large or medium node or we want to place pods on not small node etc.

We can acheive this by using node affinity.
Node Affinity:
  This is used/ensure pods are placed/scheduled/hosted on particular nodes.
  Here we have the nodeaffinity types and nodeselector terms where we provide the matching expressions(in key ,operator and values formate).

  Nodeaffinity Types:
   1. requiredDuringSchedulingIgnoredDuringExecution.
   2. preferredDuringSchedulingIgnoredDuringExecution.
   Planned(planning to introduce the below types as well)
   1. requiredDuringSchedulingRequiredDuringExecution.
   2. preferredDuringSchedulingRequiredDuringExecution.
