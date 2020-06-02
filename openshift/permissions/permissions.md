# permissions and roles

## Viewing Roles and Users for a Project

All possible roles

```bash
oc describe clusterrole
oc get rolebindings
```

Documentation:

* <https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html>
* <https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html>
* <https://docs.openshift.com/container-platform/3.3/admin_solutions/user_role_mgmt.html#view-roles-users-project>

## local roles

```bash
oc create role NAME --verb=VERB --resource=RESOURCE -n PROJECT
oc create role podview --verb=get --resource=pod -n blue
```

Documentation:

* <https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#creating-local-role>

## create exec role

```bash
oc create role podexec --verb=create --resource=pods/exec -n user2-develop
```

## add exec role to user2

```bash
oc adm policy add-role-to-user podexec user3 --role-namespace=user2-develop -n user2-develop
```

## examples

### copy data into pod / pv

Allow the user only access to the pod.

### copy permissions

view access to project

```bash
oc policy add-role-to-user view user3 -n user2-develop
```

#### copy commands

```bash
oc cp test.txt -n user2-develop mysql-1-2v7rp:/tmp/

oc rsync . -n user2-develop mysql-1-2v7rp:/var/lib/mysql/data/tmp
```
