# TOSCA Component for Grafana server

This component demonstrates how to manage inheritance of TOSCA node types.

[types.yaml](types.yaml) contains the TOSCA definition.

## TOSCA definition

The TOSCA description contains a node type called `grafana.containers.ansible.nodes.Grafana` that will
represent a Docker container running a Grafana server.
This node type is derived from the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer`.

A `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` component will handle the Docker container lifecycle,
by using its defined properties to create and run a Docker container on top of a Docker engine.
That's why there is no playbooks or scripts for this component.

All we need to do is to redefine the properties of the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type as we expect them to be to run a Grafana server.

The implementation of the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type can be found within the [Ystia Forge](https://github.com/ystia/forge/blob/develop/org/ystia/docker/containers/generic/types.yml).

<details>
    <summary>Pro tip</summary>

In fact we can use the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` directly within a
TOSCA application an just set the properties directly in the application. But by using a new type we can
set the *default* properties for this component and we can also customize it with a specific icon to help
to identify it easily.

It is generally recommended to define a new type to avoid confusion, to improve reusability and to make it easier to extend as we will see in the next tutorial section regarding the Prometheus server.
</details>

### Properties

This node type redefines the following properties of the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type:

* `image`: is set by default to `grafana/grafana:latest` to target a Grafana server Docker image
* `restart_policy`: is set by default to `always` to restart the container on any error or on system restart
* `published_ports`: is set by default to `["3000:3000"]` to expose the Grafana server on port 3000

### Attributes

We do not define any attributes for this node type. The attributes are defined in the `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type.

### Operations

The lifecycle of this component remain the standard normative TOSCA lifecycle. The `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` node type implements the following operations:

* `create`: to create a new container
* `start`: to start it
* `stop`: to stop it
* `delete`: to delete it

See [the `GenericContainer` component on Ystia Forge](https://github.com/ystia/forge/blob/develop/org/ystia/docker/containers/generic/types.yml) for more details.

## Next steps

In the next tutorial section we will see how to extend a `org.ystia.docker.containers.docker.generic.nodes.GenericContainer` by adding some standard and custom operations. This will be done in the
[Prometheus server](../../../prometheus/containers/ansible/README.md) component.
