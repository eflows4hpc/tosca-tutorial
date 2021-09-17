# TOSCA Component for Prometheus server

This component deep dives into inheritance of TOSCA node types by extending types with operations.

[types.yaml](types.yaml) contains the TOSCA definition itself while the [playbooks](playbooks/) directory contains Ansible playbooks that implement the TOSCA node lifecycle.

## What changed since v1?

First we imported the `prometheus.pub` component in order to have access to the
`prometheus.pub.capabilities.MonitoringEndpoint` capability.

Then we defined a new `requirements` section in the `prometheus.containers.ansible.nodes.Prometheus`
node type. This section allows to bind this component to a capability of another component.

We called this requirement `targets` as it is designed to handle how we connect Prometheus
to the targets it should scrap.

In this requirement definition we specify that the target capability of this requirement is a
`prometheus.pub.capabilities.MonitoringEndpoint`.

We also specify through the `occurrences` property that this requirement is optional
and we can use this requirement to bind to an infinity of other components (
noted `occurrences: [0, UNBOUNDED]`).

The final part of the requirement definition is the `relationship` property.
A `relationship` adds meaning and a lifecycle to the relation between the two nodes.
For instances the `tosca.relationships.HostedOn` and `tosca.relationships.ConnectsTo`
adds a well known meaning that the component that is *hosted on* or *connects to* another component
should wait for the other component to be ready/started before trying to execute its
lifecycle.

In our case we used a new relationship named `prometheus.containers.ansible.relationships.TargetsTo`
specific to this implementation and defined in another section of the file.

## Component description from v1

### TOSCA definition

The TOSCA description contains a node type called `prometheus.containers.ansible.nodes.Prometheus` that will
represent a Docker container running a Prometheus server.
Just like for the Grafana server in the previous tutorial section, this node type is derived from the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer`.

A `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` component will handle the Docker container lifecycle,
by using its defined properties to create and run a Docker container on top of a Docker engine.
That's why there is no playbooks or scripts for this component.

We need to redefine some properties of the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type as we expect them to be to run a Prometheus server.

The implementation of the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type can be found within the [Ystia Forge](https://github.com/ystia/forge/blob/develop/org/ystia/docker/containers/generic/types.yml).

But unlike the Grafana server, Prometheus needs a configuration file to manage its targets on which it should
collect metrics. So in our implementation we need both to generate configuration file and to mount it into
the container.

Beside of that if we want to dynamically add targets to the Prometheus server, we need to update this file and
send a `KILLUP` signal to the container to reload the configuration. Those two operations are handled by defining
*custom commands*.

#### Properties

This node type redefines the following properties of the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type:

* `image`: is set by default to `prom/prometheus:latest` to target a Grafana server Docker image
* `restart_policy`: is set by default to `always` to restart the container on any error or on system restart
* `published_ports`: is set by default to `["3000:3000"]` to expose the Grafana server on port 3000
* `volumes`: is set by default to `["/var/lib/prometheus/:/etc/prometheus/"]` to mount the `/var/lib/prometheus/`
  directory into `/etc/prometheus/` within the container

In addition we define a new property specific to the Prometheus server `configuration_dir` that will reference a
path to a directory on the host used to store the Prometheus configuration files. By default this property is
set to `/var/lib/prometheus/`.

#### Attributes

We do not define any attributes for this node type. The attributes are defined in the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type.

#### Operations

It is possible to combine operations from a parent and a child node type.
The only limitation is that if a child type redefines an operation already defined in the parent type,
only the child operation will be executed.

##### Standard operations

The lifecycle of this component remain the standard normative TOSCA lifecycle. The `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type implements the following operations:

* `create`: to create a new container
* `start`: to start it
* `stop`: to stop it
* `delete`: to delete it

See [the `GenericContainer` component on Ystia Forge](https://github.com/ystia/forge/blob/develop/org/ystia/docker/containers/generic/types.yml) for more details.

In addition this component adds a `configure` operation to generate the configuration file to be mounted into the container. As per the standard normative TOSCA lifecycle this operation is called after the `create` and before the `start` operations.

##### Custom operations

In addition to the standard lifecycle operations this component implements the following custom operations:

* `insert_target`: to add a new target to the Prometheus configuration file
* `reload_config`: to send a `KILLUP` signal to the Prometheus server to notify it to reload its configuration

### Next steps

Until now we only defined types that are use to manage the lifecycle of services, meaning that are installed, configured and started. They are kept started until we request them to stop, typically when we undeploy the application.
In the next tutorial section we will see how to deal with job/batch components. Jobs are different from services
in the sense that they are submitted to the scheduler and then they run to completion.
Let's see how to define a [stress job](../../../stress/job/noscheduler/README.md) component in order to
generate load on the compute system.
