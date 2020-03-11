# oc tool

Official documentation: <https://docs.openshift.com/container-platform/3.11/cli_reference/basic_cli_operations.html>

## OpenShift resources

### OpenShift / Kubernetes resources

Get informations about the OpenShift / Kubernetes resource objects.

Dive into resource description:

1. `oc explain DeploymentConfig`
1. `oc explain DeploymentConfig.spec`
1. `oc explain DeploymentConfig.spec.strategy`

Full description of a DeploymentConfig: `oc explain dc --recursive=true`

JSON schema definitions: <https://github.com/garethr/openshift-json-schema>

### OpenShift API resources

Get informations about the resources used by calling the OpenShift API.

#### get resource types

Get all resource types: `oc api-resources | sort`

#### get OpenShift API objects

Get the definition of a resource. Path: <https://OPENSHIFT_API_URL/apis/VERSION>

Example for the DeploymentConfig (kind: DeploymentConfig, version: apps.openshift.io/v1)

```bash
curl -k -H "Authorization: Bearer $(oc whoami -t)" -H 'Accept: application/yaml' https://OPENSHIFT_API_URL/apis/apps.openshift.io/v1
```

Documentation: <https://docs.openshift.com/container-platform/3.11/rest_api/index.html>

## access two cluster

Login into two cluster at the same time. Or login as two different user.

Do this in two different bash windows and different directories.

```bash
export KUBECONFIG=$(pwd)/.kube
```

## custom columns

```bash
oc get events -o wide -o custom-columns="creation:metadata.creationTimestamp,last:lastTimestamp,count:count,name:metadata.name,type:type,reason:reason,source:source,message:message"
```

get all project names:

```bash
oc get project -o custom-columns=NAME:metadata.name
```

## patch configmap

Patch the configmap with the oc tool.

example configmap

```yaml
apiVersion: v1
data:
  launcher.backend.catalog.filter: ""
...
  launcher.keycloak.client.id: ""
  launcher.keycloak.realm: ""
  launcher.keycloak.url: ""
```

Patch command to update values.

```bash
oc patch configmap launcher -p '{"data":{"launcher.keycloak.client.id":"fabric8-launcher"}}'
oc patch configmap launcher -p '{"data":{"launcher.keycloak.realm":"realm-name"}}'
oc patch configmap launcher -p '{"data":{"launcher.keycloak.url":"https://sso.keycloak.com/auth"}}'
```

## convert json to yaml

Easy way to convert between json and yaml format.

```bash
oc convert -f pod.yaml --local -o json
oc convert -f pod.json --local -o yaml
```

## Pod selector

### manage pod by dc

```bash
oc get pods -l deploymentconfig=mysql
oc delete pods -l deploymentconfig=mysql
```

### rsh by dc

```bash
oc rsh dc/mysql
oc rsh $(oc get pods -o name -l app=my-app)
```

### debug by dc

```bash
oc debug dc/my-app
# same but as root user
oc debug --as-root=true dc/my-app
```
