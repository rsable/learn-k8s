# learn-k8s
Notes from when I learnt K8s

## Architecture :
* **Worker Node :**
    * Should have 3 processes on every node :
        * A container runtime : Cri-o, Docker Engine.
        * Kublet : Actual process responsible for starting a pod, assign resources and making sure they are running.
        * Kube Proxy : Is a network proxy used to forward requests etc between services in an optimzed manner. The requests could be from inside (pod-to-pod) or outside the cluster.
* **Master Node :**
    * Manage the worker nodes and monitors them.
    * There are 4 process on every master node :
        * Api Server : Interface used to validate, manage, create and configure k8s cluster. It could be GUI, API or CLI (kubectl). *Api server is usually load balanced between multiple Master nodes.*
        * Schedule : *Determines* which node to start a pod on. Kublet will actually start the pod after receiving a request from scheduler.
        * Control Manager : Various control loops that detect state changes, like crashing of pods and then decide on the next action.
        * etcd : This key:value data store is the clusters brain. Every change in the cluster is saved here eg when a new pod is scheduled, or a pod died, the resocures available on nodes, cluster health. Using this the queries from Scheduler / Api Server are answered. *[etcd](https://www.ibm.com/cloud/learn/etcd#:~:text=etcd%20is%20an%20open%20source,the%20popular%20container%20orchestration%20platform.) is fully replicated across all the master nodes.* ᕙ(⇀‸↼‶)ᕗ

## Concepts Overview :
* **Node - Pod - Container :**
    * Node could be a baremetal system / virtual host.
    * Pod is an abstraction layer for container i.e. it creates an layer over the container, so that k8s can manage it.
    * There can be multiple pods on a node and there can be multiple containers in one pod. But usually one pod is limited to one contaner for simplicity.
    * Each pod will have its own IP address, but pods are ephemeral. So we will need a service to manage the IP (╯°□°）╯┻┻
* **Service & Ingress :**
    * Service could be an IP address with a DNS name that is attached to a pod.
    * The lifecycle of a service and pod are disconnected, so even if a pod dies the service will continue to work.
    * A service could be internal or external.
    * Service also acts as a load balancer between multiple nodes.
    * Ingress is used to manage extrnal access to services or more speifically route traffic into the cluster.
* **ConfigMap & Secret :**
    * ConfigMap is an extrenal easibly changable configuration for the application. Could be a URL to something / DB Url / path. Consider this as a Variable ¯\\_(ツ)_/¯
    * Just like ConfigMap, but to save super super secret stuff use Secret. 
    * Both of these are connected to the pod to be used.
* **Volumes :**
    * To store persistent data.
* **Deployment & StatefulSet :**
    * Deployment is the blueprint for pods.
    * Will have details about pod, and other configurational details like number of replicas (Replicaset) etc.
    * StatefulSet is similar to deployment but deployment can not manage statful applications like database, which have active I/O and can cause data corruption if run though multiple sources. So StatefulSet manages such applications along with other tasks like replication etc. which are done by Deployment.

## Deploying the first cluster :

* Install :
    ##### **Minikube** : 
    > [Minikube](https://minikube.sigs.k8s.io/docs/start/) is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes and to create test clusters.
    ##### **Kubectl** :
    > The Kubernetes command-line tool, [kubectl](https://kubernetes.io/docs/tasks/tools/#:~:text=on%20your%20computer.-,kubectl,cluster%20resources%2C%20and%20view%20logs.&text=kubectl%20is%20installable%20on%20a,Linux%20platforms%2C%20macOS%20and%20Windows.), that allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs.
* Create a cluster ლ(｀ー´ლ) :
    ```ps
    PS G:\K8s> minikube.exe -p c1 start
    PS G:\K8s> minikube.exe -p c1 status
    c1
    type: Control Plane
    host: Running
    kubelet: Running
    apiserver: Running
    kubeconfig: Configured
    ```
* Creating a test pod :
    ```ps
    PS G:\K8s> kubectl create deployment nginxtest --image=nginx
    deployment.apps/nginxtest created
    PS G:\K8s> kubectl.exe get pod
    NAME                        READY   STATUS              RESTARTS   AGE
    nginxtest-d487d74db-4jqbb   0/1     ContainerCreating   0          3s
    PS G:\K8s> kubectl.exe get pod
    NAME                        READY   STATUS    RESTARTS   AGE
    nginxtest-d487d74db-4jqbb   1/1     Running   0          2m21s
    ```
* Deployments and replicas :
    ```ps
    PS G:\K8s> kubectl.exe describe deployment nginx
    Name:                   nginxtest
    Namespace:              default
    CreationTimestamp:      Sun, 13 Feb 2022 01:54:40 +0530
    Labels:                 app=nginxtest
    Annotations:            deployment.kubernetes.io/revision: 1
    Selector:               app=nginxtest
    Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:           RollingUpdate
    MinReadySeconds:        0
    RollingUpdateStrategy:  25% max unavailable, 25% max surge
    Pod Template:
      Labels:  app=nginxtest
      Containers:
       nginx:
        Image:        nginx
        Port:         <none>
        Host Port:    <none>
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   nginxtest-d487d74db (1/1 replicas created)
    Events:
      Type    Reason             Age    From                   Message
      ----    ------             ----   ----                   -------
      Normal  ScalingReplicaSet  4m25s  deployment-controller  Scaled up replica set nginxtest-d487d74db to 1
    
    PS G:\K8s> kubectl.exe get replicaset
    NAME                  DESIRED   CURRENT   READY   AGE
    nginxtest-d487d74db   1         1         1       3m53s
    ```
* Edit deployments :
    * Let's have 2 replicas of nginx :
    ```ps
    PS G:\K8s> kubectl.exe edit deployment nginxtest
    deployment.apps/nginxtest edited
    PS G:\K8s> kubectl.exe describe deployment nginx | findstr /i replicas:
    Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
    PS G:\K8s> kubectl.exe get pod
    NAME                        READY   STATUS    RESTARTS   AGE
    nginxtest-d487d74db-4jqbb   1/1     Running   0          14m
    nginxtest-d487d74db-prqmb   1/1     Running   0          20s
    PS G:\K8s> kubectl.exe get replicaset
    NAME                  DESIRED   CURRENT   READY   AGE
    nginxtest-d487d74db   2         2         2       17m
    ```
    * Similarly you can change other componenets like image version etc.