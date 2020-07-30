.. _service_vnf:

==========================================================
Service Virtual Network Functions (VNF) and Virtual Router
==========================================================

OpenNebula `Marketplace Appliance <http://marketplace.opennebula.systems/appliance/>`_ implementing various **Virtual Network Functions** (VNFs) and `Virtual Router <http://docs.opennebula.org/stable/operation/network_management/vrouter.html>`_.


.. note::

    This appliance replaces the Virtual Routers available on the Marketplace in the past as **Vrouter Alpine** for KVM and vCenter platforms. These old versions are referrenced as **legacy** from now on. Templates and contextualization parameters are compatible with new appliance, you only need to update reference in the old imported template to the new image. It's highly recommended to migrate to the new appliance.

    To meet different use-cases, the core logic is provided via 2 appliances (sharing same image) on the Marketplace identified listed as

    * ``Service VNF`` - exposing all features as regular VM
    * ``Service Virtual Router`` - integration with OpenNebula Virtual Router interface

.. include:: shared/features-alpine.txt
* High-availability provided by `Keepalived <https://www.keepalived.org/>`_.
* **Network Functions**

  * DHCPv4
  * DNS recursor
  * NATv4
  * Virtual Router

Platform Notes
==============

Appliance components versions:

============================= ==================
Component                     Version
============================= ==================
ISC Kea                       1.6.1
Contextualization package     5.12.0
============================= ==================

.. _service_vnf_quick_start:

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <vnf_context_param>` parameters:

|image-context-vars|

.. note::

    **No VNF services are started by default**, you always need to select and enable those you require to run. This can be done right on VM instantiation or later (both to enable or disable) via the live VM update operations (in Sunstone in VM details do **Conf → Update Configuration** or use CLI command ``onevm updateconf``).

After you are done, click on the button **Instantiate**. Virtual machine with running service(s) should be ready in a few minutes.

.. include:: shared/report.txt

.. code::

        ___   _ __    ___
       / _ \ | '_ \  / _ \   OpenNebula Service Appliance
      | (_) || | | ||  __/
       \___/ |_| |_| \___|

     All set and ready to serve 8)

    localhost:~# cat /etc/one-appliance/config
    [VNF]
    ONEAPP_VNF_DHCP4_ENABLED = 'YES'
    ONEAPP_VNF_DHCP4_INTERFACES_DISABLED = 'ETH0'
    ONEAPP_VNF_DHCP4_LEASE_TIME = '3600'
    ONEAPP_VNF_DNS_ENABLED = 'YES'
    ONEAPP_VNF_DNS_INTERFACES_DISABLED = 'ETH0'
    ONEAPP_VNF_DNS_MAX_CACHE_TTL = '3600'
    ONEAPP_VNF_DNS_USE_ROOTSERVERS = 'YES'
    ONEAPP_VNF_NAT4_ENABLED = 'YES'
    ONEAPP_VNF_NAT4_INTERFACES_OUT = 'ETH0'
    ONEAPP_VNF_ROUTER4_ENABLED = 'YES'
    ONEAPP_VROUTER_ETH0_DNS = '10.0.0.2'
    ONEAPP_VROUTER_ETH0_GATEWAY = '192.168.150.1'
    ONEAPP_VROUTER_ETH0_IP = '192.168.150.102'
    ONEAPP_VROUTER_ETH0_MAC = '02:00:c0:a8:96:66'
    ONEAPP_VROUTER_ETH1_DNS = '192.168.101.1'
    ONEAPP_VROUTER_ETH1_GATEWAY = '192.168.101.1'
    ONEAPP_VROUTER_ETH1_IP = '192.168.101.1'
    ONEAPP_VROUTER_ETH1_MAC = '02:00:c0:a8:65:01'
    ONEAPP_VROUTER_ETH1_MASK = '255.255.255.0'
    ONEAPP_VROUTER_ETH2_DNS = '192.168.102.1'
    ONEAPP_VROUTER_ETH2_GATEWAY = '192.168.102.1'
    ONEAPP_VROUTER_ETH2_IP = '192.168.102.1'
    ONEAPP_VROUTER_ETH2_MAC = '02:00:c0:a8:66:01'
    ONEAPP_VROUTER_ETH2_MASK = '255.255.255.0'


.. _vnf_context_param:

Contextualization
=================

Contextualization parameters provided in the Virtual Machine template controls the initial VM configuration. Except for the `common set <http://docs.opennebula.org/stable/operation/references/template.html#context-section>`_ of parameters supported by every appliance on the OpenNebula Marketplace, there are few specific to the particular service appliance. The parameters should be provided in the ``CONTEXT`` section of the Virtual Machine template, read the OpenNebula `Operation Guide <http://docs.opennebula.org/stable/operation/vm_setup/kvm.html#set-up-the-virtual-machine-template>`_ for more details.

.. note::

    As described in the :ref:`Overview <service_vnf>`, there are 2 appliances available. Both share same image and support same set of contextualization parameters, the only difference is how they are managed in the OpenNebula - via Virtual Router (VR) interface or as regular Virtual Machine (VM).

If parameters support multiple values, the values can be are separated by spaces (``x y``), commas (``x,y``) or semicolons (``;``).

.. _vnfs_context_param:

Virtual Network Functions
-------------------------

Following parameters are supported in both deployment modes, as VR and VM.

.. _vnf_dhcp4_context_param:

Function DHCP4
~~~~~~~~~~~~~~

Function implements DHCPv4 service. If enabled without any specific configuration, it provides DHCP leases for all (non-management) interfaces and all their configured subnets.

**Basic parameters**:

============================================ ============== ===========
Parameter                                    Default        Description
============================================ ============== ===========
``ONEAPP_VNF_DHCP4_ENABLED``                 ``NO``         Enable/disable DHCP4 function (``YES``/``NO``)
``ONEAPP_VNF_DHCP4_INTERFACES``              all ifaces     List of :ref:`interfaces <vnf_interfaces>` to listen on (``<[!]ethX> ...``)
``ONEAPP_VNF_DHCP4_AUTHORITATIVE``           ``YES``        Server authoritativity (``YES``/``NO``)
``ONEAPP_VNF_DHCP4_LEASE_TIME``              ``3600``       DHCP lease time (seconds)
``ONEAPP_VNF_DHCP4_DNS``                                    Default nameservers (IP address)
``ONEAPP_VNF_DHCP4_GATEWAY``                                Default gateway (IP address)
``ONEAPP_VNF_DHCP4_MAC2IP_ENABLED``          ``YES``        Enable/disable MAC-to-IP translation (``YES``/``NO``)
``ONEAPP_VNF_DHCP4_MAC2IP_MACPREFIX``        ``02:00``      2-bytes OpenNebula MAC prefix for MAC-to-IP trans.
============================================ ============== ===========

**Advanced parameters:**

============================================ ============== ===========
Parameter                                    Default        Description
============================================ ============== ===========
``ONEAPP_VNF_DHCP4_ETHx``                                   Custom interface subnet/pool range (``<CIDR>:<start IP>-<end IP>``)
``ONEAPP_VNF_DHCP4_ETHx_DNS``                               Custom interface pool DNS (``<IP> ...``)
``ONEAPP_VNF_DHCP4_ETHx_GATEWAY``                           Custom interface pool gateway (``<IP> ...``)
``ONEAPP_VNF_DHCP4_ETHx_MTU``                               CUstom interface pool MTU option (number)
``ONEAPP_VNF_DHCP4_ETHx_ALIASy``                            Custom alias interface sub./pool range (``<CIDR>:<start IP>-<end IP>``)
``ONEAPP_VNF_DHCP4_ETHx_ALIASy_DNS``                        Custom alias interface pool DNS (``<IP> ...``)
``ONEAPP_VNF_DHCP4_ETHx_ALIASy_GATEWAY``                    Custom alias interface pool gateway (``<IP> ...``)
``ONEAPP_VNF_DHCP4_ETHx_ALIASy_MTU``                        Custom alias interface pool MTU (number)
``ONEAPP_VNF_DHCP4_MAC2IP_SUBNETS``                         List of subnets for MAC-to-IP transl. (``<network>/<prefix> ...``)
``ONEAPP_VNF_DHCP4_CONFIG``                                 ISC Kea configuration (JSON Base64 encoded)
``ONEAPP_VNF_DHCP4_SUBNET[0-9]``                            ISC Kea subnet definition (JSON Base64 encoded)
``ONEAPP_VNF_DHCP4_HOOK[0-9]``                              ISC Kea hook definition (JSON Base64 encoded)
``ONEAPP_VNF_DHCP4_LEASE_DATABASE``                         ISC Kea database definition (JSON Base64 encoded)
============================================ ============== ===========

.. important::

   Virtual Routers interface in the OpenNebula doesn't support managing `Network Interface Aliases <https://docs.opennebula.io/stable/operation/vm_management/vm_templates.html#network-interfaces-alias>`_. Aliases can be used only when appliance is used as regular Virtual Machine.

For more information continue to VNF :ref:`DHCP4 <vnf_dhcp4>` documentation.

.. _vnf_dns_context_param:

Function DNS
~~~~~~~~~~~~

Function implements DNS service. It can delegate requests to upstream servers based on network contextualization or directly resolve requests on its own.

**Basic parameters**:

=====================================  ============== ===========
Parameter                              Default        Description
=====================================  ============== ===========
``ONEAPP_VNF_DNS_ENABLED``             ``NO``         Enable/disable DNS function (``YES``/``NO``)
``ONEAPP_VNF_DNS_INTERFACES``          all ifaces     List of :ref:`interfaces <vnf_interfaces>` to listen on (``<[!]ethX> ...``
``ONEAPP_VNF_DNS_MAX_CACHE_TTL``       ``3600``       Maximum caching time (seconds)
``ONEAPP_VNF_DNS_USE_ROOTSERVERS``     ``YES``        Use root name servers directly (``YES``/``NO``)
``ONEAPP_VNF_DNS_NAMESERVERS``                        List of upstream NSs to forward queries to (``<IP>[@<PORT>] ...``)
``ONEAPP_VNF_DNS_UPSTREAM_TIMEOUT``    ``1128``       Upstream NS connection timeout (milliseconds)
=====================================  ============== ===========

**Advanced parameters:**

=====================================  ============== ===========
Advanced parameter                     Default        Description
=====================================  ============== ===========
``ONEAPP_VNF_DNS_CONFIG``                             Unbound server configuration (Base64 encoded)
``ONEAPP_VNF_DNS_ALLOWED_NETWORKS``                   Client networks from which is allowed to make queries (``<network>/<prefix> ...``)
``ONEAPP_VNF_DNS_TCP_DISABLED``        ``NO``         Enable/disable service over TCP (``YES``/``NO``)
``ONEAPP_VNF_DNS_UDP_DISABLED``        ``NO``         Enable/disable service over UDP (``YES``/``NO``)
=====================================  ============== ===========

For more information continue to VNF :ref:`DNS <vnf_dns>` documentation.

.. _vnf_nat4_context_param:

Function NAT4
~~~~~~~~~~~~~

Function implements IPv4 Network Address Translation (Masquerade) service for the connected interfaces (except management), through the specified outgoing interface.

===================================== ============== ===========
Parameter                             Default        Description
===================================== ============== ===========
``ONEAPP_VNF_NAT4_ENABLED``           ``NO``         Enable/disable NAT function (``YES``/``NO``)
``ONEAPP_VNF_NAT4_INTERFACES_OUT``     none          **Mandatory:** Outgoing :ref:`interface <vnf_interfaces>` for NAT (``<[!]ethX> ...``)
===================================== ============== ===========

For more information confinue to VNF :ref:`NAT4 <vnf_nat4>` documentation.

.. _vnf_router4_context_param:

Function ROUTER4
~~~~~~~~~~~~~~~~

Function implements routing among connected network interfaces.

===================================== ============== ===========
Parameter                             Default        Description
===================================== ============== ===========
``ONEAPP_VNF_ROUTER4_ENABLED``        ``NO``         Enable/disable Router function (``YES``/``NO``)
``ONEAPP_VNF_ROUTER4_INTERFACES``     all ifaces     List of routed :ref:`interfaces <vnf_interfaces>` (``<[!]ethX> ...``)
===================================== ============== ===========

For more information continue to VNF :ref:`Router4 <vnf_router4>` documentation.

.. _vnf_vrouter_context_param:

OpenNebula Virtual Router
-------------------------

Function implements another routing mechanism among connected network interfaces, but integrated with OpenNebula Virtual Router feature. In addition to all contextualization parameters above, the VNF appliance recognizes also the following variables passed by the OpenNebula when running over Virtual Router (VR) interface.

.. _vnf_legacy_vrouter_context_param:

===================================== ============== ===========
Parameter                             Default        Description
===================================== ============== ===========
``VROUTER_KEEPALIVED_ID``             ``1``          Global VR ID (1-255)
``VROUTER_KEEPALIVED_PASSWORD``       (empty)        Global VR password (max 8 characters)
``ETHx_VROUTER_IP``                   (empty)        Floating IPv4 (VIP) for ethX
``ETHx_VROUTER_MANAGEMENT``           ``NO``         Set ethX a management interface (``YES``/``NO``)
===================================== ============== ===========

.. important::

    No VNFs, routing or Keepalived services are ever started on **management interfaces** (``ETHx_VROUTER_MANAGEMENT``) except the default SSH server.

.. _vnf_new_vrouter_context_param:

..
    TODO: I don't know how to fit this new VIP parameters into the documentation.
    Using ONEAPP_VROUTER prefix doesn't fit into ONEAPP_VNF_* convention above.

    **Additional parameters** (not automatically set by OpenNebula):

    ===================================== ============== ===========
    Parameter                             Default        Description
    ===================================== ============== ===========
    ``ONEAPP_VROUTER_ETHx_VIP<0-9>``      (empty)        Extra floating IPv4 (VIPs) for ethX
    ===================================== ============== ===========

    .. note::

       ``ONEAPP_VROUTER_ETHx_VIP<0-9>`` **must always have index** in suffix, e.g. ``ONEAPP_VROUTER_ETH0_VIP0`` or ``ONEAPP_VROUTER_ETH0_VIP1``.

.. _vnf_keepalived_context_param:

Keepalived
----------

`Keepalived <https://www.keepalived.org/>`_ is an open-source project implementing `VRRP protocol <https://en.wikipedia.org/wiki/Virtual_Router_Redundancy_Protocol>`_ and a pre-installed appliance service which provides high-availability to other VNFs running on the instance. It's not a self-contained and directly usable service. In case of incident, it's able to migrate the floating IP (VIP) addresses and services to another instance with as little downtime as possible.

.. note::

    OpenNebula **Virtual Router** interface transparently integrates with the Keepalived function to provide high-availability when multiple router instances are started. When running appliances as **Virtual Router** (over VR specific :ref:`context parameters <vnf_legacy_vrouter_context_param>`), it's not necessary to directly deal with following context parameters.

.. important::

    You need to have at least one **VNF** enabled and at least one **floating IP** (VIP) configured.

**Basic parameters**:

===================================== ============== ===========
Parameter                             Default        Description
===================================== ============== ===========
``ONEAPP_VNF_KEEPALIVED_ENABLED``     ``NO``         Enable/disable Keepalived function (``YES``/``NO``)
``ONEAPP_VNF_KEEPALIVED_INTERFACES``  all ifaces     List of managed :ref:`interfaces <vnf_interfaces>` (``<[!]ethX> ...``)
``ONEAPP_VNF_KEEPALIVED_PASSWORD``    (empty)        Global VR password (max 8 characters)
``ONEAPP_VNF_KEEPALIVED_PRIORITY``    ``100``        Global VR numerical priority
``ONEAPP_VNF_KEEPALIVED_VRID``        ``1``          Global VR ID (1-255)
``ONEAPP_VNF_KEEPALIVED_INTERVAL``    ``1``          Global advertising interval (seconds)
===================================== ============== ===========

**Advanced parameters:**

========================================== ============== ===========
Parameter                                  Default        Description
========================================== ============== ===========
``ONEAPP_VNF_KEEPALIVED_ETHx_PASSWORD``    (empty)        VR password for ethX (max 8 characters)
``ONEAPP_VNF_KEEPALIVED_ETHx_PRIORITY``    ``100``        VR numerical priority for ethX
``ONEAPP_VNF_KEEPALIVED_ETHx_VRID``        ``1``          VR ID for ethX (1-255)
``ONEAPP_VNF_KEEPALIVED_ETHx_INTERVAL``    ``1``          Advertising interval for ethX (seconds)
========================================== ============== ===========

The fundamental part is the floating IP address (VIP), which must be always configured, otherwise function won't work. Also, the function must be provided with **Virtual Router ID** (``VRID``) unique for the subnets instance runs on (otherwise different VRs with same ``VRID`` could try to join into the single same cluster and both fail terribly)!

.. warning::

    **Known Issue:** Multiple quick consecutive reconfigurations of VM/VR (e.g., hot-attaching several NICs) without a proper delay might break a Keepalived cluster! You should always wait and verify that the instance is in the desired state after each change.

.. _vnf_interfaces:

Interfaces
----------

Each VNF has **interfaces** context parameter which defines on which network interfaces the service is or isn't active . If no interfaces context variable is provided (or is empty), the enabled VNF is listening on all available interfaces (except loopback ``lo`` and :ref:`OpenNebula Virtual Router <vnf_legacy_vrouter_context_param>` management interfaces).

.. note::

   **NAT4 VNF**: A special case of NAT4 VNF uses a context parameter ``ONEAPP_VNF_NAT4_INTERFACES_OUT`` (with ``_OUT`` suffix) to emphasize the actual place where the network address translation will happen (outgoing/external interface). This parameter doesn't provide any default. If it's not specified, the NAT4 won't select any outgoing interface automatically and service won't work. For NAT4 the parameter ``ONEAPP_VNF_NAT4_INTERFACES_OUT`` is **always mandatory**.

The listed interface names must follow the naming of the interfaces in the OpenNebula (see `context parameters <http://docs.opennebula.io/stable/operation/references/template.html#context-section>`__). Always use ``eth`` interface names followed by index starting from 0, i.e. ``eth0`` for first NIC, ``eth4`` for fifth NIC. The real interface names in the running VR/VM might differ - have a different prefixes (e.g., ``enoX``, ``ensX``, ``enpXsY``) following Consistent Network Device Naming or even different interface index(!). Appliance scripts **automatically translates OpenNebula interface names from context into real instance names**.

Extra interfaces specified in context variables, but missing in the instance, are **ignored**.

Example: To enable :ref:`DNS VNF <vnf_dns_context_param>` only on 3rd and 4th network interfaces, set the corresponding context parameter and its value as follows. If the instance wouldn't have these interfaces (but only first two, i.e. ``eth0`` and ``eth``), the DNS VNF would not run on any interface.

.. code::

    ONEAPP_VNF_DNS_INTERFACES="eth2 eth3"

.. note::

   You can separate interfaces by spaces (``"eth2 eth3"``), commas (``"eth2,eth3"``) or semicolons (``"eth2;eth3"``).

Exclude Interfaces
~~~~~~~~~~~~~~~~~~

To disable VNF on particular interface, you can use one of the following syntax:

* **include**: name all interfaces where service should be enabled and skip the disabled interface(s)
* **exclude**: prefix disabled interface(s) with ``!`` (e.g, ``!eth0``), rest available interfaces will be included automatically

Example: To disable :ref:`DHCP4 VNF <vnf_dhcp4_context_param>` on 3rd (i.e., ``eth2``) from 5 interfaces, use any of the following styles:

.. code::

    ONEAPP_VNF_DHCP4_INTERFACES="eth0 eth1 eth3 eth4"
      or
    ONEAPP_VNF_DHCP4_INTERFACES="!eth2"

.. important::

   Mixing include and exclude syntaxes within a single parameter is conflicting and must be avoided (e.g., ``!eth0 eth1 eth2``). The semantic of exclude syntax is to automatically include all the rest available interfaces (except those excluded). If both syntaxes are still used, the include syntax has higher priority and excluded interfaces are not respected.

.. _vnf_list:

List of VNFs
============

.. _vnf_dhcp4:

DHCP4 - DHCPv4 Service
----------------------

See: :ref:`Contextualization Parameters <vnf_dhcp4_context_param>`

The VNF provides *Dynamic Host Configuration Protocol* (DHCPv4) service implemented by `ISC Kea <https://www.isc.org/kea/>`_ software suite.

.. warning::

    Be careful and always specify interfaces the DHCP service should operate on as you could negatively affect availability of other the devices and services running on the connected networks!

Enabled service without any other context parameters will run DHCP on all interfaces and their subnets. The context parameter ``ONEAPP_VNF_DHCP4_INTERFACES`` limits the functionality on particular interfaces. Firstly, it tells on which interface it will listen for DHCP requests. Secondly, it will determine for which subnets it will provide leases - it will auto-generate subnet lease configuration (if no ``ONEAPP_VNF_DHCP4_SUBNET[0-9]`` are provided).

The format of values in ``ONEAPP_VNF_DHCP4_INTERFACES`` can be

* ``ethX`` - interface name (e.g., ``eth0``)
* ``ethX/IP`` - interface name with IP address to pinpoint the listening address and subnet creation in case more than one IP address is assigned to the interface (e.g., ``eth0/192.168.1.1``)

Service by default provides configuration to the remote DHCP clients based on IP configuration of interfaces on which it's enabled via ``ONEAPP_VNF_DHCP4_INTERFACES``. Interfaces configuration is taken from static network `contextualization parameters <http://docs.opennebula.org/stable/operation/references/template.html#context-section>`__ (e.g., ``ETH0_GATEWAY``), not from run-time configuration of the particular interfaces on the instance. Settings can be overridden by similarly named DHCP VNF specific contextualization parameters per NIC or NIC alias, e.g.:

.. code::

    CONTEXT=[
        ONEAPP_VNF_DHCP4_ETHx="<CIDR>:<start IP>-<end IP>",
        ONEAPP_VNF_DHCP4_ETHx_DNS="<IP> ...",
        ONEAPP_VNF_DHCP4_ETHx_GATEWAY="<IP> ...",
        ONEAPP_VNF_DHCP4_ETHx_MTU="<number>",
        ONEAPP_VNF_DHCP4_ETHx_ALIASy="<CIDR>:<start IP>-<end IP>",
        ONEAPP_VNF_DHCP4_ETHx_ALIASy_DNS="<IP> ...",
        ONEAPP_VNF_DHCP4_ETHx_ALIASy_GATEWAY="<IP> ...",
        ONEAPP_VNF_DHCP4_ETHx_ALIASy_MTU="<number>",
        ...
    ]

The context parameters for NIC aliases are applied only if the **subnet of NIC alias is unique** (i.e., no other interface uses the same subnet). Otherwise particular NIC alias configuration is ignored. Contextualization of main (non-aliased) interfaces always takes a priority over NIC aliases for the same subnet.

.. note::

    **Example:** Having VM NICs network parameters provided by OpenNebula contextualization

    * ``eth0``: ``192.168.0.1/255.255.0.0``
    * ``eth0`` alias 0: ``192.168.1.100/255.255.0.0``

    with following overrides within VNF DHCP4:

    .. code::

        CONTEXT=[
             ONEAPP_VNF_DHCP4_ETH0_DNS="8.8.8.8",
             ONEAPP_VNF_DHCP4_ETH0_ALIAS0_DNS="4.4.4.4",
             ONEAPP_VNF_DHCP4_ETH0_ALIAS0="192.168.0.0/16:192.168.100.100-192.168.200.250",
             ...
        ]

    In this case both the ``eth0`` and its alias share same subnet, but with two different overrides for nameserver option data and subnet pool (default vs. explicitly defined one). And so when VNF tries to create a DHCP4 configuration it encounters a conflict between the subnet pools and the option data (``DNS``, ``GATEWAY`` and ``MTU``) of the two. That is why the interface variables (``ONEAPP_VNF_DHCP4_ETH0``) will **always** take precedence in such scenarios (all ``ONEAPP_VNF_DHCP4_ETH0_ALIAS0`` will be ignored).

    For illustration the example of generated configuration for `ISC Kea <https://www.isc.org/kea/>`_ of the case above:

    .. code::

        ...
        "subnet4": [
        {
          "subnet": "192.168.0.0/16",
          "pools": [ { "pool": "192.168.0.2-192.168.255.254" } ],
          "option-data": [
            { "name": "domain-name-servers", "data": "8.8.8.8" },
            { "name": "routers", "data": "192.168.0.1" }
          ],
          "reservations": [
            { "flex-id": "'DO-NOT-LEASE-192.168.101.1'", "ip-address": "192.168.0.1" },
            { "flex-id": "'DO-NOT-LEASE-192.168.101.100'", "ip-address": "192.168.1.100" }
          ],
          "reservation-mode": "all"
        },
        ...

For more tailored subnet configuration, you can use ``ONEAPP_VNF_DHCP4_SUBNET`` context variable(s) with **raw configuration** which is directly passed to the DHCP server and the value must be a valid Base64 encoded JSON configuration of `ISC Kea subnet4 section <https://kea.readthedocs.io/en/latest/arm/dhcp4-srv.html#configuration-of-ipv4-address-pools>`_. More subnet configuration variables can be specified and they must be suffixed with numerical index (e.g., ``ONEAPP_VNF_DHCP4_SUBNET0``). Subnets defined by these subnet context parameters always **take precedence** over other interface specific parameters (i.e., existence of ``ONEAPP_VNF_DHCP4_SUBNET`` disables any contextualization based on interface configuration). Subnet definitions **must be unique**, although it's possible to have overlapping subnets - in such (or other non-trivial) setup consult the `ISC Kea documentation <https://kea.readthedocs.io/en/latest/arm/dhcp4-srv.html>`_ to better understand how DHCP lease will work.

.. warning::

   The appliance allows (live) **reconfiguration** and adapts to the changes in context parameters as they happen. Known issue of this process is that some variables defined in the past might still remain active if they are removed! E.g., a problem could arise if ``ONEAPP_VNF_DHCP4_SUBNET``-like variable was defined, but now you wish to use dynamic per interface variables instead (``ONEAPP_VNF_DHCP4_ETHx``-like), remove the former variable, but it still remains in the appliance configuration to a certain extent. A workaround is not to remove old variable, but instead provide the empty content, e.g. ``ONEAPP_VNF_DHCP4_SUBNET0=""``.

   **IMPORTANT:** It's recommended not to delete once used context variables, but set their content to empty string. You can remove them safely delete it after a next full recontextualization (or reboot).

To have full control over the DHCPv4 VNF, you can provide the complete ISC Kea configuration via ``ONEAPP_VNF_DHCP4_CONFIG`` contextualization parameter. It must be a valid Base64 encoded JSON configuration file following the `documentation <https://kea.readthedocs.io/en/latest/arm/dhcp4-srv.html>`_ and as required by ISC Kea service.

OpenNebula MAC to IPv4 Translation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the OpenNebula, there is clear relation between MAC (Hardware) and IPv4 addresses allocated for the Virtual Machine NICs. The MAC address for particular NIC is constructed as a concatenation of

- ``02:00`` (default) prefix followed by
- hexadecimal representation of the allocated IPv4 address ``01:02:03:04`` (e.g., for ``1.2.3.4``)

The leading prefix can be configured in OpenNebula via ``MAC_PREFIX`` option in `oned.conf <https://docs.opennebula.org/stable/deployment/references/oned_conf.html#virtual-networks>`_ (but then must be aligned with prefix configured for DHCPv4 VNF via ``ONEAPP_VNF_DHCP4_MAC2IP_MACPREFIX``)

The DHCPv4 VNF comes with **translation hook** (custom service plugin), which computes and offers the suitable IPv4 addresses just based on the MAC address same way as the OpenNebula does. It ensures that VM gets same IPv4 addresses via dynamic configuration (DHCP) as it would get through static contextualization (i.e., parameters on contextualization CD-ROM/service). This allows networking to unmodified VMs, which are not aware of the OpenNebula static contextualization.

.. note::

    The OpenNebula `MAC to IP translation <https://github.com/OpenNebula/addon-kea-hooks>`_ hook for ISC Kea works completely offline, without need to directly query the OpenNebula front-end. It's enabled by default and can be disabled via context parameter ``ONEAPP_VNF_DHCP4_MAC2IP_ENABLED="NO"``.

Translation hook can be restricted to work only on selected subnets via ``ONEAPP_VNF_DHCP4_MAC2IP_SUBNETS`` parameter, which accepts list of ranges in CIDR notation. For all the rest subnets and pools not covered in this parameter, the normal ISC Kea's lease behaviour is applied. Missing or empty parameter defaults to translation hook work on all subnets.

.. important::

   On subnets managed by OpenNebula MAC to IP translation hook (by default all), the requests from MAC addresses which can't be converted to suitable IP address are ignored!

If DHCP VNF is provided to both OpenNebula (via MAC to IP) and custom addressed clients, it must be either set ``ONEAPP_VNF_DHCP4_MAC2IP_SUBNETS`` or translation hook disabled completely (``ONEAPP_VNF_DHCP4_MAC2IP_ENABLED="NO"``). Otherwise, you could encounter issues with failed DHCP requests for all non-OpenNebula addressed DHCP clients.

.. _vnf_nat4:

NAT4 - IPv4 Network Address Translation
---------------------------------------

See: :ref:`Contextualization Parameters <vnf_nat4_context_param>`

The VNF provides **IPv4 Network Address Translation** (Masquerade) to connected interfaces over a specific outgoing interface. The outgoing interface **always must be set** in ``ONEAPP_VNF_NAT4_INTERFACES_OUT``, the default is empty list and with empty list the function won't work even if it's enabled. This is differs from how other functions deal with interface lists (where default empty list means all interfaces).

.. _vnf_router4:

IPv4 Router (ROUTER4)
---------------------

See: :ref:`Contextualization Parameters <vnf_router4_context_param>`

The VNF provides *routing* functionality among different networks and allows the Virtual Machines from different networks to talk to each other. If enabled (``ONEAPP_VNF_ROUTER4_ENABLED``) and by default, the routing is done among all connected interfaces (except management), but can be limited only to interfaces specified via ``ONEAPP_VNF_ROUTER4_INTERFACES``. The function is implemented on Linux kernel level by enabling IP packages forwarding on the selected network interfaces.

.. important::

  This function provides only routing. Function must be combined with :ref:`NAT4 <vnf_nat4>` VNF if you want to, e.g. give your private network clients access to the public Internet services.

.. _vnf_dns:

DNS Recursor (DNS)
------------------

See: :ref:`Contextualization Parameters <vnf_dns_context_param>`

The VNF provides *DNS recursor* service implemented by `Unbound <https://nlnetlabs.nl/documentation/unbound/>`_ server. Introduces the DNS resolving functionality for network clients, which cannot access the Internet or other locally configured name server directly. The VNF by default uses DNS **root servers** to resolve the requests on its own. It can also forward queries (when ``ONEAPP_VNF_DNS_USE_ROOTSERVERS="NO"``) to another configured name servers (set in ``ONEAPP_VNF_DNS_NAMESERVERS`` or auto-configured from your virtual networks).

.. note::

   In forward-only behaviour the auto-configurated settings usually **IS NOT** desired, don't forget to specify the upstream DNS name servers, e.g.:

    .. code::

        CONTEXT=[
            ONEAPP_VNF_DNS_NAMESERVERS="8.8.8.8, 8.8.4.4",
            ...
        ]

Service can restricted only to work on particular network interfaces via ``ONEAPP_VNF_DNS_INTERFACES``. Apart from described syntax to list the :ref:`interfaces <vnf_interfaces>` to include or exclude (``ethX``, ``!ethX``), in this VNF it's extended to also cover optional listening IP and port on the particular interface (with syntax ``ethX/IP[@port]``), e.g.:

.. code::

        CONTEXT=[
            ONEAPP_VNF_DNS_INTERFACES="eth0, eth1/10.0.0.1, eth2/192.168.0.1@53",
            ...
        ]

.. important::

   Beware of running the DNF VNF on any of your Internet-facing interfaces! It might become DNS recursor for the whole Internet and be a victim of some form of `Denial-of-Service Attack <https://en.wikipedia.org/wiki/Denial-of-service_attack>`_!

Function might be disabled to work over TCP (via ``ONEAPP_VNF_DNS_TCP_DISABLED="YES"``) or UDP (via ``ONEAPP_VNF_DNS_UDP_DISABLED="YES"``) protocols. But, it's not generally recommended disable UDP protocol as many public name server using UDP exclusively.

To have full control over DNS VNF, you can provide the complete Unbound configuration file via ``ONEAPP_VNF_DNS_CONFIG`` contextualization parameter. It must a Base64 encoded string with valid `unbound.conf <https://nlnetlabs.nl/documentation/unbound/unbound.conf/>`_ content.

.. _vnf_tutorials:

Tutorials
=========

In this section we will demonstrate how this appliance can be used - but there is a need for a few prerequisities. VNF appliance is useful only when there are networks involved so we will create a couple of virtual networks in OpenNebula - if you do not know how to create them then peek into `the vnet documentation <https://docs.opennebula.org/stable/operation/network_management/manage_vnets.html>`_.

============ ================ ================================= =============== ===============
Network name Subnet           Range                             Gateway         DNS
============ ================ ================================= =============== ===============
public       192.168.150.0/24 192.168.150.100 - 192.168.150.199 192.168.150.1
vnet_a       192.168.101.0/24 192.168.101.100 - 192.168.101.199 192.168.101.111 192.168.101.111
vnet_b       192.168.102.0/24 192.168.102.100 - 192.168.102.199 192.168.102.111 192.168.102.111
vnet_mgt     192.168.103.0/24 192.168.103.100 - 192.168.103.199 192.168.103.111 192.168.103.111
============ ================ ================================= =============== ===============

.. important::

   Before you start with the tutorials, please, ensure that you followed the steps as was described in the :ref:`Quick Start <service_vnf_quick_start>`. Mainly that you downloaded the VNF image and prepared the template by setting up the password or ssh key or both.

.. _vnf_tutorial_vnf:

VNF appliance as VM
-------------------

This demo creates a VNF VM which will serve as a router with a NAT and as a DHCP server and DNS. It will have access to the internet via public/external network where NAT will happen and it will be attached to other two different local subnets where it will provide DHCP and DNS service. Plus it will forward packets between these networks and it will provide access to the internet.

We start with instantiation of the VNF appliance - no network is attached yet. The provided contextualization can be seen in this picture:

|image-vnf-demo-vm1|

.. _vnf_tutorial_vnf_explanation:

.. note::

   **Explanation**: We don't want to provide DHCP and DNS recursor service to the internet so we define interfaces for these VNF with a value of: "``!eth0``". For the NAT we must be explicit and we want to translate all outgoing traffic on the public/internet interface so that is why we set NAT interfaces to: "``eth0``". For the router service we want to forward packets between all the interfaces - so the clients in both the local networks can talk to each other and to the internet. For that we can leave it empty because that is the default when the VNF is enabled.

   The rest of the context is unchanged - it is left with defaults.

Click the **Instantiate** button and wait a minute. You should also be able to login inside and look around. There is only loopback interface so all enabled VNFs are just idle.

Let us attach three virtual networks and see what happens:

* ``public``
* ``vnet_a``
* ``vnet_b``

First we will attach ``public`` vnet - that will be our ``eth0``. It is an internet facing NIC through which we can reach public services like root DNS servers.

Click on the instantiated VM and then **Network → Attach nic → public → Attach**.

If you are logged in the VM you can tail the appliance log::

       ___   _ __    ___
      / _ \ | '_ \  / _ \   OpenNebula Service Appliance
     | (_) || | | ||  __/
      \___/ |_| |_| \___|

    All set and ready to serve 8)

   localhost:~# tail -f /var/log/one-appliance/ONE_configure.log

You should see some activity there and the final lines should resemble::

   ...
   [Tue Mar 17 05:13:15 UTC 2020] => INFO: Save context/config variables as a report in: /etc/one-appliance/config
   [Tue Mar 17 05:13:15 UTC 2020] => INFO: --- CONFIGURATION FINISHED ---

Now we will attach ``vnet_a`` and ``vnet_b`` in exactly the same manner - you must just wait until the configuration is **twice** more finished...

Our VM has altogether these IP addresses:

* ``192.168.150.102/24``
* ``192.168.101.100/24``
* ``192.168.102.100/24``

You can verify that all have been configured by checking the report config file: ``/etc/one-appliance/config``

There you should see all three interfaces::

   ...
   ONEAPP_VROUTER_ETH0_IP = '192.168.150.102'
   ...
   ONEAPP_VROUTER_ETH1_IP = '192.168.101.100'
   ...
   ONEAPP_VROUTER_ETH2_IP = '192.168.102.100'
   ...

For better understanding of the current state of the appliance you can checkout these config files:

* **DHCP4**: ``/etc/kea/kea-dhcp4.conf``
* **DNS**: ``/etc/unbound/unbound.conf``
* **ROUTER4**: ``/etc/sysctl.d/01-one-router4.conf``
* **NAT**: ``/etc/iptables/nat4-rules-enabled``

We are still not done yet. DHCP4 and DNS are configured and running but DHCP does not provide the right options in the reply (it sends IP addresses preloaded from vnet options instead of its own).

We must update the context to fix this problem: click again on the VM in OpenNebula's Sunstone and **Conf → Update Configuration → Context → Custom vars**

And at the bottom is a "**plus**" button - click on it and create a couple of new context variables:

=================================== ===============
Variable name                       Value
=================================== ===============
ONEAPP_VNF_DHCP4_ETH1_DNS           192.168.101.100
ONEAPP_VNF_DHCP4_ETH1_GATEWAY       192.168.101.100
ONEAPP_VNF_DHCP4_ETH2_DNS           192.168.102.100
ONEAPP_VNF_DHCP4_ETH2_GATEWAY       192.168.102.100
=================================== ===============

And apply these by clicking on **Update** button at the top.

You can see that we configured IP address ``192.168.101.100`` and ``192.168.102.100`` as router and nameserver option to our DHCP clients (you can verify that in ``/etc/kea/kea-dhcp4.conf``).

.. note::

   Please, bear in mind that we are demoing a concrete environment - if yours differs from ours the adjust this demo accordingly...

Now we should be fine - but maybe our OpenNebula is deployed in a restricted environment and strangely DNS resolving does not work...::

   localhost:~# ping opennebula.org
   ping: bad address 'opennebula.org'
   localhost:~#

Maybe the local admin forbid DNS queries to DNS root servers - in that case let us reconfigure our DNS VNF to use forward zone.

.. note::

   If your VNF appliance resolves fine then you can skip this step.

Another context update (**Conf → Update Configuration → Context → Custom vars**):

=================================== ===============
Variable name                       Value
=================================== ===============
ONEAPP_VNF_DNS_USE_ROOTSERVERS      NO
ONEAPP_VNF_DNS_NAMESERVERS          8.8.8.8, 8.8.4.4
=================================== ===============

And **Update**.

Wait few seconds for ``configuration finished`` and verify the config like this::

   localhost:~# tail -n 5 /etc/unbound/unbound.conf
   forward-zone:
       name: "."
       forward-addr: 8.8.8.8
       forward-addr: 8.8.4.4

   localhost:~#

Does the ping works now?::

   localhost:~# ping opennebula.org
   PING opennebula.org (173.255.245.62): 56 data bytes
   64 bytes from 173.255.245.62: seq=0 ttl=54 time=158.173 ms
   64 bytes from 173.255.245.62: seq=1 ttl=54 time=158.410 ms
   64 bytes from 173.255.245.62: seq=2 ttl=54 time=158.268 ms
   ^C
   --- opennebula.org ping statistics ---
   3 packets transmitted, 3 packets received, 0% packet loss
   round-trip min/avg/max = 158.173/158.283/158.410 ms
   localhost:~#

Great!

We are done, but as a final test we instantiate two more VMs (e.g.: `Alpine appliance <http://marketplace.opennebula.org/appliance/193631c3-7082-4528-bfdb-31b2ecb3d9f5>`_)

We don't need anything fancy to configure there - just instantiate them along the instructions from the **Quick Start** (albeit with a different image!) and attach a network interface to both of them:

* one will get: ``vnet_a``
* the other: ``vnet_b``

Login to the one connected to ``vnet_a``::

   localhost:~# udhcpc
   udhcpc: started, v1.31.1
   udhcpc: sending discover
   udhcpc: sending select for 192.168.101.101
   udhcpc: lease of 192.168.101.101 obtained, lease time 3600
   localhost:~# cat /etc/resolv.conf
   nameserver 192.168.101.100
   localhost:~# ip r
   default via 192.168.101.100 dev eth0 metric 202
   192.168.101.0/24 dev eth0 proto kernel scope link src 192.168.101.101
   localhost:~#

And similarly with the other on ``vnet_b``::

   localhost:~# udhcpc
   udhcpc: started, v1.31.1
   udhcpc: sending discover
   udhcpc: sending select for 192.168.102.101
   udhcpc: lease of 192.168.102.101 obtained, lease time 3600
   localhost:~# cat /etc/resolv.conf
   nameserver 192.168.102.100
   localhost:~# ip r
   default via 192.168.102.100 dev eth0 metric 202
   192.168.102.0/24 dev eth0 proto kernel scope link src 192.168.102.101
   localhost:~#

Both of these network clients (on different subnets) should be able to talk between each other (e.g.: ``ping``) and should be able to resolve DNS and reach internet services.

**Congratulations with your first successful VNF appliance deployment!**

.. _vnf_tutorial_vrouter:

VNF appliance as VRouter
------------------------

This tutorial will be a VRouter version of the previous one - the result will differ only in the fact that the VNF appliance will be deployed in HA mode with Keepalived service running. To learn more visit `the VRouter documentation <https://docs.opennebula.org/stable/operation/network_management/vrouter.html>`_.

Instead of **Service VNF** template under **Templates → VMs** we will use **Service Virtual Router** under **Templates → Virtual Routers**.

Our context will look like this:

|image-vnf-demo-vrouter1|

You will not be able instantiate the VRouter until you fill the name explicitly:

|image-vnf-demo-vrouter2|

.. note::

   **Explanation**: The context is almost the same as was with :ref:`the previous demo <vnf_tutorial_vnf_explanation>` the biggest differences here are the missing **router** option (it is always enabled in VRouter) and that VRouter has a option **Number of VM instances** which we raised to two.

We wait for the VRouter's VMs and we can login to each of them and verify if everything was configured as expected. Although we did not attached any network yet so all enabled VNFs are idle. The goal is to attach these three virtual networks:

* ``public``
* ``vnet_a``
* ``vnet_b``

First we will attach ``public`` vnet - that will be our ``eth0``. It is an internet facing NIC through which we can reach public services like root DNS servers.

In the Sunstone pick **Instances → Virtual Routers** and click on your VRouter and then **Attach NIC → public → Attach**.

If you are logged in one of the VMs of your VRouter then you can tail the appliance log::

       ___   _ __    ___
      / _ \ | '_ \  / _ \   OpenNebula Service Appliance
     | (_) || | | ||  __/
      \___/ |_| |_| \___|

    All set and ready to serve 8)

   localhost:~# tail -f /var/log/one-appliance/ONE_configure.log

You should see some activity there and the final lines should resemble::

   ...
   [Tue Mar 17 08:04:52 UTC 2020] => INFO: Save context/config variables as a report in: /etc/one-appliance/config
   [Tue Mar 17 08:04:52 UTC 2020] => INFO: --- CONFIGURATION FINISHED ---

Now we will attach ``vnet_a`` and ``vnet_b`` but there will be one extra but crucial step in it...

Again pick **Instances → Virtual Routers** and click on your VRouter and then **Attach NIC**. Click on **vnet_a** and now we must fill in the textbox **Force IPv4** with value: ``192.168.101.111`` and tick the checkbox on the right: **Floating IP**. It should look like this:

|image-vnf-demo-vrouter3|

And hit **Attach**. We do this once more for ``vnet_b`` in which case you set **Force IPv4** as ``192.168.102.111`` and again enable **Floating IP**:

|image-vnf-demo-vrouter4|

Wait till both VRouter's VMs are configured (``/var/log/one-appliance/ONE_configure.log``) - don't forget that reconfiguration is triggered with each attached NIC - so before you continue be sure that you see **twice** more: "``CONFIGURATION FINISHED``" in the log and that one of the VM is **Master** (``/etc/keepalived/ha-check-status.sh``).

At that point both VRouter's VMs should have three interfaces with Keepalived running and one of them should have all VNFs up and ready and assigned all floating IPs. You can verify that by checking the report config file: ``/etc/one-appliance/config``

There you should see all three interfaces for VM1::

   ...
   ONEAPP_VROUTER_ETH0_IP = '192.168.150.102'
   ...
   ONEAPP_VROUTER_ETH1_IP = '192.168.101.100'
   ...
   ONEAPP_VROUTER_ETH2_IP = '192.168.102.100'
   ...

And for VM2::

   ...
   ONEAPP_VROUTER_ETH0_IP = '192.168.150.103'
   ...
   ONEAPP_VROUTER_ETH1_IP = '192.168.101.102'
   ...
   ONEAPP_VROUTER_ETH2_IP = '192.168.102.102'
   ...

.. note::

   The actual IP addresses in your case can differ of course - adjust this tutorial accordingly...

For better understanding of the current state of the appliance you can checkout these config files:

* **DHCP4**: ``/etc/kea/kea-dhcp4.conf``
* **DNS**: ``/etc/unbound/unbound.conf``
* **ROUTER4**: ``/etc/sysctl.d/01-one-router4.conf``
* **NAT**: ``/etc/iptables/nat4-rules-enabled``
* **KEEPALIVED**: ``/etc/keepalived/keepalived.conf``

The important thing is to have both floating IPs assigned - log into each of these VMs and checkout the situation::

   localhost:~# /etc/keepalived/ha-check-status.sh
   KEEPALIVED: RUNNING
   VRRP-INSTANCE(ETH0): MASTER
   VRRP-INSTANCE(ETH1): MASTER
   VRRP-INSTANCE(ETH2): MASTER
   SYNC-GROUP(vrouter): MASTER
   localhost:~# ip a | grep -e 192.168.101.111 -e 192.168.102.111
       inet 192.168.101.111/32 scope global eth1
       inet 192.168.102.111/32 scope global eth2
   localhost:~#

From this output you can see that this particular VM is the **Master** and has all floating IPs assigned and ready. Let's see how is the situation in the other VM::

   localhost:~# /etc/keepalived/ha-check-status.sh
   KEEPALIVED: RUNNING
   VRRP-INSTANCE(ETH0): BACKUP
   VRRP-INSTANCE(ETH1): BACKUP
   VRRP-INSTANCE(ETH2): BACKUP
   SYNC-GROUP(vrouter): BACKUP
   localhost:~# ip a | grep -e 192.168.101.111 -e 192.168.102.111
   localhost:~#

The other VM is now on standby.

We are done, but as a final test we instantiate two more VMs (e.g.: `Alpine appliance <http://marketplace.opennebula.org/appliance/193631c3-7082-4528-bfdb-31b2ecb3d9f5>`_)

We don't need anything fancy to configure there - just instantiate them along the instructions from the **Quick Start** (albeit with a different image!) and attach a network interface to both of them:

* one will get: ``vnet_a``
* the other: ``vnet_b``

Login to the one connected to ``vnet_a``::

   localhost:~# udhcpc
   udhcpc: started, v1.31.1
   udhcpc: sending discover
   udhcpc: sending select for 192.168.101.101
   udhcpc: lease of 192.168.101.101 obtained, lease time 3600
   localhost:~# cat /etc/resolv.conf
   nameserver 192.168.101.111
   localhost:~# ip r
   default via 192.168.101.111 dev eth0 metric 202
   192.168.101.0/24 dev eth0 proto kernel scope link src 192.168.101.101
   localhost:~#

And similarly with the other on ``vnet_b``::

   localhost:~# udhcpc
   udhcpc: started, v1.31.1
   udhcpc: sending discover
   udhcpc: sending select for 192.168.102.101
   udhcpc: lease of 192.168.102.101 obtained, lease time 3600
   localhost:~# cat /etc/resolv.conf
   nameserver 192.168.102.111
   localhost:~# ip r
   default via 192.168.102.111 dev eth0 metric 202
   192.168.102.0/24 dev eth0 proto kernel scope link src 192.168.102.101
   localhost:~#

Both of these network clients (on different subnets) should be able to talk between each other (e.g.: ``ping``) and should be able to resolve DNS and reach internet services.

**Congratulations with your first successful VROUTER appliance deployment!**

VRouter management
~~~~~~~~~~~~~~~~~~

VRouter has a option to attach a NIC which will have a special purpose - it will serve only as a entrypoint to the VRouter's VMs and no VNF and no service will be running on such interface. This interface we call **management** and here is how we can add it to our VRouter.

Our management interface will be connected to the reserved virtual network ``vnet_mgt`` which we have already defined at :ref:`the beginning of the tutorials <vnf_tutorials>`.

In the Sunstone pick **Instances → Virtual Routers** and click on your VRouter and then **Attach NIC → vnet_mgt**, tick the checkbox: **Management Interface** and follow with the final **Attach**.

|image-vnf-demo-vrouter5|

Again wait for all the VRouter's VMs to be configured and one of them voted as **Master**. You should see a new interface inside but nothing should be listening on it.

VRouter failover test
~~~~~~~~~~~~~~~~~~~~~

Now we verify that HA works as the last part of this tutorial.

Login to both of the network client VMs (``vnet_a`` and ``vnet_b``) and let them ping each other - that means on the VM1 (``vnet_a``)::

   localhost:~# ping 192.168.102.101

and on the VM2 (``vnet_b``)::

   localhost:~# ping 192.168.101.101

At this point they should ping each other without a problem - let them so. Now find which of the two VRouter's VMs is currently the master. Look up the Master VM (by IP address for example) in the Sunstone and click on it. Then in the top menu bar pick and click on **Reboot (hard)** - this will simulate failover.

Go back to your pinging VMs and you should see a short freeze and quick resume of pinging - that means failover successfully happened. To make it double sure login to the former Backup VM and check its state again (``/etc/keepalived/ha-check-status.sh``). It should be the Master now and the former Master VM should be soon up and ready as a new Backup.

.. |image-download| image:: /images/vnf/vnf-download.png
.. |image-context-vars| image:: /images/vnf/vnf-context-vars.png
.. |image-context-vars-vrouter| image:: /images/vnf/vnf-context-vars-vrouter.png
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
.. |image-vnf-demo-vm1| image:: /images/vnf/vnf-demo-vm1.png
.. |image-vnf-demo-vrouter1| image:: /images/vnf/vnf-demo-vrouter1.png
.. |image-vnf-demo-vrouter2| image:: /images/vnf/vnf-demo-vrouter2.png
.. |image-vnf-demo-vrouter3| image:: /images/vnf/vnf-demo-vrouter3.png
.. |image-vnf-demo-vrouter4| image:: /images/vnf/vnf-demo-vrouter4.png
.. |image-vnf-demo-vrouter5| image:: /images/vnf/vnf-demo-vrouter5.png
