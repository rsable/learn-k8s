# learn-k8s
Notes from when I learnt K8s

## Architecture :
* **Worker Node :**
    * Should have 3 processes on every node :
        * A container runtime : Cri-o, Docker Engine.
        * Kublet : Actual process responsible for starting a pod, assign resources and making sure they are running.
        * Kube Proxy : Is a network proxy used to forward requests etc between services in an optimized manner. The requests could be from inside (pod-to-pod) or outside the cluster.
* **Master Node :**
    * Manage the worker nodes and monitors them.
    * There are 4 process on every master node :
        * Api Server : Interface used to validate, manage, create and configure k8s cluster. It could be GUI, API or CLI (kubectl). *Api server is usually load balanced between multiple Master nodes.*
        * Schedule : *Determines* which node to start a pod on. Kublet will actually start the pod after receiving a request from scheduler.
        * Control Manager : Various control loops that detect state changes, like crashing of pods and then decide on the next action.
        * etcd : This key:value data store is the clusters brain. Every change in the cluster is saved here eg when a new pod is scheduled, or a pod died, the resources available on nodes, cluster health. Using this the queries from Scheduler / Api Server are answered. *[etcd](https://www.ibm.com/cloud/learn/etcd#:~:text=etcd%20is%20an%20open%20source,the%20popular%20container%20orchestration%20platform.) is fully replicated across all the master nodes.* ᕙ(⇀‸↼‶)ᕗ

## Concepts Overview :
* **Node - Pod - Container :**
    * Node could be a baremetal system / virtual host.
    * Pod is an abstraction layer for container i.e. it creates an layer over the container, so that k8s can manage it.
    * There can be multiple pods on a node and there can be multiple containers in one pod. But usually one pod is limited to one container for simplicity and only side-car containers like Prometheus are installed within the same pod.
    * Each pod will have its own IP address, but pods are ephemeral. So we will need a service to manage the IP (╯°□°）╯┻┻
* **[Service &](service.md) [Ingress :](ingress.md)**
    * Service could be an IP address with a DNS name that is attached to a pod.
    * The lifecycle of a service and pod are disconnected, so even if a pod dies the service will continue to work.
    * A service could be internal or external.
    * Service also acts as a load balancer between multiple nodes.
    * Ingress is used to manage external access to services or more specifically route traffic into the cluster.
* **[ConfigMap &](createsecret.md/#creating-a-configmap) [Secret :](createsecret.md)**
    * ConfigMap is an external easily changeable configuration for the application. Could be a URL to something / DB Url / path. Consider this as a Variable ¯\\_(ツ)_/¯
    * Just like ConfigMap, but to save super super secret stuff use Secret. 
    * Both of these are connected to the pod to be used.
* **[Volumes :](ps.md)**
    * To store persistent data.
* **Deployment & StatefulSet :**
    * Deployment is the blueprint for pods.
    * Will have details about pod, and other configurational details like number of replicas (Replicaset) etc.
    * StatefulSet is similar to deployment but deployment can not manage stateful applications like database, which have active I/O and can cause data corruption if run though multiple sources. So StatefulSet manages such applications along with other tasks like replication etc. which are done by Deployment.
* **Operators :**
    * An Operator extends Kubernetes to automate the management of the entire life cycle of a particular application. Operators serve as a packaging mechanism for distributing applications on Kubernetes, and they monitor, maintain, recover, and upgrade the software they deploy.
    * Mostly used for stateful applications.
    * Some great explanation [here](cl-oreilly-kubernetes-operators-ebook-f21452-202001-en_2.pdf) and pre-made operators [here.](https://operatorhub.io/)
    * A new operator can be created using [Operator SDK.](https://sdk.operatorframework.io/)

## Ref :
1. [Understanding the Configuration file.](confiunderstanding.md)
1. [Helm.](helm.md)
1. [Deploying prometheus using an operator.](prometheus.md)
1. [Deploying the first cluster.](firstcluster.md)
