# Understanding the configuration file

* Start by defining :
    * apiVersion - Which version of the Kubernetes API you're using to create this object
    * kind - What kind of object you want to create deployment, service, etc.
* Further a configuration file is divided into 3 parts :
    1. Metadata : Name, label, etc.
    1. Specification : Configurations details, replicas, template etc.
    1. Status : This is created and added by k8s to verify if the desired state == actual state, and take actions accordingly. This information is obtained from etcd.
    * The *template* section in *specification* is like a configuration file within configuration file. It will also have metadata and specification, which apply to the pods. It will have details on which image to use, ports to open etc.
    * Labels and Selectors :
        * Metadata has labels.
        * Specification has selectors.
        * In simple terms when a pod is created it is given a *label,(app:myapp)* and when something else, lets say a service, has to look for this pod, the *selector* section of the service will have mentioned a *selector,(app:myapp)* to create the connection with the pod.
    * Each service and pod both will also have its own ports which can be used to access them. 
        * port is the port to access the service.
        * targetPort is the port of the pod (in service config)and containerPort (in deployment). targetPort shouldbe == containerPort.