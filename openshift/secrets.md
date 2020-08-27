# Secrets

Secret values are obfuscated by applying base64 encoding.

## Create Secrets

### Create with oc Tool

Create key/value pair secrets:

```bash
oc create secret generic db-secrets \
  --from-literal DB_USERNAME=db-user \
  --from-literal DB_PASSWORD=asdf1234
```

Check created secret resource:

```bash
$ oc get secret db-secrets -o yaml
apiVersion: v1
data:
  DB_PASSWORD: YXNkZjEyMzQ=
  DB_USERNAME: ZGItdXNlcg==
kind: Secret
metadata:
  name: db-secrets
type: Opaque
```

### Create by resource-yaml / template

Use `stringData` section to let Openshift make the base64 conversion.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: test-secret
type: Opaque
data:
  username: dmFsdWUtMQ0K
  password: dmFsdWUtMg0KDQo=
stringData:
  hostname: myapp.mydomain.com
```

Create: `oc apply -f filename.yaml`

resulting secret

```yaml
apiVersion: v1
data:
  hostname: bXlhcHAubXlkb21haW4uY29t
  password: dmFsdWUtMg0KDQo=
  username: dmFsdWUtMQ0K
kind: Secret
metadata:
  name: test-secret
type: Opaque
```

username and password are unchanged from the input yaml. The hostname is moved to the data section and the value is base64.

### Create from file

Secrets from file are also supported. They have to be mounted similar to the file based configmaps.

```bash
oc create secret generic webdav-users --from-file .htdigest=webdav.htdigest
oc set volume dc/website --add --secret-name webdav-users --mount-path=/opt/app-root/secrets/webdav
```
