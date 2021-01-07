# Dockerfile

## Change base image by argument

Sometime you need to change the base image at build time. For example to use an non-enterprise image for testing purposes.
This can easily be done by using arguments.

### Understand how ARG and FROM interact

Excerpt from [docs.docker.com](https://docs.docker.com/engine/reference/builder/#understand-how-arg-and-from-interact)

`FROM` instructions support variables that are declared by any `ARG` instructions that occur before the first `FROM`.

```Dockerfile
ARG  CODE_VERSION=latest
FROM base:${CODE_VERSION}
CMD  /code/run-app

FROM extras:${CODE_VERSION}
CMD  /code/run-extras
```

An `ARG` declared before a `FROM` is outside of a build stage, so it can’t be used in any instruction after a `FROM`.
To use the default value of an `ARG` declared before the first `FROM` use an `ARG` instruction without a value inside of a build stage:

```Dockerfile
ARG VERSION=latest
FROM busybox:$VERSION
ARG VERSION
RUN echo $VERSION > image_version
```
