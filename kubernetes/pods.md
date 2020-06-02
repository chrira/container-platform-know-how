# Pods

[Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/) are the smallest deployable units of computing that can be created and managed in Kubernetes.

Description and examples inspired by <https://kubernetes.io/>.

## Container run command with env vars

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

## Container run command with secret values

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
