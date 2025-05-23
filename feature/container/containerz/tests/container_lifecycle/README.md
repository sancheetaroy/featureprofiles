# CNTR-1: Basic container lifecycle via `gnoi.Containerz`.

## Summary

Verify the correct behaviour of `gNOI.Containerz` when operating containers.

## Procedure

* Build the test and upgrade container as described below
* Pass the tarballs of the container the test as arguments.

### Build Test Container

The test container is available in the feature profile repository under
`internal/cntrsrv`.

Start by entering in that directory and running the following commands:

```shell
$ cd internal/cntrsrv
$ go mod vendor
$ CGO_ENABLED=0 go build .
$ docker build -f build/Dockerfile.local -t cntrsrv:latest .
```

At this point you will have a container image build for the test container.

```shell
$ docker images
REPOSITORY  TAG            IMAGE ID       CREATED         SIZE
cntrsrv     latest         8d786a6eebc8   3 minutes ago   21.4MB
```

Now export the container to a tarball.

```shell
$ docker save -o /tmp/cntrsrv.tar cntrsrv:latest
$ docker tag cntrsrv:latest cntrsrv:upgrade
$ docker save -o /tmp/cntrsrv-upgrade.tar cntrsrv:upgrade
$ docker rmi cntrsrv:latest
```

This is the tarball that will be used during tests.

## CNTR-1.1: Deploy and Start a Container

Using the
[`gnoi.Containerz`](https://github.com/openconfig/gnoi/tree/main/containerz) API
(reference implementation to be available
[`openconfig/containerz`](https://github.com/openconfig/containerz), deploy a
container to the DUT. Using `gnoi.Containerz` start the container.

The container should expose a simple health API. The test succeeds if is
possible to connect to the container via the gRPC API to determine its health.

## CNTR-1.2: Retrieve a running container's logs.

Using the container started as part of CNTR-1.1, retrieve the logs from the
container and ensure non-zero contents are returned when using
`gnoi.Containerz.Log`.

## CNTR-1.3: List the running containers on a DUT

Using the container started as part of CNTR-1.1, validate that the container is
included in the listed set of containers when calling `gnoi.Containerz.List`.

## CNTR-1.4: Stop a container running on a DUT.

Using the container started as part of CNTR-1.2, validate that the container can
be stopped, and is subsequently no longer listed in the `gnoi.Containerz.List`
API.

## CNTR-1.5: Create a volume on the DUT.

Validate the the DUT is capable of creating a volume, reading it back
and removing it. 

## CNTR-1.6: Upgrade a container on the DUT.

Using the same container started as part of CNTR-1.1, validate that the container
can be upgraded to the new version of the image identified by a different tag
than the current running container image. 

## OpenConfig Path and RPC Coverage

The below yaml defines the RPCs intended to be covered by this test.

```yaml
rpcs:
  gnoi:
    containerz.Containerz.Deploy:
    containerz.Containerz.StartContainer:
    containerz.Containerz.StopContainer:
    containerz.Containerz.Log:
    containerz.Containerz.ListContainer:
    containerz.Containerz.CreateVolume:
    containerz.Containerz.RemoveVolume:
    containerz.Containerz.ListVolume:
    containerz.Containerz.UpdateContainer:
```
