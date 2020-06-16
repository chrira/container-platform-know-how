# service account

Service accounts are used to grant access to OpenShift resources.

They use Tokens stored in secrets.

## create service account

Create service account with name *jenkins*.

```bash
oc create serviceaccount jenkins
```

### get access token

Get the access token of the jenkins service account. This will be used from the build server.

```bash
oc serviceaccounts get-token jenkins
```

## grant access to own project

Give jenkins service account *edit* rights ...

to the actual project:

```bash
oc policy add-role-to-user edit system:serviceaccount:$(oc project --short):jenkins
```

to any project (change PROJECT-NAME to the name of the project):

```bash
oc policy add-role-to-user edit system:serviceaccount:PROJECT-NAME:jenkins
```
