.. _service_nfv:

=================
Service NFV
=================

OpenNebula `Marketplace Appliance <http://marketplace.opennebula.systems/appliance/>`_.

There are several Virtual Network Functions (VNF) available on this appliance. VNFs that are not configured will be disabled by default.

.. include:: shared/features.txt

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <nfv_context_param>` parameters:

.. TODO: add |image-context-vars|


After you are done, click on the button **Instantiate**. Virtual machine with running service should be ready in a few minutes.

.. include:: shared/report.txt

.. code::

        ___   _ __    ___
       / _ \ | '_ \  / _ \   OpenNebula Service Appliance
      | (_) || | | ||  __/
       \___/ |_| |_| \___|

     All set and ready to serve 8)

    [root@nfv-appliance ~]# cat /etc/one-appliance/config

    [Load Balancer]
    Enabled: YES
    Load Balancer port: 80
    Load Balancer backend 1: 10.0.85.96
    Load Balancer monitoring portal: YES
    Load Balancer monitoring portal URL: http://server_address:8989/stats
    Load Balancer monitoring portal user: admin
    Load Balancer monitoring portal password: admin

    [NAT]
    Enabled: YES
    NAT outgoing NIC: eth0

    [Port Forward]
    Enabled: YES
    Entry 1: 10.10.0.75:90 --> 10.10.0.51:9869

    [DHCP]
    Enabled: Yes
    DHCP address pool: 10.10.158.147-10.10.158.157
    DHCP default gateway: 10.10.158.147-10.10.158.157

    Enabled: Yes
    VPN address pool: 10.218.54.0 255.255.255.0
    VPN listen address: 10.10.0.75
    VPN client configuration file: /root/client.ovpn


.. _nfv_context_param:

Contextualization
=================

Contextualization parameters provided in the Virtual Machine template controls the initial VM configuration. Except for the `common set <http://docs.opennebula.org/stable/operation/references/template.html#context-section>`_ of parameters supported by every appliance on the OpenNebula Marketplace, there are few specific to the particular service appliance. The parameters should be provided in the ``CONTEXT`` section of the Virtual Machine template, read the OpenNebula `Operation Guide <http://docs.opennebula.org/stable/operation/vm_setup/kvm.html#set-up-the-virtual-machine-template>`__ for more details.

**NOTE: Options marked as mandatory are only required for the VNF to be configured. If a mandatory option is not set, it's respective VNF will not be enabled; but the other VNFs will be configured and enabled if their respective mandatory options are set. In other words, if only the mandatory options from the VPN VNF are set, only VPN will be configured and enabled.**

===================================== ========= ============== ========= ===========
Parameter                             Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_LB_BACKENDS``                YES                      configure IPs from the backends separated by spaces
``ONEAPP_LB_PORT``                    YES                      configure Listening port on the LB frontend
``ONEAPP_LB_USER``                    NO                       configure Username for the management portal
``ONEAPP_LB_PASSWORD``                NO                       configure Password for the management portal
``ONEAPP_NAT_NIC``                    YES                      configure Out interface for the NAT traffic
``ONEAPP_PF_ENTRIES``                 YES                      configure Port Forward entries separated by spaces in the
                                                                         format: INCOMING_IP:PORT-DEST_IP:PORT
``ONEAPP_DHCP_RANGE``                 YES                      configure DHCP IP pool in the format: start_ip-end_ip
``ONEAPP_DHCP_GATEWAY``               NO        LOCAL_IP       configure Gateway that the DHCP server will advertise.
``ONEAPP_VPN_NETWORK``                YES                      configure Network and mask that the VPN will use in the format "IP Dot-decimal_net_mask"
``ONEAPP_VPN_SERVER``                 YES                      configure IP Where VPN server is listening
===================================== ========= ============== ========= ===========

.. _nfv_lb:

List of VNFs:
=============

Load Balancer
-------------

Configures an HAProxy load balancer. **ONEAPP_LB_BACKENDS** and **ONEAPP_LB_PORT**
is required. **ONEAPP_LB_PORT**  is the same for the backends and the frontend
(the load balancer will listen on the same port where the backend servers are
listening).

If **ONEAPP_LB_USER** and **ONEAPP_LB_PASSWORD** are set, a monitoring portal
for the load balancer will be enabled. The url is: http://appliance_ip:8989/stats

Network Address Translation:
----------------------------

It will configure iptables's masquerade to NAT all the outgoing traffic. It will use the NIC
 defined on **ONEAPP_NAT_NIC** as the outgoing NIC.

Port Forwarding:
----------------

It will perform DNAT using iptables to all entries listed on **ONEAPP_PF_ENTRIES**.

.. |image-download| image:: /images/kubernetes/kubernetes-download.png
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
