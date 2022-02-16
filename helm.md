# Helm :

* Helm is the package manager, used for managing and deploying k8s yaml files. It also resolves dependencies automatically.
* Eg : To deploy a predefined stack of components without installing each one manually.
* A bunch of such yaml files is called Helm Chart, some of them are available at [helmhub.](https://helm.sh/docs/helm/helm_search_hub/)
* You can also create private repos.
* Install : [Minikube](https://github.com/helm/helm/releases)
* Helm works in a client-server model. The server is called Tiller and is deployed in the k8s cluster which will then deploy the yaml files (this is in v2). In help v3 Tiller is removed.
* Typical commands :
  ```ps
  ```
* Helm can also be used to create templates and then the values.yaml can be used to modify the values on the fly.