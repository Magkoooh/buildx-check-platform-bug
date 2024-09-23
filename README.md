# Steps to Reproduce:

1. Build `projectA` using a platform that is not your native platform. For example on an Apple M1: `docker buildx build --platform=linux/amd64 -t test/project-a:0.1 ./projectA`
2. Run a build check on `projectB`: `docker buildx build --check --platform=linux/amd64 -t test/project-b:0.1 ./projectB`
3. Run a build check on `projectB` using env to set platform: `DOCKER_DEFAULT_PLATFORM=linux/amd64 docker buildx build --check -t test/project-b:0.1 ./projectB`

## Expecation:

The build check should pass.

## Actual:

The build check fails both times with the following error:

```
[+] Building 0.0s (3/3) FINISHED                                                                                                                                                                 docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                             0.0s
 => => transferring dockerfile: 91B                                                                                                                                                                              0.0s
 => ERROR [internal] load metadata for docker.io/test/project-a:0.1                                                                                                                                              0.0s
 => Verifying build result                                                                                                                                                                                       0.0s
 => WARN: Requested platform "linux/amd64" does not match result platform "linux/arm64"                                                                                                                          0.0s
------
 > [internal] load metadata for docker.io/test/project-a:0.1:
------

 1 warning found (use docker --debug to expand):
 - Requested platform "linux/amd64" does not match result platform "linux/arm64"
ERROR: test/project-a:0.1: failed to resolve source metadata for docker.io/test/project-a:0.1: no match for platform in manifest: not found
Dockerfile:1
--------------------
   1 | >>> FROM test/project-a:0.1
   2 |     COPY example.txt example2.txt
   3 |
--------------------
```

## Note:

Building `projectB` works as expected without the `--check` flag:

1. `docker buildx build --platform=linux/amd64 -t test/project-b:0.1 ./projectB`
2. `DOCKER_DEFAULT_PLATFORM=linux/amd64 docker buildx build --check -t test/project-b:0.1 ./projectB`
