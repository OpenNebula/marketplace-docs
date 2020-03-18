.. _service_vnf:

=====================
Service VNF (VRouter)
=====================

OpenNebula `Marketplace Appliance <http://marketplace.opennebula.systems/appliance/>`_ implementing `Virtual Router <http://docs.opennebula.org/stable/operation/network_management/vrouter.html>`_ and providing useful set of **Virtual Network Functions** - **VNFs** (aside from routing: DHCP, DNS recursor, NAT and HA via `Keepalived <https://www.keepalived.org/>`_ service).

.. note::

    This appliance supersedes the previous version of `Virtual Router <http://docs.opennebula.org/stable/operation/network_management/vrouter.html>`_ with which you may be already familiar with. The old **vrouter** template is still supported - you may just need to update the image to point to this newer version. You should do that even if you don't need the new functionality - this new improved version fixes many issues which you could encounter with the old vrouter (mainly during attach/detach of the network interfaces). The old vrouter will be referenced as the **legacy vrouter** from now on.

.. include:: shared/features-alpine.txt

.. _quick_start:

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <vnf_context_param>` parameters:

|image-context-vars|

.. note::

    You should enable at least one of the VNFs otherwise no service will be started - you can always enable/disable and modify the VNFs (services) anytime later via the **Conf → Update Configuration**.

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

Contextualization parameters provided in the Virtual Machine template controls the initial VM configuration. Except for the `common set <http://docs.opennebula.org/stable/operation/references/template.html#context-section>`_ of parameters supported by every appliance on the OpenNebula Marketplace, there are few specific to the particular service appliance. The parameters should be provided in the ``CONTEXT`` section of the Virtual Machine template, read the OpenNebula `Operation Guide <http://docs.opennebula.org/stable/operation/vm_setup/kvm.html#set-up-the-virtual-machine-template>`__ for more details.

.. note::

   VNF Service appliance and VRouter share the same image and support the same context parameters. The main difference is in usage: VNF service is used like any other appliance (or VM) but VRouter has its own set of features which are built into the Sunstone UI and you don't instantiate (VRouter's) VMs directly - that means it is operated little bit differently.

.. _vnfs_context_param:

VNFs context parameters
-----------------------

These behave as you would expect and works the same in both types of instantiation (as VM and as VRouter).

.. _vnf_dhcp4_context_param:

DHCP4
~~~~~

For more info jump below to :ref:`DHCP4 VNF section <vnf_dhcp4>`.

===================================== ========= ============== ========= ===========
Parameter                             Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_VNF_DHCP4_ENABLED``                    NO             configure Enable/Disable DHCP4 Service: `<boolean>`
``ONEAPP_VNF_DHCP4_INTERFACES``                 All interfaces configure Listening interfaces: `<[!]eth?> ...`
``ONEAPP_VNF_DHCP4_AUTHORITATIVE``              YES            configure Server authoritativity: `<boolean>`
``ONEAPP_VNF_DHCP4_LEASE_TIME``                 3600           configure DHCP lease time (in secs): `<number>`
``ONEAPP_VNF_DHCP4_DNS``                        Empty          configure Global nameservers option (provides default): `<ip> ...`
``ONEAPP_VNF_DHCP4_GATEWAY``                    Empty          configure Global routers option (provides default): `<ip> ...`
``ONEAPP_VNF_DHCP4_MAC2IP_ENABLED``             YES            configure Enable/Disable hook for MAC-to-IP DHCP lease: `<boolean>`
``ONEAPP_VNF_DHCP4_MAC2IP_MACPREFIX``           02:00          configure HW/MAC address prefix for MAC-to-IP hook: `<HEX>:<HEX>`
===================================== ========= ============== ========= ===========

===================================== ========= ============== ========= ===========
Advanced parameter                    Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_VNF_DHCP4_CONFIG``                     Empty          configure ISC Kea JSON configuration in base64: `<base64>`
``ONEAPP_VNF_DHCP4_<ETH?>``                     Empty          configure Explicit subnet and pool definition: `<CIDR>:<start>-<end>`
``ONEAPP_VNF_DHCP4_<ETH?>_DNS``                 Empty          configure Nameservers option for <ETH?> subnet definition: `<ip> ...`
``ONEAPP_VNF_DHCP4_<ETH?>_GATEWAY``             Empty          configure Routers option for <ETH?> subnet definition: `<ip> ...`
``ONEAPP_VNF_DHCP4_<ETH?>_MTU``                 Empty          configure MTU option for <ETH?> subnet definition: `<number>`
``ONEAPP_VNF_DHCP4_MAC2IP_SUBNETS``             Empty          configure List of subnets for MAC-to-IP hook: `<network>/<prefix> ...`
``ONEAPP_VNF_DHCP4_SUBNET[0-9]``                Empty          configure ISC Kea JSON subnet definition in base64: `<base64>`
``ONEAPP_VNF_DHCP4_HOOK[0-9]``                  Empty          configure ISC Kea JSON hook definition in base64: `<base64>`
``ONEAPP_VNF_DHCP4_LEASE_DATABASE``             Empty          configure ISC Kea JSON database definition in base64: `<base64>`
===================================== ========= ============== ========= ===========

.. _vnf_dns_context_param:

DNS
~~~

For more info jump below to :ref:`DNS VNF section <vnf_dns>`.

===================================== ========= ============== ========= ===========
Parameter                             Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_VNF_DNS_ENABLED``                      NO             configure Enable/Disable DNS Service: `<boolean>`
``ONEAPP_VNF_DNS_INTERFACES``                   All interfaces configure Listening interfaces: `<[!]eth?> ...`
``ONEAPP_VNF_DNS_MAX_CACHE_TTL``                3600           configure Maximum caching time (secs): `<number>`
``ONEAPP_VNF_DNS_USE_ROOTSERVERS``              YES            configure Use root name servers directly: `<boolean>`
``ONEAPP_VNF_DNS_NAMESERVERS``                  Empty          configure List of upstream nameservers to which forward queries: `<ip>[@<port>] ...`
``ONEAPP_VNF_DNS_UPSTREAM_TIMEOUT``             1128           configure Upstream nameservers connection timeout (msecs): `<number>`
===================================== ========= ============== ========= ===========

===================================== ========= ============== ========= ===========
Advanced parameter                    Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_VNF_DNS_CONFIG``                       Empty          configure Unbound configuration in base64: `<base64>`
``ONEAPP_VNF_DNS_ALLOWED_NETWORKS``             Empty          configure Client networks from which is allowed to make queries: `<network>/<prefix> ...`
``ONEAPP_VNF_DNS_TCP_DISABLED``                 False          configure Disable TCP protocol: `<boolean>`
``ONEAPP_VNF_DNS_UDP_DISABLED``                 False          configure Disable UDP protocol: `<boolean>`
===================================== ========= ============== ========= ===========

.. _vnf_nat4_context_param:

NAT4
~~~~

For more info jump below to :ref:`NAT4 VNF section <vnf_nat4>`.

===================================== ========= ============== ========= ===========
Parameter                             Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_VNF_NAT4_ENABLED``                     NO             configure Enable/Disable NAT Service: `<boolean>`
``ONEAPP_VNF_NAT4_INTERFACES_OUT``              None           configure NATed interfaces: `<[!]eth?> ...`
===================================== ========= ============== ========= ===========

.. _vnf_keepalived_context_param:

KEEPALIVED
~~~~~~~~~~

For more info jump below to :ref:`KEEPALIVED VNF section <vnf_keepalived>`.

.. note::

   If you wish to use Keepalived then it would probably be better to utilize it via VRouter feature than as VNF VM.

.. important::

   These Keepalived context parameters are not enough to be of any use by themselves - to utilize Keepalived fully you must also configure at least one **Virtual IP** (VIP - floating IP). That can be done via :ref:`VRouter context variable <vnf_new_vrouter_context_param>` `ONEAPP_VROUTER_<ETH?>_VIP<0-9>` or by running VNF appliance as VRouter and not as just VM.

===================================== ========= ============== ========= ===========
Parameter                             Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_VNF_KEEPALIVED_ENABLED``               NO             configure Enable/Disable Keepalived Service: `<boolean>`
``ONEAPP_VNF_KEEPALIVED_INTERFACES``            All interfaces configure Managed interfaces: `<[!]eth?> ...`
``ONEAPP_VNF_KEEPALIVED_PASSWORD``              Empty          configure Global VRouter password (max 8 characters): `<password>`
``ONEAPP_VNF_KEEPALIVED_INTERVAL``              1              configure Global advertising interval (secs): `<number>`
``ONEAPP_VNF_KEEPALIVED_PRIORITY``              100            configure Global VRouter priority: `<number>`
``ONEAPP_VNF_KEEPALIVED_VRID``                  1              configure Global vrouter id (1-255): `<1-255>`
===================================== ========= ============== ========= ===========

========================================== ========= ============== ========= ===========
Advanced parameter                         Mandatory Default        Stage     Description
========================================== ========= ============== ========= ===========
``ONEAPP_VNF_KEEPALIVED_<ETH?>_PASSWORD``            Empty          configure VRouter password for <ETH?> (max 8 characters): `<password>`
``ONEAPP_VNF_KEEPALIVED_<ETH?>_INTERVAL``            1              configure Advertising interval for <ETH?> (secs): `<number>`
``ONEAPP_VNF_KEEPALIVED_<ETH?>_PRIORITY``            100            configure VRouter priority for <ETH?>: `<number>`
``ONEAPP_VNF_KEEPALIVED_<ETH?>_VRID``                1              configure vrouter id (1-255) for <ETH?>: `<1-255>`
========================================== ========= ============== ========= ===========

.. _vnf_router4_context_param:

ROUTER4
~~~~~~~

For more info jump below to :ref:`ROUTER4 VNF section <vnf_router4>`.

===================================== ========= ============== ========= ===========
Parameter                             Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_VNF_ROUTER4_ENABLED``                  NO             configure Enable/Disable routing Service: `<boolean>`
``ONEAPP_VNF_ROUTER4_INTERFACES``               All interfaces configure Forwarding interfaces: `<[!]eth?> ...`
===================================== ========= ============== ========= ===========

.. _vnf_vrouter_context_param:

VRouter context parameters
--------------------------

In addition to all above VNF appliance recognizes also the following variables:

.. _vnf_legacy_vrouter_context_param:

Legacy VRouter
~~~~~~~~~~~~~~

If used as VRouter these are supported in Sunstone and filled automatically:

===================================== ========= ============== ========= ===========
Parameter                             Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``VROUTER_KEEPALIVED_ID``                       1              configure Global vrouter id (1-255): `<1-255>`
``VROUTER_KEEPALIVED_PASSWORD``                 Empty          configure Global VRouter password (max 8 characters): `<password>`
``<ETH?>_VROUTER_IP``                           Empty          configure Floating IPv4 (VIP) for <ETH?>: `<ip>`
``<ETH?>_VROUTER_MANAGEMENT``                   False          configure Management interface <ETH?>: `<boolean>`
===================================== ========= ============== ========= ===========

.. important::

   If you set an interface as **management** (`<ETH?>_VROUTER_MANAGEMENT`) then no VNF no service will be enabled or working on this interface!

.. _vnf_new_vrouter_context_param:

New VRouter
~~~~~~~~~~~

These are not yet built into Sunstone UI:

===================================== ========= ============== ========= ===========
Parameter                             Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_VROUTER_<ETH?>_VIP<0-9>``              Empty          configure Extra floating IPv4 (VIPs) for <ETH?>: `<ip>`
===================================== ========= ============== ========= ===========

.. note::

   `ONEAPP_VROUTER_<ETH?>_VIP<0-9>` **has to** have numbered suffix like `ONEAPP_VROUTER_ETH0_VIP0` or `ONEAPP_VROUTER_ETH0_VIP1` but it will not work without it: `ONEAPP_VROUTER_<ETH?>_VIP` (**WRONG**) because this is reserved for internal usage.

Interfaces
----------

Each VNF has **interfaces** context parameter which defines on which network interfaces is the service active (or where cannot be active via "`!`" negation prefix). Generally if this interfaces context variable is empty (no value is provided) then it means that the **enabled** VNF in question is listening on all interfaces (except "`lo`" - loopback which either does not make sense or must be provided explicitly in all other cases).

.. note::

   In the case of **NAT4** VNF there is an extra `_OUT` suffix to emphasize the actual place where the network address translation will happen (out-going/external interface). Also it leaves a room for the potential future release where `_IN` suffix could be used for further fine-tunning.

   Another difference from other VNFs is that empty `ONEAPP_VNF_NAT4_INTERFACES_OUT` does not implicitly mean to work on all interfaces but on the contrary - it will not be enabled anywhere. In another words - in the case of **NAT4** VNF you must be always explicit!

You must reference the interfaces as `eth?` starting from `eth0` which is the first non-loopback network interface. The name can differ inside the VMs but the appliance will automatically take care of the conversion. So as a user you are only interested in **eth** naming scheme: `eth0` means first NIC, `eth1` the second, etc.

For example: you may wish to enable **DNS recursor** service on only the third and the fourth interface: `eth2 eth3`

.. note::

   You can separate interfaces by spaces, commas or semicolons.

What happens if there is not enough attached interfaces? The extraneous interfaces are simply ignored. In our example above it would mean that if we have only two interfaces (`eth0` and `eth1`) then DNS service would not listen anywhere. If we would have three (`eth0`, `eth1` and `eth2`) then DNS would listen on only one (`eth2`).

Another issue can be when you have a VNF/VRouter with many attached interfaces but you wish to enable service on most of them except a few. You have two options:

* explicitly name all the interfaces where the service should be enabled
* use the **negated interface** syntax

For example: we have a VNF instance with a dozen of interfaces attached (or we intend to attach in the future) and we wish to provide a **DHCP4** service on all of them except the first two interfaces. We could tediously write down all desired interfaces as: `eth2, eth3, eth4, eth5, ...` up to `eth11` or...

...we could just write: `!eth0 !eth1`

and don't care about the actual count of attached interfaces.

.. note::

   Using negated interface syntax with normal interface syntax will not have much sense: "`!eth0 eth1 eth2`" is in a conflict because it basically tries to say: "listen on all interfaces except `eth0` **AND** listen only on `eth1` and `eth2`". In such a case - where at least one non-negated interface is used - all the negated interfaces are ignored! So the previous list becomes: "`eth1 eth2`".

.. _vnf_list:

List of VNFs
============

.. _vnf_dhcp4:

IPv4 Dynamic Host Configuration Protocol (DHCP4)
------------------------------------------------

This VNF provide a DHCP service implemented by `ISC Kea <https://www.isc.org/kea/>`_ software suite.

If you enable this service and don't provide any other context then some default will be used with DHCP serving on every interface for all subnets associated with your appliance...

.. important::

   If you will not be careful and don't specify explicitly on which interfaces the DHCP service should operate then you could severely disrupt yours or other people's network!

If a parameter supports multiple of values then they are separated by spaces, commas or semicolons e.g.::

   ONEAPP_VNF_DHCP4_INTERFACES="eth1 eth2 eth3"

If you wish to have a full control over this VNF then you can provide the complete configuration via `ONEAPP_VNF_DHCP4_CONFIG`. It must be a valid ISC Kea config file (JSON) encoded in base64. How to create one you can take a look in the `documentation <https://kea.readthedocs.io/en/latest/arm/dhcp4-srv.html>`_.

The most important context variable is the `ONEAPP_VNF_DHCP4_INTERFACES`. Firstly it will determine on which interface it will listen for DHCP requests and secondly it will determine for which subnets it will provide leases - it will auto-generate subnet lease configuration (if no `ONEAPP_VNF_DHCP4_SUBNET*` is provided).

..
   TODO: fix kea-config-generator - fails when one interface is used more than once

..
   The value can be either just an interface name (like `eth0`) or with appended IP address (e.g.: `eth0/192.168.1.1`) to pinpoint the listening address and subnet leases - if more than one address is used on the interface and each is in a different subnet (you can use one interface more than once if IPs will differ: `eth0/192.168.1.1` `eth0/10.0.0.1`).

For more tailored subnet configuration you can use `ONEAPP_VNF_DHCP4_SUBNET` context variables (you can use more than one by adding numbering: `ONEAPP_VNF_DHCP4_SUBNET0`). The value here must be a valid JSON configuration for ISC Kea `subnet4 section <https://kea.readthedocs.io/en/latest/arm/dhcp4-srv.html#configuration-of-ipv4-address-pools>`_ and the result must be base64 encoded.

DHCP4 also recognizes dynamic contextualization parameters - they are searched and applied based on the interfaces in `ONEAPP_VNF_DHCP4_INTERFACES` variable.

For example: if we defined `eth0` as a one of the listening interfaces then the following is also recognized as contextualization parameters::

    ONEAPP_VNF_DHCP4_ETH0=<cidr subnet>:<start ip>-<end ip>
    ONEAPP_VNF_DHCP4_ETH0_DNS=<ip> ...
    ONEAPP_VNF_DHCP4_ETH0_GATEWAY=<ip> ...
    ONEAPP_VNF_DHCP4_ETH0_MTU=<number>

.. note::

   Subnets defined by `ONEAPP_VNF_DHCP4_SUBNET*` params take precedence over `ONEAPP_VNF_DHCP4_<IFACE>*` params - so if you define even one `ONEAPP_VNF_DHCP4_SUBNET` then only these subnets will be configured.

.. important::

   This appliance allows reconfiguration - so some previously defined variables will be still respected! This can pose a problem if for example a `ONEAPP_VNF_DHCP4_SUBNET0` was defined already but now you wish to use dynamic per interface variables instead (`ONEAPP_VNF_DHCP4_<IFACE>*`). In that case you must also provide an override for the old `ONEAPP_VNF_DHCP4_SUBNET0` variable...simply set it empty: `ONEAPP_VNF_DHCP4_SUBNET0=""`

The DHCP4 VNF also provides other contextualization parameters among which is prominent the **ONElease** hook (via `ONEAPP_VNF_DHCP4_MAC2IP_ENABLED`). It serves a simple purpose of leasing IP addresses which match HW/MAC addresses of OpenNebula's VMs. This behavior is by default enabled - if you wish to disable it then just simply set `ONEAPP_VNF_DHCP4_MAC2IP_ENABLED` to `false`.

.. important::

   For the **ONElease** hook to function properly you should check and verify that the `ONEAPP_VNF_DHCP4_MAC2IP_MACPREFIX` parameter is either equal to OpenNebula's `MAC_PREFIX` option in `oned.conf <https://docs.opennebula.org/stable/deployment/references/oned_conf.html#virtual-networks>`_ or...just empty.

The hook also recognizes `ONEAPP_VNF_DHCP4_MAC2IP_SUBNETS` parameter via which we can restrict subnets where ONElease hook is applicated. If used then the normal Kea's lease mechanics is applied for all the other configured and valid subnets and pools. If `ONEAPP_VNF_DHCP4_MAC2IP_SUBNETS` is not used (is empty) and `ONEAPP_VNF_DHCP4_MAC2IP_ENABLED` is enabled then requests which cannot be mapped to any valid IP address (corresponding to the HW/MAC address) will be dropped.

.. important::

   If you want to use this DHCP VNF for both OpenNebula's virtual machines and also to provide leases to other machines and clients then you must either set `ONEAPP_VNF_DHCP4_MAC2IP_SUBNETS` or disable the hook completely (`ONEAPP_VNF_DHCP4_MAC2IP_ENABLED`). Otherwise you could encounter issues with failed DHCP requests for all non-opennebula DHCP clients.

.. _vnf_nat4:

IPv4 Network Address Translation (NAT4)
---------------------------------------

NAT VNF differs from the other VNFs in the way how it treats an empty **interfaces** parameter (`ONEAPP_VNF_NAT4_INTERFACES_OUT`). If it is left empty then even despite being explicitly enabled (`ONEAPP_VNF_NAT4_ENABLED`) it will not do anything - no NAT will be in action.

It must be explicitly stated on which interfaces will be outgoing traffic translated (`ONEAPP_VNF_NAT4_INTERFACES_OUT`).

.. important::

   Here the interfaces described by `ONEAPP_VNF_NAT4_INTERFACES_OUT` are the outgoing/external interfaces - all IP packets leaving one of these interfaces will have their source IP replaced with the IP of this interface.

.. _vnf_router4:

IPv4 Router (ROUTER4)
---------------------

Routing service is provided by enabling the forwarding of IP packets on the designated interfaces. If you enable **ROUTER4** VNF (`ONEAPP_VNF_ROUTER4_ENABLED`) then by default the forwarding is enabled on all interfaces and that means that any network client connected to the VNF/VRouter appliance can talk to any other connected client - which may not be what you want.

.. important::

   On the other hand if you enable forwarding on only one interface then it won't probably do anything useful - you need at least two enabled interfaces so the IP packets can be forwarded from one interface to the other...

.. note::

   If you wish for your network clients on private networks to be able to use public internet services then enabling forwarding is not enough - you must also enable **Network Address Translation**. For that you can look into the other provided VNF service: :ref:`NAT <vnf_nat4>`

.. _vnf_dns:

DNS Recursor (DNS)
------------------

DNS recursor service is implemented by `Unbound <https://nlnetlabs.nl/documentation/unbound/>`_.

The intention for this VNF is to provide resolving option for network clients which cannot access internet or your locally configured nameserver directly. You can either configure the **unbound** server to forward queries to another dns nameserver (yours or some public one) or to use the DNS **root servers** (`ONEAPP_VNF_DNS_USE_ROOTSERVERS`). If you do not want to use root servers (the default) then you must explicitly disable it (`ONEAPP_VNF_DNS_USE_ROOTSERVERS="NO"`). The appliance then will configure dns forward zone which will forward queries to configured nameservers. These are either provided explicitly by you (`ONEAPP_VNF_DNS_NAMESERVERS`) or they are auto-configurated from your virtual networks.

.. note::

   Most probably the auto-configurated forward zone is **NOT** what you want so either use root servers or explicitly provide upstream nameservers to which queries will be forwarded via `ONEAPP_VNF_DNS_NAMESERVERS`.

   For example::

      ONEAPP_VNF_DNS_NAMESERVERS="8.8.8.8, 8.8.4.4"

.. important::

   Beware to not run DNS recursor service on one of your internet-facing interface - otherwise you could become DNS recursor for the whole internet and be a victim of some form of `Denial-of-Service attack <https://en.wikipedia.org/wiki/Denial-of-service_attack>`_!

Aside from `eth?` and `!eth?` designation in `ONEAPP_VNF_DNS_INTERFACES` you can also use: `eth?/<ip>[@<port>]`

If the contextualization options are insufficiant to you then you can provide your own `unbound.conf <https://nlnetlabs.nl/documentation/unbound/unbound.conf/>`_ as base64 encoded string in `ONEAPP_VNF_DNS_CONFIG`.

.. important::

   Although it is a provided option you probably don't want to disable UDP protocol (`ONEAPP_VNF_DNS_UDP_DISABLED`) because many public nameservers are using UDP exclusively.

.. _vnf_keepalived:

Virtual Router Redundancy Protocol - VRRP (KEEPALIVED)
------------------------------------------------------

`Keepalived <https://www.keepalived.org/>`_ is an open-source project implementing `VRRP protocol <https://en.wikipedia.org/wiki/Virtual_Router_Redundancy_Protocol>`_.

The main purpose for it is to quickly and with as little downtime as possible migrate IP addresses due to some network issue or a VM crash. Keepalived thus provides a form of simple **High Availibility** (HA).

The fundamental element of it are Virtual IP addresses (VIPs or floating IPs).

This appliance leverages this feature via :ref:`vnf_vrouter_context_param`.

Keepalived will be configured internally with **vrrp instances** and **vrrp group** - each instance has a name corresponding to the name of the required interface, e.g: `ETH0` (It will be mostly irrelevant to you - this is done auto-magically).

Keepalived VNF can be still configured quite flexibly especially if you wish to run multiple VRouters on the same subnet (you must reserve unique **virtual router id** for each of them otherwise they will try to be part of the same cluster and that will mostly result in failure).

:ref:`These contextualization parameters are described above. <vnf_keepalived_context_param>`

.. note::

   If you will use this appliance as VRouter then most of the context variables are prefilled for you - you will only need to check few boxes and fill out the IP address which will serve as a floating IP.

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

   Before you start with the tutorials, please, ensure that you followed the steps as was described in the :ref:`Quick Start <quick_start>`. Mainly that you downloaded the VNF image and prepared the template by setting up the password or ssh key or both.

.. _vnf_tutorial_vnf:

VNF appliance as VM
-------------------

This demo creates a VNF VM which will serve as a router with a NAT and as a DHCP server and DNS. It will have access to the internet via public/external network where NAT will happen and it will be attached to other two different local subnets where it will provide DHCP and DNS service. Plus it will forward packets between these networks and it will provide access to the internet.

We start with instantiation of the VNF appliance - no network is attached yet. The provided contextualization can be seen in this picture:

|image-vnf-demo-vm1|

.. _vnf_tutorial_vnf_explanation:

.. note::

   **Explanation**: We don't want to provide DHCP and DNS recursor service to the internet so we define interfaces for these VNF with a value of: "`!eth0`". For the NAT we must be explicit and we want to translate all outgoing traffic on the public/internet interface so that is why we set NAT interfaces to: "`eth0`". For the router service we want to forward packets between all the interfaces - so the clients in both the local networks can talk to each other and to the internet. For that we can leave it empty because that is the default when the VNF is enabled.

   The rest of the context is unchanged - it is left with defaults.

Click the **Instantiate** button and wait a minute. You should also be able to login inside and look around. There is only loopback interface so all enabled VNFs are just idle.

Let us attach three virtual networks and see what happens:

* `public`
* `vnet_a`
* `vnet_b`

First we will attach `public` vnet - that will be our `eth0`. It is an internet facing NIC through which we can reach public services like root DNS servers.

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

Now we will attach `vnet_a` and `vnet_b` in exactly the same manner - you must just wait until the configuration is twice more finished...

Our VM has altogether these IP addresses:

* `192.168.150.102/24`
* `192.168.101.100/24`
* `192.168.102.100/24`

You can verify that all have been configured by checking the report config file: `/etc/one-appliance/config`

There you should see all three interfaces::

   ...
   ONEAPP_VROUTER_ETH0_IP = '192.168.150.102'
   ...
   ONEAPP_VROUTER_ETH1_IP = '192.168.101.100'
   ...
   ONEAPP_VROUTER_ETH2_IP = '192.168.102.100'
   ...

For better understanding of the current state of the appliance you can checkout these config files:

* **DHCP4**: `/etc/kea/kea-dhcp4.conf`
* **DNS**: `/etc/unbound/unbound.conf`
* **ROUTER4**: `/etc/sysctl.d/01-one-router4.conf`
* **NAT**: `/etc/iptables/nat4-rules-enabled`

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

You can see that we configured IP address `192.168.101.100` and `192.168.102.100` as router and nameserver option to our DHCP clients (you can verify that in `/etc/kea/kea-dhcp4.conf`).

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

Wait few seconds for `configuration finished` and verify the config like this::

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

* one will get: `vnet_a`
* the other: `vnet_b`

Login to the one connected to `vnet_a`::

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

And similarly with the other on `vnet_b`::

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

Both of these network clients (on different subnets) should be able to talk between each other (e.g.: `ping`) and should be able to resolve DNS and reach internet services.

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

   **Explanation**: The context is almost the same as was with :ref:`the previous demo <vnf_tutorial_vnf_explanation>` the biggest differences here are the missing **router** option (it is always enabled in VRouter) and that VRouter has a option `Number of VM instances` which we raised to two.

We wait for the VRouter's VMs and we can login to each of them and verify if everything was configured as expected. Although we did not attached any network yet so all enabled VNFs are idle. The goal is to attach these three virtual networks:

* `public`
* `vnet_a`
* `vnet_b`

First we will attach `public` vnet - that will be our `eth0`. It is an internet facing NIC through which we can reach public services like root DNS servers.

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

Now we will attach `vnet_a` and `vnet_b` but there will be one extra but crucial step in it...

Again pick **Instances → Virtual Routers** and click on your VRouter and then **Attach NIC**. Click on **vnet_a** and now we must fill in the textbox `Force IPv4` with value: `192.168.101.111` and tick the checkbox on the right: `Floating IP`. It should look like this:

|image-vnf-demo-vrouter3|

And hit **Attach**. We do this once more for `vnet_b` in which case you set `Force IPv4` as `192.168.102.111` and again enable `Floating IP`:

|image-vnf-demo-vrouter4|

Wait till both VRouter's VMs are configured (`/var/log/one-appliance/ONE_configure.log`) - don't forget that reconfiguration is triggered with each attached NIC - so before you continue be sure that you see twice `CONFIGURATION FINISHED` in the log and that one of the VM is **Master** (`/etc/keepalived/ha-check-status.sh`).

At that point both VRouter's VMs should have three interfaces with Keepalived running and one of them should have all VNFs up and ready and assigned all floating IPs. You can verify that by checking the report config file: `/etc/one-appliance/config`

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

* **DHCP4**: `/etc/kea/kea-dhcp4.conf`
* **DNS**: `/etc/unbound/unbound.conf`
* **ROUTER4**: `/etc/sysctl.d/01-one-router4.conf`
* **NAT**: `/etc/iptables/nat4-rules-enabled`
* **KEEPALIVED**: `/etc/keepalived/keepalived.conf`

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

* one will get: `vnet_a`
* the other: `vnet_b`

Login to the one connected to `vnet_a`::

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

And similarly with the other on `vnet_b`::

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

Both of these network clients (on different subnets) should be able to talk between each other (e.g.: `ping`) and should be able to resolve DNS and reach internet services.

**Congratulations with your first successful VROUTER appliance deployment!**

VRouter management
~~~~~~~~~~~~~~~~~~

VRouter has a option to attach a NIC which will have a special purpose - it will serve only as a entrypoint to the VRouter's VMs and no VNF and no service will be running on such interface. This interface we call **management** and here is how we can add it to our VRouter.

Our management interface will be connected to the reserved virtual network `vnet_mgt` which we have already defined at :ref:`the beginning of the tutorials <vnf_tutorials>`.

In the Sunstone pick **Instances → Virtual Routers** and click on your VRouter and then **Attach NIC → vnet_mgt**, tick the checkbox: `Management Interface` and follow with the final **Attach**.

|image-vnf-demo-vrouter5|

Again wait for all the VRouter's VMs to be configured and one of them voted as **Master**. You should see a new interface inside but nothing should be listening on it.

VRouter failover test
~~~~~~~~~~~~~~~~~~~~~

Now we verify that HA works as the last part of this tutorial.

Login to both of the network client VMs (`vnet_a` and `vnet_b`) and let them ping each other - that means on the VM1 (`vnet_a`)::

   localhost:~# ping 192.168.102.101

and on the VM2 (`vnet_b`)::

   localhost:~# ping 192.168.101.101

At this point they should ping each other without a problem - let them so. Now find which of the two VRouter's VMs is currently the master. Look up the Master VM (by IP address for example) in the Sunstone and click on it. Then in the top menu bar pick and click on **Reboot (hard)** - this will simulate failover.

Go back to your pinging VMs and you should see a short freeze and quick resume of pinging - that means failover successfully happened. To make it double sure login to the former Backup VM and check its state again (`/etc/keepalived/ha-check-status.sh`). It should be the Master now and the former Master VM should be soon up and ready as a new Backup.

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
