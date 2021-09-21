# TOSCA tutorial - Prometheus demo

This repository contains material for the TOSCA tutorial.

## Repository organization

This repository is organized in the following way:

* the [`main`](https://github.com/eflows4hpc/tosca-tutorial/tree/main) branch is the default one.
  It contains the first part of the tutorial that will introduce basic concepts of how to use TOSCA while still
  being able to run an end-to-end demo.
* the `advanced` branch - the one you are reading right now - contains the second part of the tutorial. It contains the advanced concepts that will allow to reduce manual work by automating configuration of nodes using **TOSCA relationships**.

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

Compared to the `main` branch the `advanced` branch brings the following improvements:

1. A new `prometheus.pub.capabilities.MonitoringEndpoint` is defined in the [Prometheus public types component](prometheus/pub/README.md) and serve as an interface between Prometheus and the monitored systems.
2. The [Prometheus Node Exporter](prometheus/node-exporter/ansible/README.md) component has been refactored to
   integrate this new capability.
3. The [Prometheus server](prometheus/containers/ansible/README.md) ships a new requirement and a new relationship
   in order to automatically connect Prometheus to the node exporter
4. Finally the [Topology template](topologies/monitoring/README.md) has been updated to reflect these changes
