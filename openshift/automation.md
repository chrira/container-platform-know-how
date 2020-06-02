# automation

OpenShift automation by build servers.

## CLI

Automation can be done using the OpenShfit CLI, short oc tool. See the official [documentation](https://docs.openshift.com/container-platform/latest/cli_reference/openshift_cli/getting-started-cli.html).

More hints and tipps for the oc tool: [oc-tool.md](oc-tool.md)

## independency

It is crucial to be independent between the jobs / builds.

### configuration location

The default location for the oc configuration is `$HOME/.kube/`

When all jobs are executed with the same user, this will possibly effect other jobs.

Logging into a OpenShift cluster or changing the project will modify that configuration.

#### configure local oc configuration location

Define the oc configuration location to be inside the workspace.

The oc tool is based on the Kubernetes kubectl.
This is the reason because that the environment variable name is *KUBECONFIG*.

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
