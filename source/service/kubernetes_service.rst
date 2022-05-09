.. _service_ubuntu_k8s:

=============================
Kubernetes (K8s) 1.23 Service
=============================

OpenNebula `OneFlow service <https://docs.opennebula.io/stable/management_and_operations/multivm_service_management/appflow_use_cli.html>`_ / `Marketplace Appliance <https://marketplace.opennebula.io/appliance/b5033eba-cd31-487e-892a-035cd70441ef>`_  with preinstalled `Kubernetes <https://kubernetes.io/>`_ software.

The service appliance consists of four distinct roles:

* **vnf**: `Virtual Network Functions <https://docs.opennebula.io/appliances/service/vnf.html>`_, a `Linux Virtual Server <http://www.linuxvirtualserver.org/>`_ load-balancer implementation for the Control-Plane.
* **master**: Control-Plane nodes themselves.
* **worker**: Nodes to run your workloads on.
* **storage**: Nodes to keep your persistent storage (**PV**/**PVC**) on.

Each role is preconfigured to deploy a single server to work as a **non-Highly-Available (non-HA)** cluster:

* **vnf**: Single server managed by `keepalived <https://keepalived.readthedocs.io/en/latest/introduction.html>`_.
* **master**: Single dedicated Control-Plane node.
* **worker**: Single worker node.
* **storage**: Single dedicated storage node to keep your Longhorn replicas on.

.. important::

    Each role can be scaled up to achieve **Highly-Available (HA)** setup.

.. include:: shared/features-ubuntu.txt
* Multi-master ready.
* Preconfigured with Canal CNI networking (`Calico <https://www.projectcalico.org/>`_, `Flannel <https://github.com/coreos/flannel>`_).
* Preconfigured with `CNCF <https://www.cncf.io/>`_ Longhorn distributed storage solution for Kubernetes (`Longhorn <https://longhorn.io/>`_).
* Installed with MetalLB LoadBalancer provider (`MetalLB <https://metallb.universe.tf/>`_).

Platform Notes
==============

Component versions
------------------

.. table::
    :widths: 100 50 40

    +-----------------------------+------------------------------------------------------------------------------------------------------------------+
    | Component                   | Version                                                                                                          |
    +=============================+=====================================================================================+============================+
    | `Kubernetes Appliance 1.23 <https://marketplace.opennebula.io/appliance/b5033eba-cd31-487e-892a-035cd70441ef>`_   |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Ubuntu                      | 20.04.4 LTS                                                                         |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Kubernetes                  | 1.23.6                                                                              |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+ |image-k8s-certified-logo| |
    | Docker                      | 20.10.14 CE                                                                         |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Calico                      | 3.22                                                                                |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Longhorn                    | 1.2.4                                                                               |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | MetalLB                     | 0.12.1                                                                              |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+                            |
    | Contextualization package   | 6.4.0                                                                               |                            |
    +-----------------------------+-------------------------------------------------------------------------------------+----------------------------+

Requirements
------------

* OpenNebula 6.2 - 6.4
* `OneFlow <https://docs.opennebula.io/stable/management_and_operations/multivm_service_management/overview.html>`_ and `OneGate <https://docs.opennebula.io/stable/management_and_operations/multivm_service_management/onegate_usage.html>`_ for multi-node orchestration
* Min. Memory per VM: 512 MB (**vnf**), 3 GB (**master**), 3 GB (**worker**), 3 GB (**storage** worker)
* Min. Cores (VCPU) per VM: 1 (**vnf**), 2 (**master**), 2 (**worker**), 2 (**storage** worker)

Quick Start
===========

.. _k8s_instantiate_service:

Instantiate the service (from the Sunstone UI)
----------------------------------------------

Download the service:

- Go to **Storage → Apps** tab (on the left menu panel)
- Find and select the ``Service Kubernetes 1.23`` app
- Click the **Import into Datastore** button at the top

|image-oneflow-step-01|

- Select a datastore to save your resources, then click the **Donwload** button

|image-oneflow-step-02|

Run the service:

- Go to **Templates → Services** tab (on the left menu panel)
- Find and select the ``Service Kubernetes 1.23`` service template
- Click on the green **+** button at the top and **Instantiate**

|image-oneflow-step-03|

Enter parameters:

- Enter the name for your new service in the ``Service name`` box
- Select destination VNET to deploy your new service into

|image-oneflow-step-04|

- Enter a single IPv4 address into the ``Default Gateway VIP`` box (it should be a free IPv4 address from the ``Private`` VNET)
- Enter a single IPv4 address into the ``Control Plane Endpoint VIP`` box (it should be a free IPv4 address from the ``Private`` VNET)
- Optionally enter a single IPv4 address range for the ``MetalLB`` load-balancer
- Click the **Instantiate** button at the top

|image-oneflow-step-05|

.. important::

    The VNF Appliance is based on ``keepalived`` and can be scaled up to run on multiple VMs.

    The ``Default Gateway VIP`` address is set on the **active** VNF node. The same address is later set as the **default gateway address** on every Kubernetes node (which allows all outgoing traffic comming from the private VNET to be NATed on VNF).

    The ``Control Plane Endpoint VIP`` is a **LVS-DR** (Direct-Routing) load-balancer used for forming the Kubernetes Control-Plane. Because of the LVS-DR caveats it's not currently possible to reuse the endpoint to connect to the Kubernetes API from outside.

Follow the service deployment status in **Instances → Services** and **Instances → VMs** tabs.

|image-oneflow-step-06|

|image-oneflow-step-07|

.. note::

    Your new Kubernetes cluster should be operational in a few minutes.

.. important::

    After your cluster is ready, you can manually scale up the number of VMs in any of the roles, this is especially recommended for the **worker** role to match your workload. You can also increase CPU and memory resources for a role by editing VM templates.

    - Go to **Instances → Services** tab (on the left menu panel)
    - Find your service, go to **Roles → worker** and click "+Scale" button

    |image-rescale-step-01|

    - Adjust the number of VMs and confirm

    |image-rescale-step-02|

.. important::

    After your cluster is ready, you can manually resize the dedicated storage disk for any of the storage VMs.

    - Go to **Instances → VMs** tab (on the left menu panel)
    - Find and select a **storage** VM, then click the "resize" button on the right

    |image-storage-step-01|

    - Adjust the size and click the ``Resize`` button to confirm

    |image-storage-step-02|

.. _k8s_check_status:

Check Kubernetes cluster status
-------------------------------

Log into a **master node** over SSH and check the cluster contains all the deployed nodes.

For example:

.. prompt:: text [master]# auto

   [master]# kubectl get nodes -o wide
   NAME                        STATUS   ROLES                  AGE    VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
   onekube-ip-172-20-100-101   Ready    control-plane,master   7m     v1.23.6   172.20.100.101   <none>        Ubuntu 20.04.4 LTS   5.4.0-109-generic   docker://20.10.15
   onekube-ip-172-20-100-102   Ready    <none>                 9m     v1.23.6   172.20.100.102   <none>        Ubuntu 20.04.4 LTS   5.4.0-109-generic   docker://20.10.15
   onekube-ip-172-20-100-103   Ready    <none>                 9m     v1.23.6   172.20.100.103   <none>        Ubuntu 20.04.4 LTS   5.4.0-109-generic   docker://20.10.15

.. _k8s_contextualization:

Contextualization
=================

Custom attributes
-----------------

The service appliance is designed to use couple of `custom attributes <https://docs.opennebula.io/6.2/management_and_operations/multivm_service_management/appflow_use_cli.html#using-custom-attributes>`_ to provide easier configuration across multiple VM templates.

====================================== ========= ======================= ========= ========================= ===========
Parameter                              Mandatory Default                 Stage     Role                      Description
====================================== ========= ======================= ========= ========================= ===========
``ONEAPP_VROUTER_ETH1_VIP0``                                             configure vnf master worker storage Default Gateway VIP (IPv4)
``ONEAPP_VNF_LB0_IP``                  ``YES``                           configure vnf master worker storage Control Plane Endpoint VIP (IPv4)
``ONEAPP_K8S_PORT``                              ``6443``                configure master worker storage     Kubernetes API port on which nodes communicate
``ONEAPP_K8S_PODS_NETWORK``                      ``10.244.0.0/16``       configure master worker storage     Kubernetes pod network - pods will have IP from this range
``ONEAPP_K8S_EXTRA_SANS``                        ``localhost,127.0.0.1`` configure master                    ApiServer extra certificate SANs
``ONEAPP_K8S_LOADBALANCER_RANGE``                                        configure master worker storage     LoadBalancer IP range (IPv4-IPv4)
``ONEAPP_K8S_LOADBALANCER_CONFIG``                                       configure master worker storage     Custom LoadBalancer config (encoded in Base64)
``ONEAPP_STORAGE_DEVICE``              ``YES``   ``/dev/vdb``            configure storage                   Attached block device used by Longhorn to store Persistent Volume (PV) data
``ONEAPP_STORAGE_FILESYSTEM``                    ``xfs``                 configure storage                   Filesystem type to init Longhorn storage device
``ONEAPP_VNF_NAT4_ENABLED``                      ``YES``                 configure vnf                       Enable NAT for the whole Kubernetes cluster
``ONEAPP_VNF_NAT4_INTERFACES_OUT``               ``eth0``                configure vnf                       NAT - Outgoing (public) interfaces
``ONEAPP_VNF_ROUTER4_ENABLED``                   ``YES``                 configure vnf                       Enable IPv4 forwarding for selected NICs
``ONEAPP_VNF_ROUTER4_INTERFACES``                ``eth0,eth1``           configure vnf                       IPv4 Router - NICs selected for IPv4 forwarding
====================================== ========= ======================= ========= ========================= ===========

.. _k8s_storage_role:

Storage (Longhorn) role
-----------------------

Storage nodes are deployed by the **storage** role. They are by default labeled with ``node.longhorn.io/create-default-disk: "true"`` and tainted with ``NoSchedule``, which allows Longhorn to hold ``persistent volume (PV)`` data on them exclusively.
Deployment of regular pods onto the **storage** nodes is prevented, instead they are deployed into **worker** nodes only.

Longhorn deploys the following default storage class:

.. code::

    kind: StorageClass
    apiVersion: storage.k8s.io/v1
    metadata:
      name: longhorn
    provisioner: driver.longhorn.io
    allowVolumeExpansion: true
    reclaimPolicy: Delete
    volumeBindingMode: Immediate
    parameters:
      fsType: "ext4"
      numberOfReplicas: "3"
      staleReplicaTimeout: "2880"
      fromBackup: ""

For your convenience the following "**retain**" version of the default storageclass is deployed as well:

.. code::

    kind: StorageClass
    apiVersion: storage.k8s.io/v1
    metadata:
      name: longhorn-retain
    provisioner: driver.longhorn.io
    allowVolumeExpansion: true
    reclaimPolicy: Retain
    volumeBindingMode: Immediate
    parameters:
      fsType: "ext4"
      numberOfReplicas: "3"
      staleReplicaTimeout: "2880"
      fromBackup: ""

.. important::

    When the **longhorn-retain** storage class is used the ``persistent volume (PV)`` must be destroyed manually even if ``persistent volume claim (PVC)`` is destroyed already. **That prevents data from being accidentally deleted**.

.. _k8s_loadbalancer_service:

LoadBalancer Service (K8s)
--------------------------

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
          - 172.20.100.200-172.20.100.250
          - 172.20.101.200-172.20.101.250
          - 172.20.102.200-172.20.102.250

The most straightforward way is to just set the range via ``ONEAPP_K8S_LOADBALANCER_RANGE``,

.. code::

    ONEAPP_K8S_LOADBALANCER_RANGE=172.20.100.124-172.20.100.128

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
      - 172.20.100.124-172.20.100.128

    Events:  <none>

As we can see we have one range configured (which is routable) - now we can attempt to deploy the service.

.. prompt:: text [master]# auto

    [master]# kubectl run nginx --image=nginx --port 80
    pod/nginx created
    [master]# kubectl expose pod nginx --type=LoadBalancer
    service/nginx exposed
    [master]# kubectl get services
    NAME         TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
    kubernetes   ClusterIP      10.96.0.1      <none>           443/TCP        125m
    nginx        LoadBalancer   10.101.121.5   172.20.100.124   80:32546/TCP   8s

Our service ``nginx`` is exposed on the next free loadbalanced IP from the provided range or ranges - in this case ``172.20.100.124`` - and can be reached outside of the Kubernetes appliance as expected:

.. prompt:: text [remote]$ auto

    [remote]$ curl -fs http://172.20.100.124 | html2text
    ****** Welcome to nginx! ******
    If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.
    For online documentation and support please refer to nginx.org.
    Commercial support is available at nginx.com.
    Thank you for using nginx.

Generic IP range
~~~~~~~~~~~~~~~~

Sometimes the range cannot be known in advance and so we can configure and force the LoadBalancer to act on **every** address with this workaround:

.. code::

    ONEAPP_K8S_LOADBALANCER_RANGE=0.0.0.0-255.255.255.255

This time we can expose the service on a desired address (with ``--load-balancer-ip``) which we know that it is safe to use:

.. prompt:: text [master]# auto

    [master]# kubectl expose pod nginx --type=LoadBalancer --load-balancer-ip=172.20.100.128
    [master]# kubectl get service/nginx
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
    nginx        LoadBalancer   10.110.6.11   172.20.100.128   80:32172/TCP   11s

.. important::

    Use this only when you know what you are doing and can secure that no one will abuse this and create conflicts on the network!

.. _k8s_tutorial_service:

Example Application
===================

In this section, we demonstrate how to deploy a simple application.

.. note::

    All the actions need to be executed from the **master** node, or from a host which has ``kubectl`` CLI :ref:`installed and configured <k8s_use_remotely>`.

Deploy Application
------------------

LoadBalancer service and PVC (a complete example)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Check ``MetalLB``'s config:

.. prompt:: text [master]# auto

    [master]# kubectl -n metallb-system describe configmap/config
    Name:         config
    Namespace:    metallb-system
    Labels:       <none>
    Annotations:  <none>

    Data
    ====
    config:
    ----
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 172.20.100.124-172.20.100.128

    BinaryData
    ====

    Events:  <none>

Check ``Longhorn``'s storage classes:

.. prompt:: text [master]# auto

    [master]# kubectl get storageclass
    NAME              PROVISIONER          RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
    longhorn          driver.longhorn.io   Delete          Immediate           true                   29m
    longhorn-retain   driver.longhorn.io   Retain          Immediate           true                   29m

Create the following multi-resource manifest:

.. prompt:: text [master]# auto

    [master]# cat - >~/example.yaml <<'EOF'
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: example-deployment
    spec:
      selector:
        matchLabels:
          app: nginx
      replicas: 1
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:stable-alpine
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
            volumeMounts:
            - name: nginx
              mountPath: /usr/share/nginx/html/
          volumes:
          - name: nginx
            persistentVolumeClaim:
              claimName: nginx
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
    spec:
      selector:
        app: nginx
      type: LoadBalancer
      ports:
      - name: http
        protocol: TCP
        port: 80
        targetPort: 80
    ---
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: nginx
    spec:
      accessModes:
      - ReadWriteOnce
      volumeMode: Filesystem
      resources:
        requests:
          storage: 4Gi
      storageClassName: longhorn-retain
    EOF

Apply the manifest (it may take a while for the Kubernetes to download docker images):

.. prompt:: text [master]# auto

    [master]# kubectl apply -f ~/example.yaml
    deployment.apps/example-deployment unchanged
    service/nginx created
    persistentvolumeclaim/nginx created

List all the created resources:

.. prompt:: text [master]# auto

    [master]# kubectl get deployment,pods,services,persistentvolumeclaims,persistentvolumes
    NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/example-deployment   1/1     1            1           23s

    NAME                                      READY   STATUS    RESTARTS   AGE
    pod/example-deployment-56dcfb6478-549pz   1/1     Running   0          23s

    NAME                 TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
    service/kubernetes   ClusterIP      10.96.0.1       <none>           443/TCP        32m
    service/nginx        LoadBalancer   10.109.185.45   172.20.100.124   80:32624/TCP   23s

    NAME                          STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
    persistentvolumeclaim/nginx   Bound    pvc-933533b2-1c58-459f-9e97-9c710a57063f   4Gi        RWO            longhorn-retain   23s

    NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM           STORAGECLASS      REASON   AGE
    persistentvolume/pvc-933533b2-1c58-459f-9e97-9c710a57063f   4Gi        RWO            Retain           Bound    default/nginx   longhorn-retain            21s

.. important::

    The requested ``peristent volume (PV)`` has been mounted inside the nginx container (and will follow to other **worker** nodes when the pod is evicted):

    .. prompt:: text [master]# auto

        [master]# kubectl exec -t pod/example-deployment-56dcfb6478-549pz -- df -h | grep -A1 pvc
        /dev/longhorn/pvc-933533b2-1c58-459f-9e97-9c710a57063f
                                  3.9G     16.0M      3.8G   0% /usr/share/nginx/html

Verify if the LoadBalancer service works and pod correctly serves HTTP content:

.. prompt:: text $ auto

    $ curl -fs http://172.20.100.124 | html2text
    ****** Welcome to Example Kubernetes Service! ******
    If you see this page, your Kubernetes cluster is up and running.

Shutdown Application
--------------------

Destroy the example application:

.. prompt:: text [master]# auto

    [master]# kubectl delete -f ~/example.yml
    deployment.apps "example-deployment" deleted
    service "nginx" deleted
    persistentvolumeclaim "nginx" deleted

.. important::

    To reuse a "**Released**" ``persistent volume (PV)`` execute ``kubectl patch pv <name> -p '{"spec":{"claimRef": null}}'``, which should change
    the status of the volume to "**Available**", then you can re-create the ``persistent volume claim (PVC)`` with the same exact name as before.

.. _k8s_use_remotely:

Use Kubernetes remotely
=======================

Connect to Kubernetes API (directly)
------------------------------------

To control the Kubernetes cluster remotely, you need to have the ``kubectl`` CLI tool installed on your system (workstation, laptop). Follow the official `installation guide <https://kubernetes.io/docs/tasks/tools/install-kubectl/>`_. When finished, you can validate the correct installation by running:

.. prompt:: text [remote]$ auto

    [remote]$ kubectl --help

You also need to have the configuration with cluster master IP (or the Control-Plane Endpoint) address and access keys. This configuration can be taken from any **master** node from a file ``/etc/kubernetes/admin.conf`` and put on your remote system into ``~/.kube/config``.

For example (place a valid master node IP address):

.. prompt:: text [remote]$ auto

    [remote]$ mkdir -p ~/.kube/
    [remote]$ scp root@172.20.100.4:/etc/kubernetes/admin.conf ~/.kube/config

After this you should be able to manage all resources from the remote location via ``kubectl/kustomize`` or ``helm``, etc.

.. note::

    The access configuration can be placed into any custom file, but the location has be explicitly configured. Via

    - env. variable, e.g. ``KUBECONFIG=${HOME}/admin.conf kubectl get nodes``
    - CLI argument, e.g. ``kubectl --kubeconfig=${HOME}/admin.conf get nodes``

Connect to Kubernetes API (via SSH)
-----------------------------------

By default the context parameter ``ONEAPP_K8S_EXTRA_SANS`` is set to **localhost,127.0.0.1** which allows to access Kubernetes API via SSH tunnels.

.. note::

    We recommend using the ``ProxyCommand`` SSH feature.

To download the **/etc/kubernetes/admin.conf** (kubeconfig) file:

.. prompt:: text [remote]$ auto

   [remote]$ mkdir -p ~/.kube/
   [remote]$ scp -o ProxyCommand='ssh -A root@10.2.11.200 -W %h:%p' root@172.20.1.101:/etc/kubernetes/admin.conf ~/.kube/config

.. note::

    The ``10.2.11.200`` is a **public** address of a VNF node, ``172.20.1.101`` is a **private** address of a master node (inside internal VNET).

To create SSH tunnel, forward ``6443`` port and query cluster nodes:

.. prompt:: text [remote]$ auto

   [remote]$ ssh -o ProxyCommand='ssh -A root@10.2.11.200 -W %h:%p' -L 6443:localhost:6443 root@172.20.1.101

..and then in another terminal:

.. prompt:: text [remote]$ auto

   [remote]$ kubectl get nodes
   NAME                      STATUS   ROLES                  AGE    VERSION
   onekube-ip-172-20-1-101   Ready    control-plane,master   13m    v1.23.6
   onekube-ip-172-20-1-102   Ready    <none>                 11m    v1.23.6
   onekube-ip-172-20-1-103   Ready    <none>                 11m    v1.23.6

.. important::

    You must make sure that the cluster endpoint inside the kubeconfig file (**~/.kube/config**) points to **localhost**, for example:

    .. prompt:: text [remote]$ auto

        gawk -i inplace -f- ~/.kube/config <<'EOF'
        /^    server: / { $0 = "    server: https://localhost:6443" }
        { print }
        EOF

.. _k8s_certificate_sans:

Update Kubernetes PKI to use custom certificate SANs (in an existing cluster)
-----------------------------------------------------------------------------

If you intend to connect to Kubernetes API using SSH tunnels you will probably need to add/update certificate **Subject Alternative Names (SANs)**. Here's how:

Obtain the ``ClusterConfiguration`` document:

.. prompt:: text [master]# auto

    [master]# kubectl -n kube-system get configmap kubeadm-config -o jsonpath='{.data.ClusterConfiguration}' > cc.yml

Edit the document to add custom SANs:

.. code::

    apiServer:
      certSANs:
      - "my.custom.domain"
      - "other.custom.domain"
      - "10.11.12.13"
      extraArgs:
        authorization-mode: Node,RBAC
      timeoutForControlPlane: 4m0s
    apiVersion: kubeadm.k8s.io/v1beta3
    certificatesDir: /etc/kubernetes/pki
    clusterName: kubernetes
    controlPlaneEndpoint: 172.20.100.2:6443
    controllerManager: {}
    dns: {}
    etcd:
      local:
        dataDir: /var/lib/etcd
    imageRepository: k8s.gcr.io
    kind: ClusterConfiguration
    kubernetesVersion: v1.23.6
    networking:
      dnsDomain: cluster.local
      podSubnet: 10.244.0.0/16
      serviceSubnet: 10.96.0.0/12
    scheduler: {}

**Move** (and backup) existing ApiServer Key and Certificate:

.. prompt:: text [master]# auto

    [master]# mv /etc/kubernetes/pki/apiserver.{key,crt} /var/tmp/

Generate new ApiServer Key and Certificate:

.. prompt:: text [master]# auto

    [master]# kubeadm init phase certs apiserver --config cc.yml
    [certs] Generating "apiserver" certificate and key
    [certs] apiserver serving cert is signed for DNS names [kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local my.custom.domain onekube-ip-172-20-100-101 other.custom.domain] and IPs [10.96.0.1 172.20.100.101 172.20.100.2 10.11.12.13]

.. important::

    If you have multiple masters you need to **copy**/replace existing ``/etc/kubernetes/pki/apiserver.{key,crt}`` files with newly generated ones to **all** masters.

Use **docker** to destroy the ``kube-apiserver`` container:

.. prompt:: text [master]# auto

    [master]# docker ps | awk '/kube-apiserver/ && !/pause/ {print $1}' | xargs -rn1 docker kill

.. important::

    If you have multiple masters you need to destroy ``kube-apiserver`` containers on **all** masters.

Update the ``kubeadm-config`` configmap:

.. prompt:: text [master]# auto

    [master]# kubeadm init phase upload-config kubeadm --config cc.yml
    [upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace

.. |image-oneflow-step-01| image:: /images/kubernetes_service/kubernetes_service_step_01.png
.. |image-oneflow-step-02| image:: /images/kubernetes_service/kubernetes_service_step_02.png
.. |image-oneflow-step-03| image:: /images/kubernetes_service/kubernetes_service_step_03.png
.. |image-oneflow-step-04| image:: /images/kubernetes_service/kubernetes_service_step_04.png
.. |image-oneflow-step-05| image:: /images/kubernetes_service/kubernetes_service_step_05.png
.. |image-oneflow-step-06| image:: /images/kubernetes_service/kubernetes_service_step_06.png
.. |image-oneflow-step-07| image:: /images/kubernetes_service/kubernetes_service_step_07.png
.. |image-rescale-step-01| image:: /images/kubernetes_service/kubernetes_role_scale_step_01.png
.. |image-rescale-step-02| image:: /images/kubernetes_service/kubernetes_role_scale_step_02.png
.. |image-storage-step-01| image:: /images/kubernetes_service/kubernetes_storage_resize_step_01.png
.. |image-storage-step-02| image:: /images/kubernetes_service/kubernetes_storage_resize_step_02.png
.. |image-k8s-certified-logo| image:: /images/kubernetes/certified-kubernetes-logo.svg
   :height: 120
   :align: middle
   :alt: CNCF Certified Logo of Kubernetes
   :target: https://landscape.cncf.io/selected=open-nebula-kubernetes-appliance
