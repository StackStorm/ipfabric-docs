Using VCS Fabric 
================

These workflows can be used with VCS Fabric implementations. This document provides a summary of
what the workflows do - see the :doc:`reference documentation <../workflows>` for specific
details on requirements, parameters, return codes and error messages.

.. contents::
   :local:
   :depth: 1

Add a Multihomed or Singlehomed Endpoint
----------------------------------------

**Workflow Name: add_multihomed_endpoint**

**Workflow Name: add_singlehomed_endpoint**

The add_multihomed_endpoint workflow automates the configuration of the edge ports of the VCS fabric.
The workflow automates creation of port-channel interfaces (LAGs and vLAGs), configuration of the
port-channel interface as access or trunk, creation and association of VLANs with the port-channel
interfaces as well as validation of the port channel state.

This workflow must be used to automate the provisioning external layer 2 connections from the
VCS fabric. The external connections can be to endpoints in the network like physical hosts
or network services like load balancers and firewalls or can be to other networking devices
like Core devices.

When deploying single homed endpoints, use **add_singlehomed_endpoint** workflow, which works similar to the above except without creating a port channel.

Configure VRRPe Gateway
-----------------------

**Workflow Name: configure_vrrpe_gw**

The configure_vrrpe_gw workflow automates the creation of a VRRP-E based default gateway
in a VCS fabric including the VE interfaces.

This workflow can be used for use cases where L3 boundary is present in the VCS fabric.
This could be at the spine in a VRRP-E based redundant gateway for the VLANs 
present in the fabric, the provisioning for which is automated through this workflow. 
It could also be used where the Layer 3 boundary is at the border leaves.

Add Multihomed Endpoint and Configure L3 Gateway
------------------------------------------------

** Workflow Name: add_multihomed_endpoint_and_gw**

The add_multihomed_endpoint_and_gw workflow automates the addition of an endpoint which needs
Layer 3 termination within the VCS fabric. It automates the provisioning of both the
edge ports as well as the VRRP-E based redundant gateway. It combines the actions in
add_multihomed_endpoint and configure_vrrpe_gw.
