When ever there is a new deployment created and deployed then a rollout is created and rollout will create the revisions according to the updates of the application.
we can check all the revisions or history of the applications using the below rollout commands
 kubectl rollout history <deploymentname>
 kubectl rollout status <deploymentname>

 Deployment strategies:
  1. Recreate strategy.
      in this strategy the current version of the applications are brought down and new version applications will be created. Due to this strategy application will have the down time and user wont be able to access the application during this upgrade.
  2. Rolling update strategy (it is a default strategy)
       in this strategy one instance of the application will be brought down and new one will be created in that place. by this the other application instances will be available for the user.

if the upgrade goes wrong then we want to roll back the changes made to the application then we can use the below command

kubectl rollout undo <deploymentname>

CMD and EntryPoint in docker:
  CMD : is the place where we will specify the parameters that will execute when the container starts. These values are static.
  ex: cmd ["sleep","5"] -- the first parameter(sleep) should always be executable.
  EntryPoint : is the place where we will specify the parameter which will be appended to the input provided by the user to execute at the start of the container.
   ex: entrypoint ["sleep"] -- here while running the container the user will enter the value for the sleep which is always dynamic. so the sleep parameter will be appended to the value and goes as input for the container to execute while starting.
 If user forgots to enter the input while running the container then it will through the error called "missing operand".
 To avoid this we can use both the cmd and entrypoint together. So the cmd will have the default value which incase will be used if the user forgots to enter the input.
  ex: entrypoint ["sleep"]
      cmd["5"] --- here if the user din't mention any input then the sleep parameter will take the value 5 as input.
 If we want to override the entrypoint command during the run time then we can use the below command.
  ex: docker run --entrypoint sleep2.0 <dockerfile> 10

Commands and arguments in Kubernetes:
  In kubernetes the entrypoint will be replaced by command and cmd will be replaced by args.
   ref the pod.yaml file in the section.

Environment variable in kubernetes:
 we will set the environment variable in kubernetes in the pod specification under the env field using the name and value arguments.
   ex: env:
        - name:
          value:

  in docker we will use
    docker run -e <name>=<value> simple-webapp

 In kubernetes we can specify the env variables in 3 ways
   i. using the value directly i.e. plain key=value format
       env:
        - name:
          value:
   ii. using the values from the configmap
       env:
        - name:
          valueFrom:
            configMapKeyRef:
   iii. using the values from the secrets
       env:
        - name:
          valueFrom:
            secretKeyRef:

Configuration of ConfigMaps in Kubernetes:
       This is were the env variables are read from a file. Is like a plain text.
       At first we will be creating the configMap
         for creating the configmap we have two ways.
           i. Imperative
              here we will be directly using the kubectl to create command 
               ex: kubectl create configmap <configmapname> --from-literal=<key>=<value>
                  kubectl create configmap <configmapname> --from-file=<pathofthefile>
               This approach is okay if we have less data(key:value) to define
           ii. declarative
               here we will be creating the configmap using the configmap.yaml file.
       Then add that configMap to the Pod spec

Secrets in Kubernetes.
The data defined in env and configMap are like a plain text. So sensitive info cannot be stored and also hardcoded.
So we use secrets to store the sensitive info by encoding it but not the encrypted.

to create the secret in imperative way.
  kubectl create secret generic <secretname> --from-literal=<key>=<value>
  kubectl create secret genric <secretname> --from-file=<pathofthefile>
  kubectl create -f <secrets.yaml>

to encode the plain text we use below linux command

echo -n <"requireddata"> | base64

to decode the encoded text
 echo -n <"encodedtext"> | base64 --decode 

In ETCD the encoded data will be stored in a plain text. So we need to encrypt the data in etcd which will be stored in the rest.
To do so Kubeapi-server should be configured with below parameter
- --encryption-provider-config=/etc/kubernetes/enc/enc.yaml # we need to provide the encryption file location.
and also added the file location under volumeMounts for the pod to access and under the volumes to mount the local file system.
ex: volumeMounts:
    ...
    - name: enc                           # add this line
      mountPath: /etc/kubernetes/enc      # add this line
      readOnly: true                      # add this line
    ...
  volumes:
  ...
  - name: enc                             # add this line
    hostPath:                             # add this line
      path: /etc/kubernetes/enc           # add this line
      type: DirectoryOrCreate 
