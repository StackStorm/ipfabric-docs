.. NOTE: This file has been generated automatically, don't manually edit it

remove_switchport_access_vlan
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Description**: This removes a physical interface or port-channel from a VLAN. 

.. table::

   ================================  ======================================================================
   Parameter                         Description
   ================================  ======================================================================
   **mgmt_ip**                       The management IP address of the target device.

                                     Type: ``string``
   *username*                        The login user name to connect to the device.

                                     Type: ``string``
   *password*                        The login password to connect to the device.

                                     Type: ``string``
   **intf_type**                     The interface type.

                                     Choose from:

                                     - ethernet
                                     - tengigabitethernet
                                     - gigabitethernet
                                     - fortygigabitethernet
                                     - hundredgigabitethernet
                                     - port_channel

                                     **Default**: tengigabitethernet
   **intf_name**                     The interface name, for VDX in 3-tuple format (24/0/1), SLX/NI in 2-tuple format (24/1) or Port-channel number <1-6144>, for NI <1-256>.

                                     Type: ``string``
   **vlan_id**                       The VLAN ID to be configured on the interface. For 802.1Q VLANs, ID must be below 4096, for service or transport VFs valid range is from 4096 through 8191, for NI, vlan range <1-4090>.

                                     Type: ``string``
   ================================  ======================================================================

