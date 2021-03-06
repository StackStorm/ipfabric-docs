Setting Up An IP Fabric
=======================

This document provides an overview of how to use the Extreme Workflow Composer DC Fabric Automation
Suite to automate the provisioning and the maintenance of an IP Fabric. The DC Fabric Automation
Suite can automatically configure interfaces, BGP peering and related settings. This ensures
consistent configuration across the IP Fabric, with minimal effort.

.. note::
    This document covers the operation of the |ewc| DC Fabric Automation Suite. For more information
    about IP Fabrics in general, refer to the `Network OS IP Fabric
    Configuration Guide <https://documentation.extremenetworks.com/networkos/SW/73x/nos-730-ipfabrics.pdf>`_
    and the `IP Fabric Validated Design <https://www.extremenetworks.com/resources/extreme-validated-design/extreme-ip-fabric-architecture/>`_ 

The EWC DC Fabric Automation Suite supports easy integration with Zero-Touch Provisioning (ZTP). It can also be used 
without ZTP, however, initial switch setup and registration will be a manual process.

The EWC DC Fabric Automation Suite's default configuration comes with a set of predefined configuration parameters such 
as ASN range, and IP address ranges which can be used to create an IP Fabric. The values of these predefined configuration parameters are fixed and cannot be changed. To see the list of these predefined parameters, refer to the
:ref:`IP Fabric configuration parameters<ip_fabric_parameters>` documentation.

.. figure:: ../../../_static/images/solutions/dcfabric/bwc_components.jpg
    :align: center

    **Components of Extreme Workflow Composer**

The illustration shows the components of the |ewc| and the switch spines and leaves connectivity. This will be
the example setup for showing how to initially provision an IP Fabric using EWC.

Once your IP Fabric is provisioned, check out the :doc:`Using IP Fabric<using_ipfabric>` documentation
for Day-N service provisioning workflows.

.. note::
    The VCS ID for switch spine and leaves should be different in both the ZTP-enabled configuration and
    non-ZTP enabled configuration. If the VCS IDs are the same, the switches will automatically form an
    Ethernet fabric. For example, the VCS ID for spines can be 1 and for leaves it can be 2.

Initial Fabric Provisioning
---------------------------

Configuring an IP Fabric with ZTP enabled
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The DC Fabric Automation Suite can automatically provision a VDX switch and create an IP Fabric on the switch
if the switch has ZTP enabled and if no management IP address has been assigned to the switch.

Zero Touch Provisioning (ZTP) can be used to bring up a switch with new firmware and a preset configuration automatically. The switch does not need to be configured via the console. ZTP uses a DHCP-based process known as DHCP Automatic Deployment (DAD) to handle basic configuration such as assigning a VCS ID, VCS mode, an RBridge ID, and downloading firmware.

As part of the ZTP process, the switch can execute a local script. This stage is used to register the switch with |ewc| and run the BGP workflow if desired.

.. note::
    For detailed information about ZTP, refer to the :doc:`ZTP reference <../ztp_reference>`
    and the `Network OS Management Configuration Guide <https://documentation.extremenetworks.com/networkos/SW/73x/nos-730-management.pdf>`_.

.. warning::
    The first switch that is powered on and executes the ZTP process must be a spine switch.
    This is to ensure that the workflow correctly identifies switches as leaf or spine.

If the switch has ZTP enabled, complete the following steps:

    1.  Ensure that DHCP and FTP servers to be used in the fabric have been installed.
    2.  Ensure that |ewc| and DC Fabric automation suite have been installed.
    3.  After the process has finished executing, enter the ``bwc dcf show config bgp`` command

.. note::
    Make sure switches have not been powered on. Connect the switches in a leaf-spine topology as shown in the illustration.
    DC Fabric Automation Suite assigns management IP addresses to the switches, registers the switches in its 
    database, and creates an IP Fabric.

.. code-block:: shell

    $ bwc dcf show config bgp
      Show BGP Configuration

      Switch 10.24.39.225 (Leaf):
      rbridge-id 225
        router bgp
          local-as 65000
          bfd interval 300 min-rx 300 multiplier 3
          neighbor 10.10.10.1 remote-as 64512 state ESTAB up_time 2d20h40m creation_time 2016-08-11
          05:11:45
          neighbor 10.10.10.1 ebgp-multihop 5
          neighbor 10.10.10.3 remote-as 64513 state ESTAB up_time 17h5m24s creation_time 2016-08-11
          05:11:45
          neighbor 10.10.10.3 ebgp-multihop 5
          address-family ipv4 unicast
           redistribute connected
           neighbor 10.10.10.1 allowas-in 5
           neighbor 10.10.10.3 allowas-in 5
           maximum-paths 8
           graceful-restart
           next-hop-recursion
          address-family l2vpn evpn
           neighbor 10.10.10.1 activate
           neighbor 10.10.10.1 allowas-in 5
           neighbor 10.10.10.1 next-hop-unchanged
           neighbor 10.10.10.3 activate
           neighbor 10.10.10.3 allowas-in 5
           neighbor 10.10.10.3 next-hop-unchanged

      Switch 10.24.39.224 (Spine):
      rbridge-id 224
        router bgp
          local-as 64512
          bfd interval 300 min-rx 300 multiplier 3
          neighbor 10.10.10.0 remote-as 65000 state ESTAB up_time 2d20h40m creation_time 2016-08-11
          05:11:52
          neighbor 10.10.10.0 ebgp-multihop 5
          neighbor 10.10.10.4 remote-as 65001 state ESTAB up_time 17h5m26s creation_time 2016-08-11
          05:11:52
          neighbor 10.10.10.4 ebgp-multihop 5
          neighbor 10.10.10.10 remote-as 65002 state ESTAB up_time 17h5m30s creation_time 2016-08-11
          05:11:52
          neighbor 10.10.10.10 ebgp-multihop 5
          neighbor 10.10.10.12 remote-as 65003 state IDLE up_time 0h0m0s creation_time 2016-08-11
          05:11:52
          neighbor 10.10.10.12 ebgp-multihop 5
          neighbor 10.10.10.18 remote-as 65003 state ESTAB up_time 17h5m24s creation_time 2016-08-11
          05:11:52
          neighbor 10.10.10.18 ebgp-multihop 5
          address-family ipv4 unicast
           redistribute connected
           neighbor 10.10.10.0 allowas-in 5
           neighbor 10.10.10.4 allowas-in 5
           neighbor 10.10.10.10 allowas-in 5
           neighbor 10.10.10.12 allowas-in 5
           neighbor 10.10.10.18 allowas-in 5
           maximum-paths 8
           graceful-restart
           next-hop-recursion
          address-family l2vpn evpn
           retain route-target all
           neighbor 10.10.10.0 activate
           neighbor 10.10.10.0 allowas-in 5
           neighbor 10.10.10.0 next-hop-unchanged
           neighbor 10.10.10.4 activate
           neighbor 10.10.10.4 allowas-in 5
           neighbor 10.10.10.4 next-hop-unchanged
           neighbor 10.10.10.10 activate
           neighbor 10.10.10.10 allowas-in 5
           neighbor 10.10.10.10 next-hop-unchanged
           neighbor 10.10.10.12 activate
           neighbor 10.10.10.12 allowas-in 5
           neighbor 10.10.10.12 next-hop-unchanged
           neighbor 10.10.10.18 activate
           neighbor 10.10.10.18 allowas-in 5
           neighbor 10.10.10.18 next-hop-unchanged

Configuring an IP Fabric manually or without ZTP enabled
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If the VDX switch does not have ZTP enabled, or if you want to configure an IP Fabric
manually, complete the following steps:

    1.  Register the switch in the |ewc| database.  
    2.  Verify that the switch is registered.    
    3.  Repeat Steps 1 and 2 for each switch that will be added to the IP Fabric. 
    4.  Execute the BGP workflow.
    5.  Review and verify the IP Fabric configuration using the the ``bwc dcf show config bgp`` command. 

.. note::
    To use the DC Fabric Automation Suite to configure an IP Fabric without ZTP enabled, your environment must meet
    these prerequisites: 

     * The switches are physically connected in a leaf-spine topology.
     * Each switch has a management IP address and gateway assigned.
     * All switches are reachable via SSH from the |st2| server.
     * All switches have a working username and password. 

.. warning::
    The first switch that is added to the server must always be a **spine** switch. If it is not,
    delete the leaf switch from the |ewc| server and add a spine switch first. After the first spine
    has been added, the order of adding more switches will not matter as they will be automatically identified as Spine or Leaf      
    switches using LLDP.

Use the DC Fabric Automation Suite CLI to configure an IP Fabric by completing the following steps:

1. Register the switches in the |ewc| database by entering the ``bwc dcf inventory
   register`` command:

   ``$ bwc dcf inventory register host=<switch IP address> fabric=<fabric_name> user=<user_name> passwd=<password>``
 
  For example, to register the switch with IP 10.24.39.224 (NB The default username is *admin* 
  and default password is *password* for all VDX switches):

.. code-block:: shell

    $ bwc dcf inventory register host=10.24.39.224 fabric=default user=admin passwd=password

      Inventory Add
      +--------------+---------+------------+----------+------+-------+-------+---------+
      | IP           | Model   | Rbridge-Id | Firmware | Name | Role  |   ASN | Fabric  |
      +--------------+---------+------------+----------+------+-------+-------+---------+
      | 10.24.39.224 | VDX6740 |        224 | 7.1.0    | sw0  | Spine | 64512 | default |
      +--------------+---------+------------+----------+------+-------+-------+---------+

2. Verify that the switches are registered by entering the ``bwc dcf inventory list fabric=<fabric_name>``
   command:

.. code-block:: shell

     $ bwc dcf inventory list --fabric=default

      Inventory List
      +--------------+-------------+------------+----------+----------------+-------+-------+---------+
      | IP           | Model       | Rbridge-Id | Firmware | Name           | Role  |   ASN | Fabric  |
      +--------------+-------------+------------+----------+----------------+-------+-------+---------+
      | 10.24.39.224 | VDX6740     |        224 | 7.1.0    | sw0            | Spine | 64512 | default |
      +--------------+-------------+------------+----------+----------------+-------+-------+---------+

3. Repeat steps 1 and 2 above to register the remaining switches to be added. If some configuration parameter value changes
   on the switch, the IP Fabric can be updated:

.. code-block:: shell

    $ bwc dcf inventory update --fabric=default

      Inventory Update
      +--------------+-------------+------------+----------+----------------+-------+-------+---------+
      | IP           | Model       | Rbridge-Id | Firmware | Name           | Role  |   ASN | Fabric  |
      +--------------+-------------+------------+----------+----------------+-------+-------+---------+
      | 10.24.39.225 | VDX6740     |        225 | 7.1.0    | sw0            | Leaf  | 65000 | default |
      | 10.24.39.229 | VDX6740     |        229 | 7.1.0    | VCS_VDX_39_229 | Leaf  |       | default |
      | 10.24.39.228 | VDX6740     |        228 | 7.1.0    | VCS_VDX_39_228 | Leaf  |       | default |
      | 10.24.39.227 | VDX6740     |        227 | 7.1.0    | sw0            | Leaf  |       | default |
      | 10.24.39.226 | VDX6740T    |         26 | 7.1.0    | sw0            | Leaf  |       | default |
      | 10.24.39.224 | VDX6740     |        224 | 7.1.0    | sw0            | Spine | 64512 | default |
      | 10.24.39.223 | VDX6740T-1G |        223 | 7.1.0    | sw0            | Spine |       | default |
      +--------------+-------------+------------+----------+----------------+-------+-------+---------+

4. Execute the BGP workflow by entering the command ``bwc dcf workflow bgp`` command:

.. code-block:: shell

     $ bwc dcf workflow bgp fabric=default

       BGP Workflow Result:
   
       Switch 10.24.39.225 (Leaf):
       rbridge-id 225
         router bgp
           local-as 65000
           bfd interval 300 min-rx 300 multiplier 3
           neighbor 10.10.10.1 remote-as 64512 state ESTAB up_time 2d20h40m creation_time 2016-08-11
           05:11:45
           neighbor 10.10.10.1 ebgp-multihop 5
           neighbor 10.10.10.3 remote-as 64513 state ESTAB up_time 17h5m24s creation_time 2016-08-11
           05:11:45
           neighbor 10.10.10.3 ebgp-multihop 5
           address-family ipv4 unicast
            redistribute connected
            neighbor 10.10.10.1 allowas-in 5
            neighbor 10.10.10.3 allowas-in 5
            maximum-paths 8
            graceful-restart
            next-hop-recursion
           address-family l2vpn evpn
            neighbor 10.10.10.1 activate
            neighbor 10.10.10.1 allowas-in 5
            neighbor 10.10.10.1 next-hop-unchanged
            neighbor 10.10.10.3 activate
            neighbor 10.10.10.3 allowas-in 5
            neighbor 10.10.10.3 next-hop-unchanged
   
       Switch 10.24.39.224 (Spine):
       rbridge-id 224
         router bgp
           local-as 64512
           bfd interval 300 min-rx 300 multiplier 3
           neighbor 10.10.10.0 remote-as 65000 state ESTAB up_time 2d20h40m creation_time 2016-08-11
           05:11:52
           neighbor 10.10.10.0 ebgp-multihop 5
           neighbor 10.10.10.4 remote-as 65001 state ESTAB up_time 17h5m26s creation_time 2016-08-11
           05:11:52
           neighbor 10.10.10.4 ebgp-multihop 5
           neighbor 10.10.10.10 remote-as 65002 state ESTAB up_time 17h5m30s creation_time 2016-08-11
           05:11:52
           neighbor 10.10.10.10 ebgp-multihop 5
           neighbor 10.10.10.12 remote-as 65003 state IDLE up_time 0h0m0s creation_time 2016-08-11
           05:11:52
           neighbor 10.10.10.12 ebgp-multihop 5
           neighbor 10.10.10.18 remote-as 65003 state ESTAB up_time 17h5m24s creation_time 2016-08-11
           05:11:52
           neighbor 10.10.10.18 ebgp-multihop 5
           address-family ipv4 unicast
            redistribute connected
            neighbor 10.10.10.0 allowas-in 5
            neighbor 10.10.10.4 allowas-in 5
            neighbor 10.10.10.10 allowas-in 5
            neighbor 10.10.10.12 allowas-in 5
            neighbor 10.10.10.18 allowas-in 5
            maximum-paths 8
            graceful-restart
            next-hop-recursion
           address-family l2vpn evpn
            retain route-target all
            neighbor 10.10.10.0 activate
            neighbor 10.10.10.0 allowas-in 5
            neighbor 10.10.10.0 next-hop-unchanged
            neighbor 10.10.10.4 activate
            neighbor 10.10.10.4 allowas-in 5
            neighbor 10.10.10.4 next-hop-unchanged
            neighbor 10.10.10.10 activate
            neighbor 10.10.10.10 allowas-in 5
            neighbor 10.10.10.10 next-hop-unchanged
            neighbor 10.10.10.12 activate
            neighbor 10.10.10.12 allowas-in 5
            neighbor 10.10.10.12 next-hop-unchanged
            neighbor 10.10.10.18 activate
            neighbor 10.10.10.18 allowas-in 5
            neighbor 10.10.10.18 next-hop-unchanged


5. After the command executes, enter the ``bwc dcf show config bgp`` command and review
   the information displayed:

.. code-block:: shell

     $ bwc dcf show config bgp --fabric=default

       Show BGP Configuration
   
       Switch 10.24.39.225 (Leaf):
       rbridge-id 225
         router bgp
           local-as 65000
           bfd interval 300 min-rx 300 multiplier 3
           neighbor 10.10.10.1 remote-as 64512 state ESTAB up_time 2d20h40m creation_time 2016-08-11
           05:11:45
           neighbor 10.10.10.1 ebgp-multihop 5
           neighbor 10.10.10.3 remote-as 64513 state ESTAB up_time 17h5m24s creation_time 2016-08-11
           05:11:45
           neighbor 10.10.10.3 ebgp-multihop 5
           address-family ipv4 unicast
            redistribute connected
            neighbor 10.10.10.1 allowas-in 5
            neighbor 10.10.10.3 allowas-in 5
            maximum-paths 8
            graceful-restart
            next-hop-recursion
           address-family l2vpn evpn
            neighbor 10.10.10.1 activate
            neighbor 10.10.10.1 allowas-in 5
            neighbor 10.10.10.1 next-hop-unchanged
            neighbor 10.10.10.3 activate
            neighbor 10.10.10.3 allowas-in 5
            neighbor 10.10.10.3 next-hop-unchanged
    
       Switch 10.24.39.224 (Spine):
        rbridge-id 224
          router bgp
            local-as 64512
            bfd interval 300 min-rx 300 multiplier 3
            neighbor 10.10.10.0 remote-as 65000 state ESTAB up_time 2d20h40m creation_time 2016-08-11
            05:11:52
            neighbor 10.10.10.0 ebgp-multihop 5
            neighbor 10.10.10.4 remote-as 65001 state ESTAB up_time 17h5m26s creation_time 2016-08-11
            05:11:52
            neighbor 10.10.10.4 ebgp-multihop 5
            neighbor 10.10.10.10 remote-as 65002 state ESTAB up_time 17h5m30s creation_time 2016-08-11
            05:11:52
            neighbor 10.10.10.10 ebgp-multihop 5
            neighbor 10.10.10.12 remote-as 65003 state IDLE up_time 0h0m0s creation_time 2016-08-11
            05:11:52
            neighbor 10.10.10.12 ebgp-multihop 5
            neighbor 10.10.10.18 remote-as 65003 state ESTAB up_time 17h5m24s creation_time 2016-08-11
            05:11:52
            neighbor 10.10.10.18 ebgp-multihop 5
            address-family ipv4 unicast
             redistribute connected
             neighbor 10.10.10.0 allowas-in 5
             neighbor 10.10.10.4 allowas-in 5
             neighbor 10.10.10.10 allowas-in 5
             neighbor 10.10.10.12 allowas-in 5
             neighbor 10.10.10.18 allowas-in 5
             maximum-paths 8
             graceful-restart
             next-hop-recursion
            address-family l2vpn evpn
             retain route-target all
             neighbor 10.10.10.0 activate
             neighbor 10.10.10.0 allowas-in 5
             neighbor 10.10.10.0 next-hop-unchanged
             neighbor 10.10.10.4 activate
             neighbor 10.10.10.4 allowas-in 5
             neighbor 10.10.10.4 next-hop-unchanged
             neighbor 10.10.10.10 activate
             neighbor 10.10.10.10 allowas-in 5
             neighbor 10.10.10.10 next-hop-unchanged
             neighbor 10.10.10.12 activate
             neighbor 10.10.10.12 allowas-in 5
             neighbor 10.10.10.12 next-hop-unchanged
             neighbor 10.10.10.18 activate
             neighbor 10.10.10.18 allowas-in 5
             neighbor 10.10.10.18 next-hop-unchanged


To add a switch to the existing fabric, register the switch to the fabric and then run ``bwc
dcf workflow bgp fabric=<fabric_name>``. To remove a switch from the fabric
run ``bwc dcf inventory delete host=<ip_address>``

.. code-block:: shell

     $ bwc dcf inventory delete host=10.24.39.224

       Inventory delete
       +--------------+---------+------------+----------+---------+-------+-----+---------+
       | IP           | Model   | Rbridge-Id | Firmware | Name    | Role  | ASN | Fabric  |
       +--------------+---------+------------+----------+---------+-------+-----+---------+
       | 10.24.39.224 | VDX6740 |        224 | 7.0.1    | VDX_224 | Spine |     | default |
       +--------------+---------+------------+----------+---------+-------+-----+---------+

.. note::
    When adding a new spine or leaf to an existing fabric, ensure the new switch does
    not have any existing BGP or interface configuration. This will ensure the workflow
    runs smoothly.

Fabric Management
-----------------

Updating switch credentials and information
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A switch is registered to the server using the switch credentials. If the credentials are
changed on the switch, the change must be updated in the |ewc| server
using the ``bwc dcf inventory update --host=<ip_address>`` command.

.. code-block:: shell

    $ bwc dcf inventory update --host=10.24.39.225  --user=lab123 --passwd=123lab

      Inventory Update
      +--------------+---------+------------+----------+------+------+-------+---------+
      | IP           | Model   | Rbridge-Id | Firmware | Name | Role |   ASN | Fabric  |
      +--------------+---------+------------+----------+------+------+-------+---------+
      | 10.24.39.225 | VDX6740 |        225 | 7.1.0    | sw0  | Leaf | 65000 | default |
      +--------------+---------+------------+----------+------+------+-------+---------+


Generating a topology map
~~~~~~~~~~~~~~~~~~~~~~~~~

You can display the fabric topology of an IP Fabric.

1. Enter the ``bwc dcf show topology fabric=<fabric_name>`` command.

Refer the :doc:`dcf CLI <../dcf_cli/basic_cli>` page for options available for the
``bwc dcf show topology`` command.

.. code-block:: shell

    $ bwc dcf show topology fabric=default --format=pdf --render_dir=/tmp

      Topology map generated: /tmp/topology_default_20160811-020715.pdf

.. note::
   "- -format=<option>" and "- -render_dir=<file location>" is optional. By default a PDF
   file and a dot file is generated in *tmp* folder if format flag and render_dir
   flags are not used.

2. Open the topology file that was generated using the appropriate software.


Confirming IP Fabric details
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To check the details of the registered switches in the |ewc| server and compare it with the current
switch configuration, use following commands:


.. code-block:: shell

    bwc dcf show config bgp fabric=<fabric_name>
    bwc dcf show topology fabric=<fabric_name> [ --format=<format> ] [--render_dir=<file location>]
    bwc dcf inventory list --fabric=<fabric_name> | --host=<switch_ip>
    bwc dcf inventory show vcs links fabric=<fabric_name>
    bwc dcf inventory show lldp fabric=<fabric_name>

.. _ip_fabric_parameters:

IP Fabric configuration parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This DC IP Fabric Automation Suite has a default set of configuration parameters defined for an
IP Fabric. The default set of configuration parameter values are fixed and cannot be changed by
users. Some of the parameters have been changed in v1.1 of the automation suite based on
`IP Fabric Validated Design <https://www.extremenetworks.com/resources/extreme-validated-design/extreme-ip-fabric-architecture/>`_
recommendations. In addition some new parameters have also been added in v1.1. You can display the
values of the parameters using the ``bwc dcf fabric config show`` CLI command.  The table below
shows the comparison for both DC IP Fabric Automation Suite v1.0 and v1.1 values with the display
showing only one version.

.. code-block:: shell
    
    $ bwc dcf fabric config show fabric=default

      +----------------------+-----------------+-----------------+-----------------+
      | Parameter Name       | DCF v1.0        | DCF v1.1        | DCF v1.2        |
      +----------------------+-----------------+-----------------+-----------------+
      | Fabric Name          | default         | default         | default         |
      | bgp_multihop         | 5               | 2               | 2               |
      | spine_asn_block      | 64512-64999     | 64512           | 64512           |
      | leaf_asn_block       | 65000-65534     | 65000-65534     | 65000-65534     |
      | spine_peer_group     | Not Supported   | leaf-group      | leaf-group      |
      | leaf_peer_group      | Not Supported   | spine-group     | spine-group     |
      | max_paths            | 8               | 8               | 8               |
      | loopback_port_number | 1               | 1               | 1               |
      | evpn_enabled         | Yes             | Yes             | Yes             |
      | allowas_in           | 5               | 1               | 1               |
      | bfd_multiplier       | 3               | 3               | 3               |
      | p2p_link_range       | 10.10.10.0/23   | 10.10.10.0/23   | 10.10.10.0/23   |
      |                      | or "unnumbered" | or "unnumbered" | or "unnumbered" |
      | bfd_tx               | 300             | 300             | 300             |
      | anycast_mac          | aabb.ccdd.eeff  | 02aa.bbcc.ddee  | 02aa.bbcc.ddee  |
      | loopback_ip_range    | 172.32.254.0/24 | 172.32.254.0/24 | 172.32.254.0/24 |
      | bfd_rx               | 300             | 300             | 300             |
      | mtu                  | 9216            | 9216            | 9216            |
      | ip_mtu               | 9018            | 9018            | 9018            |
      | vni_auto_map         | Not Supported   | Not Supported   | Yes             |
      | enable_vf            | Not Supported   | Not Supported   | No              |
      | control_VLAN         | Not Supported   | Not Supported   | 4090            |
      | mct_link_ip_range    | Not Supported   | Not Supported   | 10.10.12.0/23   |
      +----------------------+-----------------+-----------------+-----------------+

.. note::

  IP Unnumbered option for p2p_link_range in fabric deployment is not supported for SLX.

If you want a different set of configuration parameters or a different configuration with
**unnumbered** for the IP address, you must create a new IP Fabric and then define the
values for the configuration parameters. The following configuration parameters can be added
with the ``bwc dcf fabric config set fabric=<fabric_name> key=<key_name> value=<valu_name>``
command as explained in next section:

+------------------------+-------------------------------------------------------------------+
| anycast_mac            | A valid MAC address in the format xxxx.xxxx.xxxx or               |
|                        | xx:xx:xx:xx:xx:xx                                                 |
+------------------------+-------------------------------------------------------------------+
| evpn_enabled           | Yes or No                                                         |
+------------------------+-------------------------------------------------------------------+
| bfd_tx                 | An integer from 50 through 30000                                  |
+------------------------+-------------------------------------------------------------------+
| bfd_rx                 | An integer from 50 through 30000                                  |
+------------------------+-------------------------------------------------------------------+
| bfd_multiplier         | An integer from 3 through 50                                      |
+------------------------+-------------------------------------------------------------------+                 
| bgp_multihop           | An integer from 1 through 55. **Recommended value is 2**          |
+------------------------+-------------------------------------------------------------------+               
| max_paths              | An integer from 1 through 32                                      |
+------------------------+-------------------------------------------------------------------+
| p2p_link_range         | **(Required)** a valid IP-network or the word “unnumbered”        |
|                        +-------------------------------------------------------------------+ 
|                        | (case insensitive), based on what kind of BGP peers               |
|                        +-------------------------------------------------------------------+
|                        | connectivity you want, IP numbered or unnumbered. (Refer          |
|                        +-------------------------------------------------------------------+
|                        | overview section for details).                                    |
+------------------------+-------------------------------------------------------------------+
| loopback_ip_range      | **(Required)** A valid IP-network, for example,172.32.254.0/24    |
+------------------------+-------------------------------------------------------------------+                    
| leaf_asn_block         |  **(Required)** A single value or range from 1 through 4294967295 |
+------------------------+-------------------------------------------------------------------+                 
| spine_asn_block        | **(Required)** A single value or range from 1 through 4294967295  |
+------------------------+-------------------------------------------------------------------+                    
| leaf_peer_group        | Label for leaf peer group, for example, spine_group.              |
+------------------------+-------------------------------------------------------------------+                 
| spine_peer_group       | Label for spine peer group, for example, leaf_group.              |
+------------------------+-------------------------------------------------------------------+                  
| loopback_port_number   | **(Required)** A number from 1 through 255                        |
+------------------------+-------------------------------------------------------------------+                       
| allowas_in             | A number from 1 through 10. **Recommended value is 1**            |
+------------------------+-------------------------------------------------------------------+
| mtu                    | MTU size, min: 1522; max: 9216.                                   |
|                        | These values may change based on the switch operating system.     |
+------------------------+-------------------------------------------------------------------+
| ip_mtu                 | IP MTU size, min: 1300; max: 9018.                                |
|                        | These values may change based on the switch operating system.     |
+------------------------+-------------------------------------------------------------------+
| vni_auto_map           | Yes or No. Default is Yes. If 'Yes', configures VLAN or           |
|                        | bridge-domain to VNI mapping as auto under overlay gateway.       |
+------------------------+-------------------------------------------------------------------+
| enable_vf              | Yes or No. Default is No. If 'Yes', configures VCS Virtual Fabric |
|                        | on VDX devices.                                                   |
+------------------------+-------------------------------------------------------------------+
| mct_link_ip_range      | A valid Ip-network, for example,10.10.12.0/24                     |
|                        | on SLX devices.                                                   |
+------------------------+-------------------------------------------------------------------+
| control_VLAN           | A VLAN ID for MCT Control Traffic on SLX devices.                 |
|                        | Default is 4090.                                                  |
+------------------------+-------------------------------------------------------------------+

The required parameters must be added to the user-defined/custom configuration. The other
parameters are not optional. If you do not add optional parameters, |ewc|
will use the values from the default configuration.

.. note::
    Once the required configuration parameters are added to the user-defined fabric, they cannot be modified or deleted.
    To modify/update the mandatory parameter values, create a new fabric and then define the parameters required for this fabric.

Creating a new IP Fabric with user-defined configuration parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Use the ``bwc dcf fabric add fabric=<fabric_name>`` command to create a new fabric
   configuration. For example, the following command creates a new user-defined IP Fabric
   called **user_fab**.

.. code-block:: shell

    $ bwc dcf fabric add fabric=user_fab
      Fabric user_fab added successfully

2. Use the ``bwc dcf fabric config set key=<key> value=<value> fabric=<fabric_name>``
   command to add parameters to the *user_fab* fabric created in previous step.

.. code-block:: shell
    :emphasize-lines: 1,4,7,10,13,16,19,22,25
   
    $ bwc dcf fabric config set key=p2p_link_range value=10.10.10.0/23 fabric=user_fab
      Setting p2p_link_range with value 10.10.10.0/23 added to fabric user_fab
    
    $ bwc dcf fabric config set key=spine_asn_block value=64512-64999 fabric=user_fab
      Setting spine_asn_block with value 64512-64999 added to fabric user_fab
   
    $ bwc dcf fabric config set key=leaf_asn_block value=65000-65534 fabric=user_fab
      Setting leaf_asn_block with value 65000-65534 added to fabric user_fab
   
    $ bwc dcf fabric config set key=loopback_ip_range value=172.32.254.0/24 fabric=user_fab
      Setting loopback_ip_range with value 172.32.254.0/24 added to fabric user_fab
   
    $ bwc dcf fabric config set key=loopback_port_number value=1 fabric=user_fab
      Setting loopback_port_number with value 1 added to fabric user_fab
   
    $ bwc dcf fabric config set key=bfd_multiplier value=10 fabric=new_fab
      Setting bfd_multiplier with value 10 added to fabric user_fab
   
    $ bwc dcf fabric config set key=bfd_rx value=888 fabric=user_fab
      Setting bfd_rx  with value 888 added to fabric user_fab
   
    $ bwc dcf fabric config set key=bfd_tx value=888 fabric=user_fab
      Setting bfd_tx with value 888 added to fabric user_fab
   
    $ bwc dcf fabric config set key=allowas_in value=7 fabric=user_fab

Setting allowas_in with value 7 added to fabric user_fab

3. Check and verify the parameter values before saving the configuration.
4. Use the ``bwc dcf fabric config show fabric=<fabric_name>`` command to display the fabric
   details added in step 2.

.. code-block:: shell

    $ bwc dcf fabric config show fabric=user_fab

      Fabric Config Show
      +----------------------+-----------------+
      | Field                | Value           |
      +----------------------+-----------------+
      | Fabric Name          | user_fab        |
      | spine_asn_block      | 64512           |
      | leaf_asn_block       | 65000-65534     |
      | loopback_port_number | 1               |
      | allowas_in           | 1               |
      | bfd_multiplier       | 10              |
      | p2p_link_range       | 10.10.10.0/23   |
      | bfd_tx               | 888             |
      | loopback_ip_range    | 172.32.254.0/24 |
      | bfd_rx               | 888             |
      +----------------------+-----------------+

Use :command:`fabric=<fabric name>` parameter to display details for a specific fabric.

Deleting an IP Fabric
~~~~~~~~~~~~~~~~~~~~~

To delete an IP Fabric, use the CLI ``bwc dcf fabric delete fabric=<fabric_name>`` command. .

    $ bwc dcf fabric delete fabric+ipfabric

.. rubric:: What's Next?

* Check out the :doc:`Day-N workflows <using_ipfabric>`.
* Understand the DC Fabric CLI - read the :doc:`../dcf_cli/basic_cli`.

