# Deploying the first cluster

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
* Creating and accessing a container :
    * Uggh the container crashed.   
    * Understand [CrashLoopBackOff](https://komodor.com/learn/how-to-fix-crashloopbackoff-kubernetes-error/#:~:text=To%20identify%20the%20issue%20you,controller%2Dmanager%20pod%20and%20restart.) :
    ```ps
    PS G:\K8s> kubectl create deployment ubuntu-test --image=ubuntu:latest
    deployment.apps/ubuntu-test created
    PS G:\K8s> kubectl.exe get pods
    NAME                           READY   STATUS              RESTARTS   AGE
    ubuntu-test-85c8959887-ztrgg   0/1     ContainerCreating   0          3s
    PS G:\K8s> kubectl.exe get pods
    NAME                           READY   STATUS             RESTARTS     AGE
    ubuntu-test-85c8959887-ztrgg   0/1     CrashLoopBackOff   1 (7s ago)   16s
    ```
    * Lets see whats happening in the pod :
    ```ps
        PS G:\K8s> kubectl.exe describe pod ubuntu-test-85c8959887-ztrgg
    Name:         ubuntu-test-85c8959887-ztrgg
    Namespace:    default
    Priority:     0
    Node:         c1/172.18.156.98
    Start Time:   Sun, 13 Feb 2022 16:42:51 +0530
    Labels:       app=ubuntu-test
                  pod-template-hash=85c8959887
    Annotations:  <none>
    Status:       Running
    IP:           172.17.0.3
    IPs:
      IP:           172.17.0.3
    Controlled By:  ReplicaSet/ubuntu-test-85c8959887
    Containers:
      ubuntu:
        Container ID:   docker://2e856bad136f97be9d464209216d40155b5b34d265ddfc6f6ab159d472f24250
        Image:          ubuntu:latest
        Image ID:       docker-pullable://ubuntu@sha256:669e010b58baf5beb2836b253c1fd5768333f0d1dbcb834f7c07a4dc93f474be
        Port:           <none>
        Host Port:      <none>
        State:          Waiting
          Reason:       CrashLoopBackOff
        Last State:     Terminated
          Reason:       Completed
          Exit Code:    0
          Started:      Sun, 13 Feb 2022 16:43:19 +0530
          Finished:     Sun, 13 Feb 2022 16:43:19 +0530
        Ready:          False
        Restart Count:  2
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-pjgqw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             False
      ContainersReady   False
      PodScheduled      True
    Volumes:
      kube-api-access-pjgqw:
        Type:                    Projected (a volume that contains injected data from multiple sources)
        TokenExpirationSeconds:  3607
        ConfigMapName:           kube-root-ca.crt
        ConfigMapOptional:       <nil>
        DownwardAPI:             true
    QoS Class:                   BestEffort
    Node-Selectors:              <none>
    Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
    Events:
      Type     Reason     Age                From               Message
      ----     ------     ----               ----               -------
      Normal   Scheduled  48s                default-scheduler  Successfully assigned default/ubuntu-test-85c8959887-ztrgg to c1
      Normal   Pulled     43s                kubelet            Successfully pulled image "ubuntu:latest" in 4.008007591s
      Normal   Pulled     39s                kubelet            Successfully pulled image "ubuntu:latest" in 3.972582861s
      Normal   Pulling    24s (x3 over 47s)  kubelet            Pulling image "ubuntu:latest"
      Normal   Created    20s (x3 over 43s)  kubelet            Created container ubuntu
      Normal   Started    20s (x3 over 43s)  kubelet            Started container ubuntu
      Normal   Pulled     20s                kubelet            Successfully pulled image "ubuntu:latest" in 4.036496621s
      Warning  BackOff    6s (x4 over 38s)   kubelet            Back-off restarting failed container
    ```
    * It took me a while to get my head around this, but, I think since it is the nature of a container to close when the task is done, the container creates and shuts itself down (maybe something like "docker run -dit" was needed?), since there were no actions/tasks/commands really specified in the deployment. We will debug this some other day.
    * Lets try running a different container and then access it :
    ```ps
    PS G:\K8s> kubectl.exe create deployment mongodbtest --image=mongo
    deployment.apps/mongodbtest created
    PS G:\K8s> kubectl.exe get pod -w
    NAME                          READY   STATUS              RESTARTS   AGE
    mongodbtest-b955d8c76-4mhqs   0/1     ContainerCreating   0          8s
    mongodbtest-b955d8c76-4mhqs   1/1     Running             0          2m12s
    PS G:\K8s> kubectl.exe exec -it mongodbtest-b955d8c76-4mhqs -- /bin/bash
    root@mongodbtest-b955d8c76-4mhqs:/#
    ```
    > The double dash symbol "--" is used to separate the command you want to run inside the container from the kubectl arguments.
    ```bash
    root@mongodbtest-b955d8c76-4mhqs:/# ls
    bin   data  docker-entrypoint-initdb.d  home        lib    lib64   media  opt   root  sbin  sys  usr
    boot  dev   etc                         js-yaml.js  lib32  libx32  mnt    proc  run   srv   tmp  var
    root@mongodbtest-b955d8c76-4mhqs:/# hostname
    mongodbtest-b955d8c76-4mhqs
    ```
* You can also manually create/delete a deployment and then deploy it using :
  ```ps
  PS G:\K8s> kubectl.exe apply -f deploymentfilename.yaml
  PS G:\K8s> kubectl.exe delete -f deploymentfilename.yaml
  ```
* Similarly kubectl can be used with services, volumnes etc.