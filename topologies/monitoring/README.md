# TOSCA Topology Template for the monitoring application

This section is a bit different from the previous ones as we will not comment how to create a TOSCA component
but rather how to compose components within an application.
In TOSCA this is called a topology template.

[types.yaml](types.yaml) contains the TOSCA definition of the topology template.

The first thing we can note is that this YAML file is the most verbose we have seen so far. Moreover, this is a
simplified version of the Topology Template as it does not contain the normative workflows that Alien4Cloud
automatically generates.

So, basically, the idea is that creating such topology template *by hand* is painful and error prone.
Fortunately, we can use Alien4Cloud to generate such templates for us by drag&dropping components from
Alien4Cloud catalog to its WYSIWYG editor.

However, what we can see in this topology template is that we have an import section similar to the ones we
had in TOSCA components.

Then the `topology_template` itself contains an input section that allows to define inputs for the topology that
can be customized prior to the deployment. You can retrieve in the topology a `get_input` TOSCA function that
references this input.

Then there is the `node_templates` section that contains the components that we want to deploy with predefined
values for their properties.
Do not overlook at the `requirements` and `capabilities` sections as we will explore them in details in the
second part of this tutorial.

Then we have the `outputs` of the topology that are updated by Alien4Cloud as soon as they can be resolved for
runtime attributes. They can contain any kind of TOSCA function but they generally leverage the `get_attribute`
function to expose runtime attributes as they are not known in advance.
Typically in our case we use them to retrieve IP addresses of Compute nodes.

Finally, it contains a section dedicated to custom workflows. In our case we have a `AddPrometheusTarget`
workflow that sequentially call the `insert_target` and `reload_config` operations on the `Prometheus` node.
This workflow allows to provide an input which represent the IP address of the monitored target to add to the
Prometheus configuration.

## Next steps

This conclude the first part of the tutorial. You can switch to the [advanced tutorial](https://github.com/loicalbertin/tosca-tutorial/blob/advanced/README.md).
