Pods are the smallest deployable unit in the Kubernetes.

In kubernetes Pods are used to encapsulate and run the containers using the container runtime.

Pods represent a single instance of application.

we can run multiple containers in a pod.

Pod has the unique IP address. Pods comminicate with each others using IP address.

In a multi-container pod , the containers will communicate using the local host on different PORTs as they reside in the same network. Also they share the same volume mount.

we can set CPU and memory resources for each container running inside the pod.

Pods are ephmeral in nature. They can be created , deleted and updated.