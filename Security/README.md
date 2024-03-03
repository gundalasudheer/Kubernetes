In kubernetes Kube-API server is the central component with which every component will interact.
also we will access and perform any action using this API server using the kubectl. So first we need to make this secure to avoid the unnecessary actions on the cluster.
So we have two things to takecare here.
   1. Authentication -- who can access.
        different ways to authenticat
          i.username and password
          ii. username and token
          iii. certificates
          iv. integration with external authentication provides like LDAP
          v. service Accounts for the machines
    
   2. Authorization -- what actions can be performed.
        different ways to authorize
          i. RBAC - Role Based Access Controls
          ii. ABAC - Attribute Based Access Controls
          iii. Node Authorization
          iv. Webhook mode

Access between the nodes can be controlled by NetWork Policies


In kubernetes we have 3 kind of certificates
   1. Server Certificate (KubeAPI server , ETCD , Kubelet server)
   2. Client Certificate ( clients , KubeScheduler , KubeControl Manager , kubeAPI for ETCD , KubeAPI for Kubelet)
   3. Root Certificate (Certificate Authority)

To generate the certificates we will use the following Openssl commands.
  i. openssl genrsa -out ca.key 2048 -- here we will generate the private key and store in ca.key. The size of the key should be 2048. If it is less than that then we will have the security issues and if you have more than that then it will have performance issues.
  ii. openssl req -new -key ca.key -subj "/CN=kubernetes-ca" -out ca.csr --- here we are generating the CSR(Certificate signing request) using the generated private key for the provided information details under the subj option.
  iii. openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt --- Here by using the above generated privatekey and CSR we will be signing the certificate.
  iv. openssl x509 -in ca.crt -text -noout  -- by using this command we can read the sertificate in a text format and cross verify with the private key.
  v. openssl req -new -key client.key -subj "/CN=kube-admin /OU=systems-master" -out client.csr
  vi. openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -out client.crt

  imp:- while creating the client certificates for kube scheduler and kube control manager you need to mention the CN(common names) as SYSTEM:<common name> ie. CN=SYSTEM:KUBE-SCHEDULER , CN=SYSTEM:KUBE-CONTROLLER-MANAGER because these are system components in the controlplan .

By using these certificates we can communicate by using in the RESTAPIs.
   ex: curl https://kube-apiserver:6443/api/v1/pods --key client.key --cert client.crt --cacert ca.crt

While creating the certificates for kubeapi server we need to provide the alternative names as well as they are called differently btw the ppl
  ex: kubernetes
      kubernets.default
      kubernetes.default.svc
      kubernetes.default.svc.cluster.local
      <IP address>
So while specifying these alternative names in the certification we need to create the config file and provide all the alternative names under the alternative names section. Then you need to provide this file while creating the CSR under the option config.
  openssl req -new -key server.key -subj "/CN=kube-apiserver" -config <name.config>

  cat /etc/systemd/system/kube-apiserver.service -- certificates location if we setup the cluster by hardway
  cat /etc/kubernetes/manifests/kube-apiserver.yaml -- certificates location if we setup using the kubeadm
