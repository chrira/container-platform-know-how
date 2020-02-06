# OpenShift builds

## source strategy

```yaml
strategy:
    sourceStrategy:
      from:
        kind: ImageStreamTag
        name: 'nginx-112-centos7:latest'
    type: Source
```

## source input from image stream

```yaml
  source:
    images:
    - as: null
      from:
        kind: ImageStreamTag
        name: source-image-stream:latest
      paths:
      - destinationDir: "."
        sourcePath: "/opt/app-root/src/dist/."
```
