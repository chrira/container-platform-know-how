# automation

Kubernetes automation by build servers.

## CLI

Automation can be done using the Kubernetes CLI, the kubectl. See the official [documentation](https://kubernetes.io/docs/reference/kubectl/overview/).

## independency

It is crucial to be independent between the jobs / builds.

### configuration location

The default location for the kubectl configuration is `$HOME/.kube/`

When all jobs are executed with the same user, this will possibly effect other jobs.

Logging into a Kubernetes cluster can modify that configuration.

#### configure local kubectl configuration location

Define the kubectl configuration location to be inside the workspace.

The environment variable name is *KUBECONFIG*.

Environment example:

```bash
export KUBECONFIG=$(pwd)/.kube
```

Jenkins pipeline example:

```Groovy
pipeline {
    environment {
        KUBECONFIG="${pwd()}/.kube"
    }
    ...
}
```
