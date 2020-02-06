# Templates

[OpenShift Templates](https://docs.openshift.com/container-platform/4.2/openshift_images/using-templates.html)
provide a nice way to define your application deployment and use it many times.

## Parameters

Following example contains two parameter:

```yaml
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: example-spring-boot
objects:
- apiVersion: v1
  kind: DeploymentConfig
  metadata:
    creationTimestamp: null
    labels:
      app: example-spring-boot
    name: example-spring-boot
  spec:
    replicas: ${REPLICA_COUNT}
...
    template:
      spec:
        containers:
        - env:
          - name: MY_NAME
            value: ${CUSTOMER_NAME}
....
parameters:
- description: number of pods / replicas
  displayName: number of pods
  name: REPLICA_COUNT
  required: true
  value: '1'
- description: CUSTOMER_NAME environment variable
  displayName: customer name
  name: CUSTOMER_NAME
  required: true
```

Parameter placeholder definition inside the resources: `${PARAMETER-NAME}`

They need the corresponding parameter inside the *parameters* section of the template.

> :warning: The normal placeholder definition `${PARAMETER-NAME}` does only work for *CUSTOMER_NAME*.
> Read further to see the solution.

> :info: Template borrowed from [Spring Boot APPUiO Example](https://github.com/appuio/example-spring-boot-helloworld) and adapted to my needs.

### Parametrize numeric values

Parametrize values like port numbers or replica count can cause problems with the template.

Therefore the parameter placeholder definition is slightly different.
The parameter has to be embedded inside a second pair of curly braces: `${{PARAMETER-NAME}}`

This updated Template snippet has the correct definition for the replicas count.

```yaml
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: example-spring-boot
objects:
- apiVersion: v1
  kind: DeploymentConfig
  metadata:
    creationTimestamp: null
    labels:
      app: example-spring-boot
    name: example-spring-boot
  spec:
    replicas: ${{REPLICA_COUNT}}
...
    template:
      spec:
        containers:
        - env:
          - name: MY_NAME
            value: ${CUSTOMER_NAME}
...
```

## process template

Processing a template means replacing the parameters.
A valid object list will result that can be used to create the resources in OpenShift.
There will not be applied any changes to OpenShift.

show all parameters:

```bash
$ oc process -f openshift/templates/config/example-template.yaml --parameters
NAME                 DESCRIPTION                                                                                     GENERATOR           VALUE
REPLICA_COUNT        number of pods / replicas                                                                                           1
PORT                 port number of database                                                                                             8080
CUSTOMER_NAME        CUSTOMER_NAME environment variable
APPLICATION_DOMAIN   The exposed hostname that will route to the service, if left blank a value will be defaulted. 
```

process the template to see the final resource definition:

```bash
oc process -f openshift/templates/config/example-template.yaml -o yaml
```

Note: this will fail because of missing mandatory parameter

### create OpenShift resources

Use `oc apply` to create the resources in OpenShift.

#### default values

create resources without default values:

```bash
oc process -f openshift/templates/config/example-template.yaml \
  | oc apply -f -
```

command with output:

```bash
$ oc process -f openshift/templates/config/example-template.yaml   | oc apply -f -
imagestream.image.openshift.io/example-spring-boot configured
deploymentconfig.apps.openshift.io/example-spring-boot configured
route.route.openshift.io/example-spring-boot configured
service/example-spring-boot configured
```

#### custom parameter values

define parameter values with command line values:

```bash
oc process -f openshift/templates/config/example-template.yaml \
  -p CUSTOMER_NAME='chrira' \
  -p REPLICA_COUNT='2' \
  -p PORT='8088' \
  | oc apply -f -
```

define parameter values by [file](config/environment.yaml):

```bash
oc process -f openshift/templates/config/example-template.yaml \
  --param-file openshift/templates/config/environment.yaml \
  | oc apply -f -
```

## error cases

Possible error cases.

### Required value

```bash
$ oc process -f openshift/templates/config/example-template.yaml | oc apply -f -
error: unable to process template
  Required value: template.parameters[2]: parameter CUSTOMER_NAME is required and must be specified
error: no objects passed to apply
```

You did not provide all required parameter. -> Provide the parameter with name: *CUSTOMER_NAME*

### unknown parameter name

```bash
$ oc process -f openshift/templates/config/example-template.yaml -p VALUE='42' | oc apply -f -
error: unknown parameter name "VALUE"
error: no objects passed to apply
```

You passed a parameter too much. It is not defined inside the template. -> Check your parameter or parameter file.

### unrecognized type: int32

Problems with numeric types like int32, int64, ...

```bash
Error from server: unrecognized type: int32
```

To solve this issues, see [Parametrize numeric values](#parametrize-numeric-values).
