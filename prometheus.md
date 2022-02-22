# Deploying prometheus using an Operator :

* Prometheus is a tool which can monitor, alert and preemptively identify events based on logic. Eg : monitor / alert issues like spike in memory usage etc.
* All the alerts are sent thought the *Alertmanager*.
* Primarily made up of three components :
    * Retrieval : Pulls metrics from various targets (i.e. sources, eg: application, servers (linux/windows), containers, services) via unit (eg: cpu, memory, application requests etc.). The targets expose the metrics via http. Some services by default expose the metrics, if not an *exporter* is used to pull them form the source and expose them.
    * Storage : Stores metrics.
    * HTTP Server : Works as front end and to query and display metrics via PromQL or tools like Grafana.
* Deploy :
    ```ps
    PS G:\K8s\helm> helm repo add stable https://charts.helm.sh/stable
    "stable" has been added to your repositories
    PS G:\K8s\helm> helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    "prometheus-community" has been added to your repositories
    PS G:\K8s\helm> helm repo update
    Hang tight while we grab the latest from your chart repositories...
    ...Successfully got an update from the "prometheus-community" chart repository
    ...Successfully got an update from the "stable" chart repository
    Update Complete. ⎈Happy Helming!⎈
    PS G:\K8s\helm> helm install prometheus prometheus-community/kube-prometheus-stack
    NAME: prometheus
    LAST DEPLOYED: Mon Feb 21 00:00:18 2022
    NAMESPACE: default
    STATUS: deployed
    REVISION: 1
    NOTES:
    kube-prometheus-stack has been installed. Check its status by running:
      kubectl --namespace default get pods -l "release=prometheus"

    Visit https://github.com/prometheus-operator/kube-prometheus for instructions on how to create & configure Alertmanager and Prometheus instances using the Operator.
    PS G:\K8s\helm> kubectl get pod -w
    PS G:\K8s\helm> kubectl get pod -w
    NAME                                                     READY   STATUS    RESTARTS   AGE
    alertmanager-prometheus-kube-prometheus-alertmanager-0   2/2     Running   0          108s
    prometheus-grafana-6cfff89b66-pqd4t                      3/3     Running   0          2m17s
    prometheus-kube-prometheus-operator-6fc5c7cf89-6bw8x     1/1     Running   0          2m17s
    prometheus-kube-state-metrics-64df7c8675-7q8cb           1/1     Running   0          2m17s
    prometheus-prometheus-kube-prometheus-prometheus-0       2/2     Running   0          108s
    prometheus-prometheus-node-exporter-gm76n                1/1     Running   0          2m17s
    PS G:\K8s\learn-k8s> kubectl get secret --namespace default prometheus-grafana -o jsonpath="{.data.admin-password}"
    cHJvbS1vcGVyYXRvcg==
    PS G:\K8s\learn-k8s> kubectl.exe port-forward deployment/prometheus-grafana 3000
    Forwarding from 127.0.0.1:3000 -> 3000
    Forwarding from [::1]:3000 -> 3000
    Handling connection for 3000
    ```