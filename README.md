## ONOS Kubernetes

This project provides a canonical Kubernetes deployment for ONOS 1.14 and beyond. The technologies used in this project are:
* Docker
* Kubernetes
* Helm

The goals of this project are to support:
* Managed Atomix cluster
* Managed ONOS cluster
* Load balancing for ONOS controller nodes
* Autoscaling of ONOS controller nodes
* Mininet for testing

### Building Docker images

To build the ONOS Kubernetes images, run `docker build` with the Dockerfile directory:

```
docker build -t onos/onos-kubernetes:1.14.0 docker/onos
```

The Docker images are built directly from source by pulling from a git repository. You can specify the repository and branch from which to build the images with the `REPO` and `BRANCH` arguments:

```
docker build -t onos/onos-kubernetes:1.14.0 docker/onos --build-arg REPO=https://github.com/kuujo/onos.git --build-arg BRANCH=dns-config
```

### Running Atomix

To run a standalone Atomix cluster, install the Atomix Helm chart:

```
helm install charts/atomix
```

By default, the Atomix cluster will be configured with three nodes in a stateful set. To configure the number of Atomix nodes, set the `atomix.replicas` value:

```
helm install --set atomix.replicas=5 charts/atomix
```

Once the chart is installed, you can delete the chart by running `helm delete` with the chart name:

```
helm delete lunging-toucan
```

#### Using custom images

To run Atomix with a custom image, override the image values:
* `image.repository` - the Docker repository from which to pull the image
* `image.tag` - the image tag to pull
* `image.pullPolicy` - the image pull policy, use `Never` to use a local image
* `image.pullSecrets` - a list of secret names

### Running ONOS

To run an ONOS cluster, install the ONOS Helm chart:

```
> helm install charts/onos
```

By default, the ONOS cluster will be configured with three replicas and expect to connect to a three-node Atomix service. To change the number of replicas, override the `onos.replicas` value:

```
helm install --set onos.replicas=5 charts/onos
```

When deployed separately, ONOS requires the name of the Atomix service to which to connect for internal service/configuration discovery and replication. To configure the Atomix service name, override the `atomix.service` value:

```
helm install --set atomix.service=my-atomix-service charts/onos
```

#### Using custom images

To run ONOS with a custom image, override the image values:
* `image.repository` - the Docker repository from which to pull the image
* `image.tag` - the image tag to pull
* `image.pullPolicy` - the image pull policy, use `Never` to use a local image
* `image.pullSecrets` - a list of secret names

#### Autoscaling

The ONOS deployment supports autoscaling. To enable autoscaling, use the `autoscaling.enabled` value:

```
helm install --set autoscaling.enabled=true charts/onos
```

### Running Mininet

To run a Mininet node, install the Mininet Helm chart:

```
> helm install charts/mininet
```
