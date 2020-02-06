# oc tool

Official documentation: <https://docs.openshift.com/container-platform/3.11/cli_reference/basic_cli_operations.html>

## get resource types

Get all resource types: `oc api-resources | sort`

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
