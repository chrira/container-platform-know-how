# Pods

[Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/) are the smallest deployable units of computing that can be created and managed in Kubernetes.

Description and examples inspired by <https://kubernetes.io/>.

## Container run command

### Container run command with env vars

How to use environment variables inside the container run command?

Environment variable name has to be put inside `$()`.

Example for env variable *ENV_VAR_NAME*: `$(ENV_VAR_NAME)`

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: command-demo
spec:
  containers:
  - name: command-demo-container
    image: debian
    env:
    - name: MESSAGE
      value: "hello world"
    command: ["/bin/echo"]
    args: ["$(MESSAGE)"]
```

[Example definition reference](https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/#use-environment-variables-to-define-arguments)

### Container run command with secret values

This works the same as the above example. Just define an environment variable to be set from a secret.

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: command-demo
spec:
  containers:
  - name: command-demo-container
    image: debian
    env:
    - name: SECRET_MESSAGE
      valueFrom:
        secretKeyRef:
        name: secret-name
        key: message
    command: ["/bin/echo"]
    args: ["$(SECRET_MESSAGE)"]
```

[Example definition reference](https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/#use-environment-variables-to-define-arguments)

## Container Environment

### Set Environment From Secret

Add [Secrets](../openshift/secrets.md) to the environment of the deployment configuration.

```bash
oc set env dc/hello-world --from secret/db-secrets
```

check application configuration:

```bash
$ oc set env dc/hello-world --list
# deploymentconfigs hello-world, container hello-world
# DATABASE_PASSWORD from secret db-secrets, key DATABASE_PASSWORD
# DATABASE_USERNAME from secret db-secrets, key DATABASE_USERNAME
```

### Single env Variable from Secret

Env. variable from [Secret](../openshift/secrets.md)

```yaml
containers:
  - env:
    - name: MYAPP_SECRET_TOKEN
      valueFrom:
        secretKeyRef:
          key: SECRET_TOKEN
          name: secret-token-for-my-app
```

## Container File Replacements

### Mount ConfigMaps

Create ConfigMap from file:

```bash
oc create configmap my-configmap --from-file=./config/application.xml
```

Define volume from ConfigMap:

```yaml
    spec:
      containers:
        ...
      volumes:
        - name: my-configmap-volume
          configMap:
            name: my-configmap
```

#### Mount folder

This will overwrite the whole folder inside the container:

```yaml
    spec:
      containers:
      - name: my-container
        ...
        volumeMounts:
        - name: my-configmap-volume
          mountPath: /opt/app/config
```

> The folder `/opt/app/config` will hold all files from the ConfigMap.

#### Mount single file

This will mount one file into a folder:

```yaml
    spec:
      containers:
      - name: my-container
        ...
        volumeMounts:
        - name: my-configmap-volume
          mountPath: /opt/app/config/application.xml
          subPath: application.xml
```

> The file is read only!

### Mount Secrets

#### Full Customized Secret Mount

There is a [Secret](../openshift/secrets.md) with two files in it.

creation:

```bash
oc create secret generic certificates --from-file=tls.key=/path/to/key.file --from-file=tls.crt=/path/to/cert.file
```

content:

```yaml
apiVersion: v1
data:
  tls.crt: "bliblablu"
  tls.key: "its42"
kind: Secret
metadata:
  name: certificates
type: Opaque
```

I want to mount the files sperate and change the file names:

* tls.key -> /etc/ssl/private/ssl-cert.key
* tls.crt -> /etc/ssl/certs/ssl-cert.pem

The volumeMounts do the mounting of single files inside the desired directories.

The volumes are of typ projected. They do the magic of taking single files from a secret and changing the filename.

```yaml
spec:
  containers:
      volumeMounts:
        - mountPath: /etc/ssl/certs/ssl-cert.pem
          name: volmount-ssl-cert
          subPath: ssl-cert.pem
        - mountPath: /etc/ssl/private/ssl-cert.key
          name: volmount-ssl-key
          subPath: ssl-cert.key
  volumes:
    - name: volmount-ssl-cert
      projected:
        sources:
          - secret:
              items:
                - key: tls.crt
                  path: ssl-cert.pem
              name: certificates
    - name: volmount-ssl-key
      projected:
        sources:
          - secret:
              items:
                - key: tls.key
                  path: ssl-cert.key
              name: certificates
```
