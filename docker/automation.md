# automation

Docker automation by build servers.

## independency

It is crucial to be independent between the jobs / builds.

### docker configuration

The default location for the docker configuration is `$HOME/.docker/config.json`

When all jobs are executed with the same user, this will possibly effect other jobs.

Logging into a Docker Image Registry will modify that configuration file.

#### configure local docker configuration location

Define the docker configuration location to be inside the workspace.

Environment example:

```bash
export DOCKER_CONFIG="${pwd()}/.docker"
```

Jenkins pipeline example:

```Groovy
pipeline {
    environment {
        DOCKER_CONFIG="${pwd()}/.docker"
    }
    ...
}
```
