## ONOS Kubernetes

This project provides a canonical Kubernetes deployment for [ONOS] 1.14 and beyond. The goal of this project is to provide one click deployment of a configurable ONOS+Atomix+Mininet cluster.

## Pod Configuration

To install the ONOS Helm chart, download [Helm] and run `helm install`:

```
helm install charts/onos
```

When running Helm on Minikube, you must disable anti-affinity for the Atomix chart:

```
helm install --set atomix.podAntiAffinity.enabled=false charts/onos
```

The number of ONOS and Atomix nodes can be configured by overriding `replicas` and `atomix.replicas` respectively:

```
helm install --set replicas=5 --set atomix.replicas=3 charts/onos
```

To set the Docker image used by ONOS, override the `image` values:

```
helm install --set image.repository=onosproject/onos --set image.tag=1.14.1 charts/onos
```

Similarly, override the Atomix image via `atomix.image` values:

```
helm install --set atomix.image.tag=3.0.6 charts/onos
```

To set the resource requests for ONOS containers, override the `resources.requests` values:

```
helm install --set resources.requests.cpu=2 --set resources.requests.memory=4Gi charts/onos
```

The same goes for Atomix:

```
helm install --set atomix.resources.requests.cpu=2 --set atomix.resources.requests.memory=4Gi charts/onos
```

To set the Atomix persistence configuration, override `atomix.persistence` values:

```
helm install --set atomix.persistence.size=2Gi --set atomix.persistence.storageClass=local-storage charts/onos
```

We strongly recommend using the `local-storage` storage class.

### Application Configuration

To activate ONOS applications at startup, override the `apps` value:

```
helm install --set apps={openflow, mcast} charts/onos
```

To customize the Atomix configuration, override `atomix.config` and provide
a custom configuration in either JSON or HOCON format:

```
helm install --set atomix.config="partitionGroups.raft {type: raft, partitions: 7}" charts/onos
```

To change the heap size for either ONOS or Atomix, override `heap` or `atomix.heap` respectively:

```
helm install --set heap=4G charts/onos
```

### Upgrading Atomix

To upgrade the Atomix cluster, run `helm upgrade` and update the `atomix.image.tag`:

```
helm upgrade --set atomix.image.tag=3.0.6 charts/onos
```

The Atomix pod disruption budget ensures only a single Atomix node will be down at any given time. Availability for the ONOS cluster will be maintained throughout the upgrade.

[ONOS]: https://onosproject.org
[Helm]: https://helm.sh
