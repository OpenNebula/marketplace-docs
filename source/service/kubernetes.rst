.. _service_k8s:

================
Kubernetes (K8s)
================

OpenNebula `Marketplace Appliance <https://marketplace.opennebula.io/appliance/1318e7ca-bd6d-11eb-9ae8-98fa9bde1a93>`_  with preinstalled `Kubernetes <https://kubernetes.io/>`_ service.

Without any parameters provided, the appliance deploys as a single Kubernetes master node. Appliance can be customized with :ref:`contextualization <k8s_context_param>` parameters to support manually managed multi-node deployments, or automatically managed multi-node cluster as OpenNebula `OneFlow service <https://docs.opennebula.io/stable/management_and_operations/multivm_service_management/appflow_use_cli.html>`_.

.. include:: shared/features.txt
* **Only single master clusters supported**.
* Preconfigured with Canal CNI networking (`Calico <https://www.projectcalico.org/>`_, `Flannel <https://github.com/coreos/flannel>`_).
* Installed with MetalLB LoadBalancer provider (`MetalLB <https://metallb.universe.tf/>`_)
* Optional: :ref:`Kubernetes Dashboard <k8s_dashboard>`.

Platform Notes
==============

Component versions
------------------

.. table::
    :widths: 100 50 40

    +-----------------------------+------------------------------------------------------------------------------------------------------------------+
    | Component                   | Version                                                                                                          |
    +=============================+=====================================================================================+============================+
    | `Kubernetes Appliance 1.21 <https://marketplace.opennebula.io/appliance/1318e7ca-bd6d-11eb-9ae8-98fa9bde1a93>`_   |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Kubernetes                  | 1.21.5                                                                              |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Docker                      | 20.10.9 CE                                                                          |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+ |image-k8s-certified-logo| |
    | Calico                      | 3.19.1                                                                              |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | MetalLB                     | 0.9.6                                                                               |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Contextualization package   | 6.2.0                                                                               |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | `Kubernetes Appliance 1.18 <https://marketplace.opennebula.io/appliance/547ecdff-f392-43b9-abc9-5f10a9fa7aff>`_   |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Kubernetes                  | 1.18.18                                                                             |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Docker                      | 19.03.15 CE                                                                         |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Calico                      | 3.13                                                                                |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | MetalLB                     | 0.9.6                                                                               |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Contextualization package   | 6.2.0                                                                               |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+----------------------------+

Requirements
------------

* OpenNebula 4.14 - 6.2
* `OneFlow <https://docs.opennebula.io/stable/management_and_operations/multivm_service_management/overview.html>`_ and `OneGate <https://docs.opennebula.io/stable/management_and_operations/multivm_service_management/onegate_usage.html>`_ for multi-node orchestration
* Min. Memory: 3 GB (master), 1 GB (worker)
* Min. Cores (VCPU): 2 (master), 1 (worker)

Quick Start
===========

.. include:: shared/import.txt

.. important::

   In order to use a vCenter app it is necessary to attach the image to a vCenter VM Template which was previously imported. An existing VM Template can be cloned and its disks replaced with the image from the marketplace. Once the VM Template is ready, the appliance can be instantiated.

.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <k8s_context_param>` parameters:

|image-context-vars|

.. note::

    The first input for **Master node address** is important and the value may differ for the master node and the worker nodes in the same cluster. For example for the master node, it may be left empty and the actual IP address will be derived from your setup (:ref:`more info is below <k8s_master_node>`) or set it to an actual IP assigned inside the VM. For the worker nodes, it must be a real IP address (in all cases). More about this in :ref:`contextualization <k8s_context_param>`.

    The following two inputs (**Secret token** and **Secret hash**) are relevant for worker nodes only and they need to be set with values stored on the master node in :ref:`/etc/one-appliance/config <one_service_logs>`. Below is an :ref:`example <report_file_example>` of such file. These three inputs are mandatory for the worker node to join the cluster - **all of them must be set**. Otherwise, a new single master node will be created.

After you are done, click on the button **Instantiate**. A virtual machine with the running service should be ready in a few minutes.

.. include:: shared/report.txt

.. _report_file_example:

.. code::

       ___   _ __    ___
      / _ \ | '_ \  / _ \   OpenNebula Service Appliance
     | (_) || | | ||  __/
      \___/ |_| |_| \___|

    All set and ready to serve 8)

    [root@onekube-ip-192-168-122-10 ~]# cat /etc/one-appliance/config
    [Kubernetes]
    k8s_connection = https://192.168.122.10:6443
    k8s_master = 192.168.122.10
    k8s_join_command = kubeadm join 192.168.122.10:6443 --token meis0p.gxa5e4kvk8pv1xx6 --discovery-token-ca-cert-hash sha256:3b5441641c37af01c9847641c4024f1312755fb4dde1f66a388b9b4eb6ebe1a6
    k8s_token = meis0p.gxa5e4kvk8pv1xx6
    k8s_hash = 3b5441641c37af01c9847641c4024f1312755fb4dde1f66a388b9b4eb6ebe1a6
    k8s_ui_proxy_url = http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
    k8s_ui_login_token = eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJvc3AtdG9rZW4taHZjam0iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoib3NwIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMzdjOTc4NjctNDVhOS0xMWU5LWJlMTEtMDIwMGMwYTg3YTBhIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOm9zcCJ9.Z-zh1WdPrOGctKnjDs79R9DphFrN-5QGsa4NXvRH9112H5iZNjNZP_HE_MakSVnGn6lF1FEmUJppFmqEfsonz5FyP-fDvBzBnb3Xvo-Em12bYXfDfdcWQ90bvLpYIiYY8NhjJFXmAJmkZPmtiv1Eto_sK9ZlXNSTuBRsMAeF0Q7PeymSLKPHcwGCEGzPJQRZCxsBwZ7oKI4_LdcNopR6btULO0B9DesyAZaMPUbMs75KySOxloryockINFZoYYHmckB5fuiBQI_8Pni3mSwrEPU2lwnUrajRQDJuSSbNtbL1fSrMnj2suB20xLF9382yuJJ-2S9VjqrGiIAvDJ7F8g

.. _k8s_context_param:

Contextualization
=================

Contextualization parameters provided in the Virtual Machine template controls the initial VM configuration. Except for the `common set <https://docs.opennebula.io/stable/management_and_operations/references/template.html#context-section>`_ of parameters supported by every appliance on the OpenNebula Marketplace, there are few specific to the particular service appliance. The parameters should be provided in the ``CONTEXT`` section of the Virtual Machine template, read the OpenNebula `Management and Operations Guide <https://docs.opennebula.io/stable/management_and_operations/references/kvm_contextualization.html#set-up-the-virtual-machine-template>`__ for more details.

====================================== ========= ==================== ========= ======== ===========
Parameter                              Mandatory Default              Stage     Role     Description
====================================== ========= ==================== ========= ======== ===========
``ONEAPP_K8S_ADDRESS``                           routable IP          configure all      Master node address
``ONEAPP_K8S_TOKEN``                                                  configure worker   Secret token - to join worker node to the cluster
``ONEAPP_K8S_HASH``                                                   configure worker   Secret hash - to join worker node to the cluster
``ONEAPP_K8S_NODENAME``                          hostname             configure master   Master Node Name
``ONEAPP_K8S_PORT``                              ``6443``             configure master   Kubernetes API port on which nodes communicate
``ONEAPP_K8S_TAINTED_MASTER``                    ``no``               configure master   Master node acts as control-plane only (**you will need to add worker nodes**)
``ONEAPP_K8S_PODS_NETWORK``                      ``10.244.0.0/16``    configure master   Kubernetes pod network - pods will have IP from this range
``ONEAPP_K8S_ADMIN_USERNAME``                    ``admin-user``       configure master   UI dashboard admin account - K8s secret's token is prefixed with this name
``ONEAPP_K8S_LOADBALANCER_RANGE[0-9]``                                configure master   LoadBalancer IP range (can be used mutiple times with numbered suffix)
``ONEAPP_K8S_LOADBALANCER_CONFIG``                                    configure master   Custom LoadBalancer config (encoded in Base64)
====================================== ========= ==================== ========= ======== ===========

If no contextualization parameter is provided, the appliance will create a :ref:`single node Kubernetes cluster <k8s_master_node>`. By default, it will listen on port ``6443`` of an IP address assigned to a default interface. You can still add more nodes to this cluster later by providing contextualization parameters required by a :ref:`worker node <k8s_worker_node>`.

.. _k8s_master_node:

Master Node (or Single Node Deployment)
---------------------------------------

In this type of deployment, you don't need to setup anything in advance. The appliance will bootstrap a fully functional single node Kubernetes cluster which can be extended by other worker nodes at any time later.

For other cases when the default behavior is not sufficient, you can contextualize the appliance with a few parameters. The most important is ``ONEAPP_K8S_ADDRESS`` and configuration of the :ref:`Kubernetes LoadBalancer <k8s_loadbalancer>`.

.. important::

   On the **master node**, the parameter ``ONEAPP_K8S_ADDRESS`` may be left empty if the default behaviour is satisfactory.

   In the public cloud it will be derived from OneGate's External IP which is created by attaching a `public IP <https://docs.opennebula.io/6.2/quick_start/operation_basics/operating_edge_cluster.html#virtual-networks-public>`_ to the VM.

   If there are more than one NICs then ``ONEAPP_K8S_NODE_NIC`` can be used to specify on which NIC to look for External IP.

   If OneGate is not configured or deployment is not in a public cloud then the appliance defaults to **the first found routable IP** - which is usually the one on the first NIC.

   Otherwise ``ONEAPP_K8S_ADDRESS`` must be setup manually and match the actual assigned IP on one of the NICs.

   The purpose of this parameter is to pin Kubernetes communication to a designated interface and a network. So it will not be exposed on the default interface.

   For the **worker nodes**, the value must be strictly the advertised IP address of the master node!

``ONEAPP_K8S_NODENAME`` is an option solely for a convenience (to easily distinct multiple clusters by a mere look on the name of the master node). It will set a name for the master node in the Kubernetes cluster and can be seen when you run:

.. prompt:: text [master]# auto

    [master]# kubectl get nodes

If you don't set this parameter, the master node (and worker nodes) will have assigned the hostname as a Kubernetes node name.

``ONEAPP_K8S_PORT`` provides a facility to change the default Kubernetes API port (``6443``).

``ONEAPP_K8S_PODS_NETWORK`` defines a range of private addresses on which pods will be deployed. By default, it is ``10.244.0.0/16``. Kubernetes service appliance uses the CNI networking plugin `Canal (Calico+Flannel) <https://docs.projectcalico.org/v3.8/getting-started/kubernetes/installation/flannel>`_ which combines two projects:

* `Calico <https://www.projectcalico.org/>`_
* `Flannel <https://github.com/coreos/flannel>`_

.. important::

   **DO NOT CHANGE** the default value of ``ONEAPP_K8S_PODS_NETWORK`` unless you know what are you doing! It can break the internal networking!

``ONEAPP_K8S_LOADBALANCER_RANGE`` is the simplest way to configure the LoadBalancer type of services. The value must be a range (or multiple ranges by adding more params with numbered suffix), e.g.:

.. code::

    192.168.100.100-192.168.100.200

More info is in the :ref:`LoadBalancer section <k8s_loadbalancer>`.

``ONEAPP_K8S_ADMIN_USERNAME`` defaults to ``admin-user``. It is the service account name under which a Kubernetes token is created to enable login into :ref:`UI dashboard <k8s_dashboard>`. The token is stored in :ref:`/etc/one-appliance/config <one_service_logs>` as ``k8s_ui_login_token`` (you can see one in the :ref:`example <report_file_example>` above).

The only reason to change the default is to easily find and manage the mentioned token with a command:

.. prompt:: text [master]# auto

    [master]# kubectl -n kube-system get secret | grep ${ONEAPP_K8S_ADMIN_USERNAME}-token

.. important::

   Handle carefully the Kubernetes cluster token (``k8s_token`` in :ref:`/etc/one-appliance/config <one_service_logs>`) as it **never** expires. To use short-living tokens, delete the existing one and create a new token as described in the `kubeadm documentation <https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#join-nodes>`_.

.. note::

    There is an option to setup master node as a `control-plane <https://kubernetes.io/docs/concepts/overview/components/#control-plane-components>`_ only. In such scenario the master node will not schedule any pods on itself and it will run user pods on worker nodes only. Therefore single-node master with control-plane only does not make sense and it will always need other (worker) nodes.

    If you wish to run the master node in such way then set the ``ONEAPP_K8S_TAINTED_MASTER=YES``.

.. _k8s_worker_node:

Worker Node
-----------

New nodes can join already running Kubernetes cluster, if they are provided with following contextualization parameters:

* ``ONEAPP_K8S_ADDRESS`` - master node's IP address
* ``ONEAPP_K8S_TOKEN``
* ``ONEAPP_K8S_HASH``

.. important::

   For the worker nodes, the ``ONEAPP_K8S_ADDRESS`` must be an exact IP address of the master node on which the Kubernetes advertises its API! All the three parameters must be defined together to take an effect (with correct values). Otherwise a node will be deployed as an independent master of a single-node cluster.

You can find all values for these parameters on the master node in the :ref:`/etc/one-appliance/config <one_service_logs>` (actual values will differ from our :ref:`example <report_file_example>`):

* ``k8s_master``
* ``k8s_token``
* ``k8s_hash``

The example use of these parameters can be seen on the figure:

|image-worker-values|

When you check the status of the nodes in the cluster, you should see one master and all the other joined nodes.

Example:

.. prompt:: text [master]# auto

   [master]# kubectl get nodes
   NAME                                    STATUS   ROLES    AGE     VERSION
   onekube-ip-192-168-122-10.localdomain   Ready    master   10h     v1.13.4
   onekube-ip-192-168-122-11.localdomain   Ready    <none>   7h57m   v1.13.4

.. _k8s_oneflow:

OneFlow Integration
-------------------

.. note::

    On **OpenNebula 6.0 and newer** you can use dedicated appliance `Service Kubernetes 1.21 - KVM <https://marketplace.opennebula.io/appliance/dd6ed430-bd6d-11eb-9e90-98fa9bde1a93>`__ to run a Kubernetes cluster managed by OneFlow in a simple way. The result is very same as with the manual configuration described below.

The Kubernetes appliance (optionally) integrates with the OpenNebula `OneFlow service <https://docs.opennebula.io/stable/management_and_operations/multivm_service_management/appflow_use_cli.html>`_ to enable one-click multi-node cluster deployment. A template for OneFlow is not part of the imported Marketplace appliance and on older versions of OpenNebula (prior 6.0) it must be created manually (in OpenNebula 6.0+ you have access to the Kubernetes Service Template directly from the marketplace - as mentioned in the note above). The template can be created over the Sunstone wizard, or by providing a JSON specification.

.. important::

   In the OneFlow service template, the following role names must be used:

   - **master** for master node
   - **worker** for worker nodes

   If different names are chosen, the deployment will not work!

Create OneFlow Service Template (Wizard)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Step 1 - General
^^^^^^^^^^^^^^^^

- Open your OpenNebula Sunstone, log in
- Go to **Templates → Services** tab
- Click on the green **+** button at the top
- Fill the name of the new service template

In the **Advanced service parameters**, check item **Wait for VMs to report that they are READY via OneGate to consider them running** (this ensures the workers are run after the master is fully configured).

|image-oneflow-part4|

Step 2 - Role Master
^^^^^^^^^^^^^^^^^^^^

- Fill the role name as ``master`` (**nothing else!**)
- Pick the ``Kubernetes 1.21 - KVM`` (VM template)

The current status should resemble this picture with important parts highlighted:

|image-oneflow-part1|

Step 3 - Role Worker
^^^^^^^^^^^^^^^^^^^^

As the next step, we setup a worker role:

- Click on the blue button **+** next to **Roles**
- Fill the role name as ``worker`` (**nothing else!**)
- Optional: Set the **Number of VMs** run initially
- Pick the ``Kubernetes 1.21 - KVM`` (VM template)
- In **Parent roles**, set **master** as a parent role

The next status should resemble this picture with important parts highlighted:

|image-oneflow-part2|

.. important::

    Don't forget to

    - check the option **Wait for VMs to report that they are READY via OneGate to consider them running**
    - use only **master** and **worker** role names
    - set the **Parent roles** for the **worker** node
    - specify **VM template content** for both **master** and **worker roles**

    Otherwise, the deployment will not work! With the wrong service template, all nodes might be started at the same time and configured as independent single-node clusters.

At the end, click on the green **Create** button at the top.

Create OneFlow Service Template (Advanced)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Instead of going through the wizard, the service template can be passed in the JSON format.

- Open your OpenNebula Suntone, log in
- Go to **Templates → Services** tab
- Click on the green **+** button at the top
- Click on **Advanced** tab
- Put the service definition below into the text area
- Replace all ``<<VM_TEMPLATE_ID>>`` placeholders with ID of your imported Kubernetes VM template
- Click on green **Create** button at the top

Service template:

.. code::

    {
      "name": "Kubernetes",
      "deployment": "straight",
      "description": "",
      "roles": [
        {
          "name": "master",
          "cardinality": 1,
          "vm_template": <<<VM_TEMPLATE_ID>>> ,
          "vm_template_contents": "ONEGATE_ENABLE=\"YES\"",
          "elasticity_policies": [],
          "scheduled_policies": []
        },
        {
          "name": "worker",
          "cardinality": 1,
          "vm_template": <<<VM_TEMPLATE_ID>>> ,
          "vm_template_contents": "ONEGATE_ENABLE=\"YES\"",
          "parents": [
            "master"
          ],
          "elasticity_policies": [],
          "scheduled_policies": []
        }
      ],
      "ready_status_gate": true
    }

Or, use the `OneFlow CLI <https://docs.opennebula.io/stable/management_and_operations/multivm_service_management/appflow_use_cli.html#managing-services>`_.

Instantiate Service
~~~~~~~~~~~~~~~~~~~

To run the multi-node service:

- Go to **Templates → Services** tab
- Select the Kubernetes service template
- Click on the green **+** button at the top and **Instantiate**
- For each role choose ``YES`` under the **Enable OneGate reporting? (req. for multi-node)**
- Click on **Instantiate**

.. important::

   Mostly you shouldn't be asked for any inputs, but in case the OpenNebula provides you with a list of all K8s specific contextualization variables per role to customize before instantiation, you can leave all the parameters empty. **DO NOT** set any inputs for the **worker** role, the parameters will be configured automatically from the **master** node.

|image-oneflow-part5|

Follow the service deployment status in **Instances → Services** tab.

Check Kubernetes Cluster Status
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Log into the **master node** over SSH and check the cluster contains all the deployed nodes.

For example:

.. prompt:: text # auto

   # kubectl get nodes -o wide
   NAME                                    STATUS   ROLES    AGE     VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION              CONTAINER-RUNTIME
   onekube-ip-192-168-122-10.localdomain   Ready    master   16m     v1.13.4   192.168.233.100   <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64   docker://18.6.3
   onekube-ip-192-168-122-11.localdomain   Ready    <none>   10m     v1.13.4   192.168.233.101   <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64   docker://18.6.3
   onekube-ip-192-168-122-12.localdomain   Ready    <none>   9m49s   v1.13.4   192.168.233.102   <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64   docker://18.6.3

.. _k8s_remote:

Remote Usage
------------

To control the Kubernetes cluster remotely, you need to have the ``kubectl`` CLI tool installed on your system (workstation, laptop). Follow the official `installation guide <https://kubernetes.io/docs/tasks/tools/install-kubectl/>`_. When finished, you can validate the correct installation by running:

.. prompt:: text [remote]$ auto

    [remote]$ kubectl --help

You also need to have the configuration with cluster master IP address and access keys. This configuration can be taken from the **master** node from a file ``/etc/kubernetes/admin.conf`` and put on your remote system into ``~/.kube/config``.

For example (place a valid master node IP address):

.. prompt:: text [remote]$ auto

    [remote]$ mkdir -p ~/.kube
    [remote]$ scp root@203.0.113.10:/etc/kubernetes/admin.conf ~/.kube/config

Validate the tool is configured properly by checking the cluster nodes status:

.. prompt:: text $ auto

    [remote]$ kubectl get nodes

.. note::

    The access configuration can be placed into any custom file, but the location has be explicitly configured. Via

    - env. variable, e.g. ``KUBECONFIG=${HOME}/admin.conf kubectl get nodes``
    - CLI argument, e.g. ``kubectl --kubeconfig=${HOME}/admin.conf get nodes``

.. _k8s_loadbalancer:

LoadBalancer Service
--------------------

Pods or deployments can be exposed as a service of the type `LoadBalancer <https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer>`_ which is more flexible alternative to the simple type `NodePort <https://kubernetes.io/docs/concepts/services-networking/service/#nodeport>`_.

OpenNebula's Kubernetes appliance is using the baremetal loadbalancer provider called `MetalLB <https://metallb.universe.tf/>`_.

It is by default configured as `ARP/Layer2 <https://metallb.universe.tf/concepts/layer2/>`_ LoadBalancer which means that the exposed LoadBalancer IP must be routed to one of the Kubernetes nodes by means outside of the scope of the appliance itself.

MetalLB supports also `BGP/Layer3 <https://metallb.universe.tf/concepts/bgp/>`_ loadbalancing. If the user is capable of setting up its network for this dynamic routing protocol then the user can provide the appliance with the proper configuration via the contextualization parameter ``ONEAPP_K8S_LOADBALANCER_CONFIG`` (Base64 encoded).

The configuration for the ARP loadbalancing can look similar to this:

.. code::

    apiVersion: v1
    kind: ConfigMap
    metadata:
      namespace: metallb-system
      name: config
    data:
      config: |
        address-pools:
        - name: default
          protocol: layer2
          addresses:
          - 192.168.10.100-192.168.10.200
          - 192.168.20.100-192.168.20.200
          - 192.168.30.100-192.168.30.200

The most straightforward way is to just set the range via ``ONEAPP_K8S_LOADBALANCER_RANGE`` parameter or multiple ranges with more parameters, e.g.:

.. code::

    ONEAPP_K8S_LOADBALANCER_RANGE1=192.168.10.100-192.168.10.200
    ONEAPP_K8S_LOADBALANCER_RANGE2=192.168.20.100-192.168.20.200
    ONEAPP_K8S_LOADBALANCER_RANGE3=192.168.30.100-192.168.30.200
    ...

.. important::

    For the LoadBalancer to work the range must be routable inside and outside of the Kubernetes nodes and must be reserved to the Kubernetes!

Trivial example to demonstrate how LoadBalancer can be used is below.

Let's first check how is LoadBalancer configured:

.. prompt:: text [master]# auto

    [master]# kubectl describe configmap config -n metallb-system
    Name:         config
    Namespace:    metallb-system
    Labels:       <none>
    Annotations:
    Data
    ====
    config:
    ----
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 172.16.100.100-172.16.100.110
      - 172.16.100.200-172.16.100.210

    Events:  <none>

As we can see we have two ranges configured (which are routable) - now we can attempt to deploy the service.

.. prompt:: text [master]# auto

    [master]# kubectl run nginx --image=nginx --port 80
    pod/nginx created
    [master]# kubectl expose pod nginx --type=LoadBalancer
    service/nginx exposed
    [master]# kubectl get services
    NAME         TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
    kubernetes   ClusterIP      10.96.0.1      <none>           443/TCP        39h
    nginx        LoadBalancer   10.106.25.84   172.16.100.100   80:31980/TCP   12s

Our service ``nginx`` is exposed on the next free loadbalanced IP from the provided range or ranges - in this case ``172.16.100.100`` - and can be reached outside of the Kubernetes appliance as expected:

.. prompt:: text [remote]$ auto

    [remote]$ curl -s 172.16.100.100 | html2text
    # Welcome to nginx!

    If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.

    For online documentation and support please refer to
    [nginx.org](http://nginx.org/).
    Commercial support is available at [nginx.com](http://nginx.com/).

    _Thank you for using nginx._

Updating LoadBalancer Config
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It may happen that the configuration of the LoadBalancer was omitted during instantiation or simply became outdated with the time.

This can be amended by updating the **configmap** holding the LoadBalancer's configuration.

Let's create a new config file and store it as ``config.yaml``, e.g.:

.. code::

    apiVersion: v1
    kind: ConfigMap
    metadata:
      namespace: metallb-system
      name: config
    data:
      config: |
        address-pools:
        - name: default
          protocol: layer2
          addresses:
          - 192.168.100.0-192.168.255.255

We can now apply it with this command:

.. prompt:: text [master]# auto

    [master]# kubectl apply -f config.yaml

Verify that the command worked:

.. prompt:: text [master]# auto

    [master]# kubectl describe configmap config -n metallb-system
    Name:         config
    Namespace:    metallb-system
    Labels:       <none>
    Annotations:
    Data
    ====
    config:
    ----
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.100.0-192.168.255.255

    Events:  <none>

And check the Kubernetes' log:

.. prompt:: text [master]# auto

    [master]# kubectl logs --selector app=metallb -n metallb-system
    ...
    {"caller":"main.go:108","configmap":"metallb-system/config","event":"startUpdate","msg":"start of config update","ts":"2021-04-16T01:36:54.414716585Z"}
    {"caller":"main.go:117","configmap":"metallb-system/config","error":"new config not compatible with assigned IPs: service \"default/nginx\" cannot own \"172.16.100.101\" under new config","msg":"applying new configuration failed","op":"setConfig","ts":"2021-04-16T01:36:54.510442388Z"}
    {"caller":"main.go:118","configmap":"metallb-system/config","event":"endUpdate","msg":"end of config update","ts":"2021-04-16T01:36:54.510717686Z"}
    {"caller":"k8s.go:395","configmap":"metallb-system/config","error":null,"event":"configStale","msg":"config (re)load failed, config marked stale","ts":"2021-04-16T01:36:54.510868422Z"}
    ...

.. warning::

    Beware and take notice: **Our update failed!**

What happened? We applied a new config which rendered the already loadbalanced and exposed External IPs incompatible!

We must fix it - we forgot to add the old range into the new ``config.yaml``:

.. code::

    apiVersion: v1
    kind: ConfigMap
    metadata:
      namespace: metallb-system
      name: config
    data:
      config: |
        address-pools:
        - name: default
          protocol: layer2
          addresses:
          - 192.168.100.0-192.168.255.255
          - 172.16.100.100-172.16.100.200

One more time:

.. prompt:: text [master]# auto

    [master]# kubectl apply -f config.yaml

Check the logs again (do it after every change):

.. prompt:: text [master]# auto

    [master]# kubectl logs --selector app=metallb -n metallb-system

.. important::

    Always double-check that your new range(s) is actually valid!

Generic IP range
~~~~~~~~~~~~~~~~

Sometimes the range cannot be known in advance and so we can configure and force the LoadBalancer to act on **every** address with this workaround:

.. code::

    ONEAPP_K8S_LOADBALANCER_RANGE=0.0.0.0-255.255.255.255

This time we can expose the service on a desired address (with ``--load-balancer-ip``) which we know that it is safe to use:

.. prompt:: text [master]# auto

    [master]# kubectl expose pod nginx --type=LoadBalancer --load-balancer-ip=172.16.100.101

.. important::

    Use this only when you know what you are doing and can secure that no one will abuse this and create conflicts on the network!

.. _k8s_dashboard:

Dashboard
---------

**Kubernetes Dashboard** (Web UI) is not accessible by default and must be enabled on the remote system (workstation, laptop) you'll access from.

.. note::

    You must have ``kubectl`` CLI :ref:`installed and configured <k8s_remote>` on the host you'll access the dashboard.

Run the proxy command (command remains running on foreground, hit ``CTRL+C`` to terminate it at the end):

.. prompt:: text [remote]$ auto

    [remote]$ kubectl proxy
    Starting to serve on 127.0.0.1:8001

On the same host, open the web browser and reach the Kubernetes Dashboard URL:

   `http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/ <http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/>`_

You'll get on the Kubernetes Dashboard login screen.

- Select the **Token** option
- Paste the value of ``k8s_ui_login_token`` from :ref:`/etc/one-appliance/config <one_service_logs>` located on the master node

|image-ui-login|

The Kubernetes Dashboard is not only useful to explore the Kubernetes cluster, but also allows to deploy the applications by clicking on the **CREATE** button in the top-right corner:

|image-ui-create|

Check the `official documentation <https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/>`_.

.. _k8s_tutorial:

Example Application
===================

In this section, we demonstrate how to deploy a simple application.

.. note::

    All the actions need to be executed from the **master** node, or from a host which has ``kubectl`` CLI :ref:`installed and configured <k8s_remote>`.

First, check the cluster is healthy and sees all deployed nodes:

.. prompt:: text # auto

    [master]# kubectl get nodes

Deploy Application
------------------

We will loosely follow the `official guide <https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/>`_ as a demonstration how to deploy an application to our newly created Kubernetes cluster.

1. External IP without LoadBalancer
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Start with creating a manifest file (change the ``externalIPs`` to your master node IP **!**):

.. prompt:: text [master]# auto

    [master]# cat - >~/example.yaml <<EOF
    apiVersion: v1
    kind: Service
    metadata:
      name: example-service
    spec:
      selector:
        app: nginx
      ports:
      - port: 80
        targetPort: http
      externalIPs:
      - 203.0.113.10
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: example-deployment
    spec:
      selector:
        matchLabels:
          app: nginx
      replicas: 2
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.15.9-alpine
            command:
            - /bin/sh
            - "-c"
            - |
              cat > /usr/share/nginx/html/index.html <<EOF
              <html>
               <head><title>Example Kubernetes Service</title></head>
               <body>
                <h1>Welcome to Example Kubernetes Service!</h1>
                <p>If you see this page, your Kubernetes cluster is up and running.</p>
               </body>
              </html>
              EOF
              exec nginx -g 'daemon off;'
            ports:
            - name: http
              containerPort: 80
    EOF

.. important::

    Do not forget to change the **External IP** to the correct address!

    From above:

    .. code::

        externalIPs:
        - 203.0.113.10

Then just simply apply this manifest and Kubernetes will take care of the rest (it may take a while to download docker images):

.. prompt:: text [master]# auto

    [master]# kubectl apply -f ~/example.yaml

You can check that your application is ready if you see an output like this:

.. prompt:: text [master]# auto

   [master]# kubectl get pods
   NAME                                  READY   STATUS    RESTARTS   AGE
   example-deployment-6f8c74c65f-6ddch   1/1     Running   0          41s
   example-deployment-6f8c74c65f-lw5sp   1/1     Running   0          41s

Validate from the command line that the service works, e.g.:

.. prompt:: text $ auto

    $ curl 203.0.113.10          # put master node IP address
    <html>
     <head><title>Example Kubernetes Service</title></head>
     <body>
      <h1>Welcome to Example Kubernetes Service!</h1>
      <p>If you see this page, your Kubernetes cluster is up and running.</p>
     </body>
    </html>

Or, in the web browser, e.g.:

|image-kubetest|

2. External IP with LoadBalancer
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If we configured the :ref:`LoadBalancer <k8s_loadbalancer>` then we can improve on the previous example.

We expect that the intended **External IP** stayed the same (``203.0.113.10`` - **change to your setup!**) and that we correctly setup the LoadBalancer config, e.g.:

.. prompt:: text [master]# auto

    [master]# kubectl describe configmap config -n metallb-system
    Name:         config
    Namespace:    metallb-system
    Labels:       <none>
    Annotations:
    Data
    ====
    config:
    ----
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 203.0.113.10-203.0.113.10

    Events:  <none>

.. note::

   In this example we have a one range with only one loadbalanced IP address.

.. important::

    This time the Kubernetes Appliance is using private virtual network and the public IP can be attached as an **External Alias** - as long as IP packets with this address arrive to the Appliance (i.e. the address is routed correctly).

    In any case this IP cannot be actually assigned otherwise LoadBalancer will fail to work.

Create the following manifest (it is the same from before but now is shorter by leaving the service definition):

.. prompt:: text [master]# auto

    [master]# cat - >~/example.yaml <<EOF
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: example-deployment
    spec:
      selector:
        matchLabels:
          app: nginx
      replicas: 2
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.15.9-alpine
            command:
            - /bin/sh
            - "-c"
            - |
              cat > /usr/share/nginx/html/index.html <<EOF
              <html>
               <head><title>Example Kubernetes Service</title></head>
               <body>
                <h1>Welcome to Example Kubernetes Service!</h1>
                <p>If you see this page, your Kubernetes cluster is up and running.</p>
               </body>
              </html>
              EOF
              exec nginx -g 'daemon off;'
            ports:
            - name: http
              containerPort: 80
    EOF

Next step is the same - apply the manifest (it may take a while for the Kubernetes to download docker images):

.. prompt:: text [master]# auto

    [master]# kubectl apply -f ~/example.yaml

You can check that your application is ready if you see an output like this:

.. prompt:: text [master]# auto

   [master]# kubectl get pods
   NAME                                  READY   STATUS    RESTARTS   AGE
   example-deployment-6f8c74c65f-6ddch   1/1     Running   0          41s
   example-deployment-6f8c74c65f-lw5sp   1/1     Running   0          41s

This time we expose our application as `Service Type LoadBalancer <https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer>`_:

.. prompt:: text [master]# auto

    [master]# kubectl expose deployment example-deployment --type=LoadBalancer --name=example-service --load-balancer-ip=203.0.113.10

Check the service status:

.. prompt:: text [master]# auto

    [master]# kubectl get services example-service
    NAME              TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
    example-service   LoadBalancer   10.97.166.250   203.0.113.10     80:31583/TCP   9s

Validate from the command line that the service works, e.g.:

.. prompt:: text $ auto

    $ curl 203.0.113.10
    <html>
     <head><title>Example Kubernetes Service</title></head>
     <body>
      <h1>Welcome to Example Kubernetes Service!</h1>
      <p>If you see this page, your Kubernetes cluster is up and running.</p>
     </body>
    </html>

Or, in the web browser, e.g.:

|image-kubetest|

.. important::

    This simple example deployment could not properly demonstrate the advantages of the LoadBalancer type of service.

    It's benefits are leveraged when there is more than one Kubernetes nodes. If the **External IP** is used without LoadBalancer then this IP will be bound to exactly one node and if this node becomes unavailable so does the service.

    Such problem does not occur with the LoadBalancer because it will **failover** the **External IP** onto another healthy node.

Shutdown Application
--------------------

Destroy the example application at the end, e.g.:

.. prompt:: text [master]# auto

    [master]# kubectl delete service example-service
    service "example-service" deleted
    [master]# kubectl delete deployment example-deployment
    deployment.extensions "example-deployment" deleted

.. note::

    More examples can be found in the `Kubernetes Quick Start Section <https://docs.opennebula.io/stable/quick_start/usage_basics/running_kubernetes_clusters.html>`_ of the OpenNebula documentation.

.. |image-download| image:: /images/kubernetes/kubernetes-download.png
.. |image-worker-values| image:: /images/kubernetes/kubernetes-worker-values.png
.. |image-context-vars| image:: /images/kubernetes/kubernetes-context-vars.png
   :scale: 100%
.. |image-ui-login| image:: /images/kubernetes/kubernetes-ui-login.png
.. |image-ui-create| image:: /images/kubernetes/kubernetes-ui-create.png
.. |image-kubetest| image:: /images/kubernetes/kubernetes-kubetest.png
.. |image-oneflow-part1| image:: /images/kubernetes/kubernetes-oneflow-part1.png
.. |image-oneflow-part2| image:: /images/kubernetes/kubernetes-oneflow-part2.png
.. |image-oneflow-part3| image:: /images/kubernetes/kubernetes-oneflow-part3.png
.. |image-oneflow-part4| image:: /images/kubernetes/kubernetes-oneflow-part4.png
.. |image-oneflow-part5| image:: /images/kubernetes/kubernetes-oneflow-part5.png
.. |image-oneflow-part6| image:: /images/kubernetes/kubernetes-oneflow-part6.png
.. |image-k8s-certified-logo| image:: /images/kubernetes/certified-kubernetes-logo.svg
   :height: 120
   :align: middle
   :alt: CNCF Certified Logo of Kubernetes
   :target: https://landscape.cncf.io/selected=open-nebula-kubernetes-appliance
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
