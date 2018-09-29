## ONOS Kubernetes

This project provides a canonical Kubernetes deployment for [ONOS] 1.14 and beyond. The goal of this project is to provide one click deployment of a configurable ONOS+Atomix+Mininet cluster.

## Pod Configuration

To install the ONOS Helm chart, download [Helm] and run `helm install`:

```
helm install charts/onos
```

Atomix uses an anti-affinity policy to ensure cluster state is replicated on distinct hosts.
When running the ONOS chart in Minikube, anti-affinity must be disabled since there's only a single Kubernetes worker node:

```
helm install --set atomix.podAntiAffinity.enabled=false charts/onos
```

The number of ONOS and Atomix nodes are independent of one another. To scale southbound I/O,
increase the number of ONOS nodes by overriding `replicas`, and to scale data storage and fault tolerance, increase the number of Atomix nodes by overriding `atomix.replicas`:

```
helm install --set replicas=5 --set atomix.replicas=3 charts/onos
```

By default, the latest stable official ONOS and Atomix images are used. To override the images, use the `image` and `atomix.image` values:

```
helm install --set image.repository=onosproject/onos --set image.tag=1.14.1 charts/onos
```

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

Helm does not support upgrades of dependency charts. Thus, to upgrade the Atomix cluster you must manually patch the Atomix StatefulSet. First, install the ONOS chart:

```
helm install --set atomix.image.tag=3.0.5 charts/onos
```

Once the chart is ready, patch the Atomix image to upgrade Atomix:

```
kubectl patch statefulset iced-bison-atomix --type='json' -p='[{"op": "replace", "path": "/spec/template/spec/containers/0/image", "value": "atomix/atomix:3.0.6"}]'
```

The Atomix pod disruption budget ensures only a single Atomix node will be down at any given time. Availability for the ONOS cluster will be maintained throughout the upgrade.

[ONOS]: https://onosproject.org
[Helm]: https://helm.sh
