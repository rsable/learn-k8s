# Creating a Secret file

* A secret file is always stored in k8s.
* Do not add it to any repository.
* A secret can be created in multiple types, eg : Opaque, or with TLS, etc.
* The actual data in a secret should always be base64 encoded, eg :
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
        name: my-secret
    type: Opaque
    data:
        my-secret-username: dXNlcm5hbWU=
        my-secret-password: cGFzc3dvcmQ=
    ```
* Always create the secret before referencing it anywhere or the creation will fail.
    ```ps
    PS G:\K8s\learn-k8s> kubectl.exe apply -f secret.yaml
    ```
* To reference it from a deployment :
    ```yaml
    - name: VARIABLE_FOR_USERNAME_IN_PROGRAM_TO_DEPLOY
      valueFrom:
        secretKeyRef:
            name: my-secret
            key: my-secret-username
    - name: VARIABLE_FOR_PASSWORD_IN_PROGRAM_TO_DEPLOY
      valueFrom:
        secretKeyRef:
            name: my-secret
            key: my-secret-password
    ```

# Creating a ConfigMap

* ConfigMap will be created and referenced exactly like Secret.
* Just change the **kind** in the config file to *ConfigMap* and when referencing it use **configKeyRef**
* Again here the order of deployment is important. Always deploy the config before anything that may reference the config.