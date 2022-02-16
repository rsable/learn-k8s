# Services

* Types of services : ClusterIP, Headless, LoadBalancer, NodePort.
* When a service is created k8s creates an endpoint object from it, this object is used to keep track of the members of the service.
    ```ps
    PS C:\Windows\System32> kubectl.exe get endpoints
    NAME         ENDPOINTS           AGE
    kubernetes   172.31.10.42:8443   3d21h
    ```
* **ClusterIP :**
    * Default service.
    * Used to communicate between two pods, since the pod IP addresses are ephemeral.
    * Eg : Domain -> Ingress -> Service (has ClusterIP) -> Pod -> DB Service (has ClusterIP) -> DB Pod
    * All of this happens based on [labels and selectors.](confiunderstanding.md/#labels-and-selectors)
* **Headless :**
    * In case we want to talk to pods directly.
    * Eg : Pod -> Pod (instead of Pod -> Service -> Pod like above)
    * Or directly from clients -> pod.
    * We can do this by setting clusterIP: None in the service yaml.
    * It is usually suggested to have a headless service along side a clusterIP service.
* **NodePort :**
    * Makes the service directly accessible on a node port.
* **LoadBalancer :**
    * Service becomes accessible externally.
 
