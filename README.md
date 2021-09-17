# TOSCA tutorial - Prometheus demo

This repository contains material for the TOSCA tutorial.

## Repository organization

This repository is organized in the following way:

* the `main` branch is the default one. The one you are reading right now.
  It contains the first part of the tutorial that will introduce basic concepts of how to use TOSCA while still
  being able to run an end-to-end demo.
* the [`advanced`](https://github.com/loicalbertin/tosca-tutorial/tree/advanced) branch contains the second part of the tutorial. It contains the advanced concepts that will allow to reduce manual work by automating configuration of nodes using **TOSCA relationships**.

## Getting started

To be executed, this tutorial requires a running Ystia Orchestration platform connected to a supported cloud instance.
We recommend to use the [bootstrap process of Yorc](https://yorc.readthedocs.io/en/stable/bootstrap.html) in order to setup such stack.

However, we will walk through this tutorial step by step using README.md as reference. This should be enough for
most of trainees.

## Tutorial use case description

This tutorial will be used to demonstrate how to use TOSCA to deploy a simple monitoring system.
This system will be composed of the following components:

* a Prometheus Node Exporter that will expose metrics of the compute node on which it is deployed
* a Prometheus server that will collect metrics from the Node Exporter
* a Grafana server that will allow to visualize the metrics collected by the Prometheus server
* a job that could be launched on the compute node to stress the system and generate load that could be monitored.

### Tutorial walkthrough

This tutorial is designed to be read in the following order:

1. We will see the basic of modeling a TOSCA component with the [Prometheus Node Exporter](prometheus/node-exporter/ansible/README.md)
2. Then we will see how to inherit from an existing TOSCA component that manages docker containers to deploy
   a [Grafana server](grafana/containers/ansible/README.md) by simply defining some new properties.
3. A slightly more advanced scenario will be used to deploy a [Prometheus server](prometheus/containers/ansible/README.md) as a container just like Grafana, but with some extra configuration steps and we will also introduce the concept of *custom commands*
4. The [stress component](stress/job/noscheduler/README.md) will introduce the concept of *jobs scheduling* but will reuse an existing component to keep this as simple as possible.
5. Last but not least we will introduce the concept of TOSCA [Topologies templates](topologies/monitoring/README.md) that allows to define a template of an application that could be instantiated into new applications.

Once you have read all the above, you can switch to the [advanced tutorial](https://github.com/loicalbertin/tosca-tutorial/blob/advanced/README.md).
