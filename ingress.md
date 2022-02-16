# Ingress :

* Apart from configuration ingress will also need an ingress controller.
* Controller is basically another pod. It will be the actual entry point and will also manage the redirection.
* Multiple paths can also be defined. eg : my-app.com/test and my-app.com/test2 can point to two different services. Or this can also be used as a subdomain eg: test.my-app.com and test2.my-app.com.