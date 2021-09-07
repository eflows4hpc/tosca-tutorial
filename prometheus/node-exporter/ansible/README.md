# TOSCA Component for Prometheus Node Exporter

This component demonstrates how to model a TOSCA node and how to implement its lifecycle using Ansible playbooks.

[types.yaml](types.yaml) contains the TOSCA definition itself while the [playbooks](playbooks/) directory contains Ansible playbooks that implement the TOSCA node lifecycle.

## What changed since v1?

First we imported the new public types for prometheus that defines the
`prometheus.pub.capabilities.MonitoringEndpoint` capability.

Then we defined a new capability named `metrics_endpoint` on
`prometheus.node-exporter.ansible.nodes.NodeExporter`.

We take profit of this new capability to allow to configure the port on which the prometheus exporter listen
for scrapping connections as well as the http url path.

We implemented this configuration within a new operation `configure`.

What is interesting to note here is that we can retrieves properties from the capability.
In this case instead of using a TOSCA operation in the form `{ get_property: [SELF, property_name]}` we should
write it in the form `{ get_property: [SELF, capability_name, property_name]}`.

### Next steps

Now let's see how we can use this capability in the [Prometheus server](../../containers/ansible/README.md).

## Component description from v1

### TOSCA definition

The TOSCA description contains a node type called `prometheus.node-exporter.ansible.nodes.NodeExporter` that will
represent a Prometheus node exporter application.
This node type is derived from the `tosca.nodes.SoftwareComponent`. Basically this means that this node should
be hosted on a `tosca.nodes.Compute` node.

#### Imports

First it is critical to understand the import section. Imports allows to use TOSCA types that are defined in
other TOSCA components. This section uses a notation that is not supported by the TOSCA specification but
which allows to reference a TOSCA component by its name and version in the Alien4Cloud catalog.

You will almost always want to import the `tosca-normative-types` component as it contains base TOSCA types that
are widely used. As we will see later, this component implements its operations using Ansible playbooks, this kind of operations types are defined within the `yorc-types`.

#### Properties

This node type defines some properties. In TOSCA properties are variables that could be modified and resolved prior to the application deployment. For example the `download_url`  property could be modified to provide an alternative download URL for the node exporter binary.

#### Attributes

At contrary a TOSCA attribute is a variable that could only be resolved at runtime. A typical example of attribute is the `ip_address` of a `tosca.nodes.Compute` node. We will see how to use attributes later in this tutorial.

#### Lifecycle operations

Then `prometheus.node-exporter.ansible.nodes.NodeExporter` defines some operations. Those operations are part of the TOSCA standard lifecycle interface definition.
The TOSCA specification defines that the following operations are supported and should be called in the following order:

* `create`: This operation is generally used to download binaries and install them on a target host.
* `configure`: This operation is generally used to alter the installed application configuration files according to the properties defined in the component.
* `start`: Finally this operation is used to actually start the application.

Then when undeploying an application:

* `stop`: This operation is used to stop a running application.
* `delete`: This operation is generally used to perform a cleanup of the target host like removing installed binaries and configuration files.

### Lifecycle implementation

The Yorc orchestrator supports different implementation artifacts for lifecycle operations.
Basically implementations could be defined as:

* Shell scripts: in this case the file extension suffix should be `.sh`
* Python scripts: in this case the file extension suffix should be `.py`
* Ansible playbooks: in this case the file extension suffix should be either `.yml` or `.yaml`

We recommend to use the Ansible playbooks as they are the most flexible, easy to use and have better performances.

Your playbooks can import roles. But in this tutorial for the sake of simplicity and to demonstrate how to
handle playbooks we will not use roles.

### Next steps

Then let's see how to inherit from an existing TOSCA component that manages docker containers to deploy
a [Grafana server](../../../grafana/containers/ansible/README.md)] by simply defining some new properties.
