# TOSCA Component for Prometheus public API

This component contains public definitions related to Prometheus.
Having such types in a separate component allows to import and share it between components without depending on
a concrete implementation.

TOSCA definitions are stored within [types.yaml](types.yaml).

## TOSCA definition

The TOSCA description contains a capability type `prometheus.pub.capabilities.MonitoringEndpoint`.
This type derives from the `tosca.capabilities.Endpoint` capability.
`tosca.capabilities.Endpoint` is the default TOSCA type that should be used or extended to define a network endpoint capability.

The first thing to note is that capability types just like node types can carry some properties and attributes.
This allows to make them configurable in addition of being meaningful.
Within `prometheus.pub.capabilities.MonitoringEndpoint` we redefine some of the properties of the
`tosca.capabilities.Endpoint` capability in order to specify some defaults that match the default properties
of a Prometheus endpoint.

So we redefine defaults for:

* the `port` property to `9100`
* the `url_path` property to `/metrics`
* the `protocol` property to `http`

### Next steps

Then let's see how to we can use this new capability on a node type for the
[Prometheus Exporter](../node-exporter/ansible/README.md)].
