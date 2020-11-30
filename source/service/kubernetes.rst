.. _service_k8s:

==================
Service Kubernetes
==================

OpenNebula `Marketplace Appliance <https://marketplace.opennebula.systems/appliance/547ecdff-f392-43b9-abc9-5f10a9fa7aff>`_  with preinstalled `Kubernetes <https://kubernetes.io/>`_ service.

Without any parameters provided, the appliance deploys as a single Kubernetes master node. Appliance can be customized with :ref:`contextualization <k8s_context_param>` parameters to support manually managed multi-node deployments, or automatically managed multi-node cluster as OpenNebula `OneFlow service <http://docs.opennebula.org/stable/advanced_components/application_flow_and_auto-scaling/appflow_use_cli.html>`_.

.. include:: shared/features.txt
* **Only single master clusters supported**.
* Preconfigured with Canal CNI networking (`Calico <https://www.projectcalico.org/>`_, `Flannel <https://github.com/coreos/flannel>`_).
* Optional: :ref:`Kubernetes Dashboard <k8s_dashboard>`.

Platform Notes
==============

Appliance components versions:

.. table::
    :widths: 100 50 40

    +-----------------------------+------------------------------------------------+
    | Component                   | Version                                        |
    +=============================+==============+=================================+
    | Kubernetes                  | 1.18.10      |                                 |
    +-----------------------------+--------------+                                 |
    | Docker                      | 19.03 CE     |                                 |
    +-----------------------------+--------------+ |image-k8s-certified-logo-1.18| |
    | Calico                      | 3.13         |                                 |
    +-----------------------------+--------------+                                 |
    | Contextualization package   | 5.12.0.2     |                                 |
    +-----------------------------+--------------+---------------------------------+

Requirements:

* OpenNebula 4.14 - 5.12
* `OneFlow <http://docs.opennebula.org/stable/advanced_components/application_flow_and_auto-scaling/index.html>`_ and `OneGate <http://docs.opennebula.org/stable/advanced_components/application_insight/index.html>`_ for multi-node orchestration
* Min. Memory: 3 GB (master), 1 GB (worker)
* Min. Cores (VCPU): 2 (master), 1 (worker)

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <k8s_context_param>` parameters:

|image-context-vars|

.. note::

    The first input for **K8s master node address** is important and the value may differ for the master node and the worker nodes in the same cluster. For example for the master node,  it can specify a network from which an IP will be derived. For the worker nodes, it must be a real IP address (in all cases). More about this in :ref:`contextualization <k8s_context_param>`. The following two inputs (**K8s token** and **K8s hash**) are relevant for worker nodes only and they need to be set with values stored on the master node in :ref:`/etc/one-appliance/config <one_service_logs>`. Below is an :ref:`example <report_file_example>` of such file. These three inputs are mandatory for the worker node to join the cluster - **all of them must be set**. Otherwise, a new single master node will be created.

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

Contextualization parameters provided in the Virtual Machine template controls the initial VM configuration. Except for the `common set <http://docs.opennebula.org/stable/operation/references/template.html#context-section>`_ of parameters supported by every appliance on the OpenNebula Marketplace, there are few specific to the particular service appliance. The parameters should be provided in the ``CONTEXT`` section of the Virtual Machine template, read the OpenNebula `Operation Guide <http://docs.opennebula.org/stable/operation/vm_setup/kvm.html#set-up-the-virtual-machine-template>`__ for more details.

===================================== ========= ==================== ========= ======== ===========
Parameter                             Mandatory Default              Stage     Role     Description
===================================== ========= ==================== ========= ======== ===========
``ONEAPP_K8S_ADDRESS``                          routable IP          configure all      K8s master node address or network (in CIDR format)
``ONEAPP_K8S_TOKEN``                                                 configure worker   K8s token - to join worker node to the cluster
``ONEAPP_K8S_HASH``                                                  configure worker   K8s hash - to join worker node to the cluster
``ONEAPP_K8S_NODENAME``                         hostname             configure master   K8s Master Node Name
``ONEAPP_K8S_PORT``                             ``6443``             configure master   K8s API port on which nodes communicate
``ONEAPP_K8S_PODS_NETWORK``                     ``10.244.0.0/16``    configure master   K8s pods network - pods will have IP from this range
``ONEAPP_K8S_ADMIN_USERNAME``                   ``admin-user``       configure master   UI dashboard admin account - K8s secret's token is prefixed with this name
===================================== ========= ==================== ========= ======== ===========

If no contextualization parameter is provided, the appliance will create a :ref:`single node Kubernetes cluster <k8s_master_node>`. By default, it will listen on port ``6443`` of an IP address assigned to a default interface. You can still add more nodes to this cluster later by providing contextualization parameters required by a :ref:`worker node <k8s_worker_node>`.

.. _k8s_master_node:

Master Node (or Single Node Deployment)
---------------------------------------

In this type of deployment, you don't need to setup anything in advance. The appliance will bootstrap a fully functional single node Kubernetes cluster which can be extended by other worker nodes at any time later.

For other cases when the default behavior is not sufficient, you can contextualize the appliance with a few parameters. The most important is ``ONEAPP_K8S_ADDRESS``.

.. important::

   On the **master node**, the parameter ``ONEAPP_K8S_ADDRESS`` may be set either to the IP address or to a subnet (see options below). The purpose of this parameter is to pin Kubernetes communication to a designated interface and a network. So it will not be exposed on the default interface.  For the **worker nodes**, the value must be strictly the advertised IP address of the master node!

Master node's ``ONEAPP_K8S_ADDRESS`` parameter can contain:

   * an IP address, e.g. ``192.168.122.10``
   * a network address, e.g. ``192.168.122.0`` (it will pick an IP on an interface which has route for this network)
   * a subnet, e.g. ``192.168.122.0/24`` (as above)
   * or an IP address with a subnet prefix, e.g.: ``192.168.122.10/24``

Use any format which makes sense for your scenario.

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

``ONEAPP_K8S_ADMIN_USERNAME`` defaults to ``admin-user``. It is the service account name under which a Kubernetes token is created to enable login into :ref:`UI dashboard <k8s_dashboard>`. The token is stored in :ref:`/etc/one-appliance/config <one_service_logs>` as ``k8s_ui_login_token`` (you can see one in the :ref:`example <report_file_example>` above).

The only reason to change the default is to easily find and manage the mentioned token with a command:

.. prompt:: text [master]# auto

    [master]# kubectl -n kube-system get secret | grep ${ONEAPP_K8S_ADMIN_USERNAME}-token

.. important::

   Handle carefully the Kubernetes cluster token (``k8s_token`` in :ref:`/etc/one-appliance/config <one_service_logs>`) as it **never** expires. To use short-living tokens, delete the existing one and create a new token as described in the `kubeadm documentation <https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#join-nodes>`_.

.. _k8s_worker_node:

Worker Node
-----------

New nodes can join already running Kubernetes cluster, if they are provided with following contextualization parameters:

* ``ONEAPP_K8S_ADDRESS`` - master node's IP address (**NOT** a network address or a subnet!)
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

The Kubernetes appliance (optionally) integrates with the OpenNebula `OneFlow service <http://docs.opennebula.org/stable/advanced_components/application_flow_and_auto-scaling/appflow_use_cli.html>`_ to enable one-click multi-node cluster deployment. A template for OneFlow is not part of the imported Marketplace appliance, it must be created in each OpenNebula manually. The template can be created over the Sunstone wizard, or by providing a JSON specification.

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
- Pick the ``Service Kubernetes - KVM`` (VM template)
- In **Advanced role parameters**, set **VM template content** to: ``ONEGATE_ENABLE="YES"``

The current status should resemble this picture with important parts highlighted:

|image-oneflow-part1|

Step 3 - Role Worker
^^^^^^^^^^^^^^^^^^^^

As the next step, we setup a worker role:

- Click on the blue button **+** next to **Roles**
- Fill the role name as ``worker`` (**nothing else!**)
- Optional: Set the **Number of VMs** run initially
- Pick the ``Service Kubernetes - KVM`` (VM template)
- In **Parent roles**, set **master** as a parent role
- In **Advanced role parameters**, set **VM template content** to: ``ONEGATE_ENABLE="YES"``

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

Or, use the `OneFlow CLI <http://docs.opennebula.org/stable/advanced_components/application_flow_and_auto-scaling/appflow_use_cli.html#managing-service-templates>`_.

Instantiate Service
~~~~~~~~~~~~~~~~~~~

To run the multi-node service:

- Go to **Templates → Services** tab
- Select the Kubernetes service template
- Click on the green **+** button at the top and **Instantiate**
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

Then just simply apply this manifest and Kubernetes will take care of the rest (it may take a while to download docker images):

.. prompt:: text [master]# auto

    [master]# kubectl apply -f ~/example.yaml

You can check that your application is ready if you see an output like this:

.. prompt:: text [master]# auto

   [master]# kubectl get pods
   NAME                                  READY   STATUS    RESTARTS   AGE
   example-deployment-6f8c74c65f-6ddch   1/1     Running   0          41s
   example-deployment-6f8c74c65f-lw5sp   1/1     Running   0          41s

Validate the service works from the command line, e.g.:

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

Shutdown Application
--------------------

Destroy the example application at the end, e.g.:

.. prompt:: text [master]# auto

    [master]# kubectl delete service example-service
    service "example-service" deleted
    [master]# kubectl delete deployment example-deployment
    deployment.extensions "example-deployment" deleted

.. |image-download| image:: /images/kubernetes/kubernetes-download.png
.. |image-worker-values| image:: /images/kubernetes/kubernetes-worker-values.png
.. |image-context-vars| image:: /images/kubernetes/kubernetes-context-vars.png
.. |image-ui-login| image:: /images/kubernetes/kubernetes-ui-login.png
.. |image-ui-create| image:: /images/kubernetes/kubernetes-ui-create.png
.. |image-kubetest| image:: /images/kubernetes/kubernetes-kubetest.png
.. |image-oneflow-part1| image:: /images/kubernetes/kubernetes-oneflow-part1.png
.. |image-oneflow-part2| image:: /images/kubernetes/kubernetes-oneflow-part2.png
.. |image-oneflow-part3| image:: /images/kubernetes/kubernetes-oneflow-part3.png
.. |image-oneflow-part4| image:: /images/kubernetes/kubernetes-oneflow-part4.png
.. |image-oneflow-part5| image:: /images/kubernetes/kubernetes-oneflow-part5.png
.. |image-oneflow-part6| image:: /images/kubernetes/kubernetes-oneflow-part6.png
.. |image-k8s-certified-logo-1.18| image:: /images/kubernetes/certified-kubernetes-1.18-pantone.png
   :height: 120
   :align: middle
   :alt: CNCF Certified Logo of Kubernetes 1.18
   :target: https://landscape.cncf.io/selected=open-nebula-kubernetes-appliance
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
