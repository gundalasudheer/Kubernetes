While doing the OS upgrades on a particular node we are not sure how much time it is going to take to come back.
If the node is not accssible for 5 mins then the node is marked as offline and all the pods on the node will be terminated.
Also not sure all the pods which are running on the node are part of replicasets. So to make sure the pods are not loose, first we need to move those pods to other nodes which will be available during this time. This process is called DRAINing the node
kubectl drain <nodename>
During this draining period the node will be kept on noschedule.
Once the maintenance is done and node comes back still the node will be under the noschedule state only.
To make the node to schedulable we need to undrain that useing the below command.
kubectl uncordon <nodename> 
There is another option called cordon where the node will be kept to noschedule but the pods on the node are not terminanted or moved to another node.
kubectl cordon <nodename>


Cluster Upgrade:

The version of the kubernetes will look like v1.12.13 -- here v1 is the major ,12 is the minor and 13 is the patch.
Kubernetes will always support the latest 3 minor versions.
All the kubernets components will be in the same version of kube api server or less than that. but etcd cluster and coreDNS will have a different version than kubeapi server.

for example kube api server is of X version
            kube scheduler and control manager will be either in X or X-1 version.
            kubelet and kubeproxy will be in X , X-1 or X-2
            kubectl will be X+1 , X or X-1

While upgrading the cluster we will be upgrading the control plane first. During this time the applications running on the worker nodes will be available to user. But no new nodes,pods or interacting with the cluster will be happeing as the controlplane is down.
While upgrading the worker node we  have 3 strategies.
    1. All the worker nodes are upgraded at a time -- this require downtime as no application is accessable.
    2. Upgrading the one node at a time by draining that node.
    3. Creating the new nodes with the new version and terminating the older ones. This can be done on the cloud managing environments.

If we are ungradingt the managed kub cluster environments on the cloud, then we can do that by few clicks.
if we are doing it on the hardway setup cluster then we need to download the required binaries and upgrade.
If we are doing on the kubadm based cluster setup then we need to plan and applu the upgrade.
    kubeadm upgrade plan
    kubeadm upgrade apply
After upgrade the kubernetes you need to manually upgrade the kubelet on each node as kubadm wont upgrade them. After upgrading restart the kubelet.
Before upgrading the kubernetes you need to upgrade the kubeadm.
        apt update
        apt-cache madison kubeadm ## This will give us the latest version of the upgraded version in format v1.20.x-*
        apt-mark unhold kubeadm && \
        apt-get update && apt-get install -y kubeadm='1.29.x-*' && \
        apt-mark hold kubeadm

kubeadm upgrade apply <version>   ### kubeadm upgrade apply v1.28.x

        apt-mark unhold kubelet kubectl && \
        apt-get update && apt-get install -y kubelet='1.28.x-*' kubectl='1.28.x-*' && \
        apt-mark hold kubelet kubectl   ### if controlplane components are running as pods on the master node then we will have kubelet. So you need to do this. If kubelet is not there in the masternode you can skip this.
systemctl daemon-reload
systemctl restart kubelet

on worker node:

kubectl drain <nodename>  ## this need to be run on the controlplane.

apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm='1.28.x-*' && \
apt-mark hold kubeadm

kubeadm upgrade node

apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet='1.28.x-*' kubectl='1.28.x-*' && \
apt-mark hold kubelet kubectl

systemctl daemon-reload
systemctl restart kubelet
kubectl uncordon <nodename>  ## this need to be run on the controlplane.

kubectl get all --all-namespaces -o yaml > all-objects.yaml





