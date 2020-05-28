.. _docker_appliance:

==============
Service Docker
==============

OpenNebula `Marketplace Appliance <https://marketplace.opennebula.io/appliance/a8330cf6-111a-11ea-9183-f0def1753696>`_  with preinstalled `Docker <https://docker.com>`_ service.

This Docker Appliance brings the Docker Engine pre-installed and the contextualization packages configured to create Docker Hosts in a single click. Alternatively you can prepare your own image, using your favourite distribution, as long as it’s supported by Docker Machine and it has the latest OpenNebula Contextualization packages.

* Based on the Ubuntu 16.04 x86-64 Linux distribution.
* No default login (local or SSH) password - must be provided via `contextualization <http://docs.opennebula.org/stable/operation/vm_setup/kvm.html#user-credentials>`_.

Platform Notes
==============

Appliance components versions:

============================= ==================
Component                     Version
============================= ==================
Docker                        19.03 CE
Contextualization package     5.10.0-1
============================= ==================

.. important::

   This appliance differs from the other service appliances - there is no three-stage contextualization support but only the standard VM contextualization.

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

.. _docker_appliance_configuration:

Docker Appliance Configuration
=========================================================

In order to prepare your cloud to serve Docker Engines please follow the next steps.

Step 1 - Create a Template
---------------------------------------------------------

KVM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

  Download the appliance from the apps marketplace:

  |img-marketplace-kvm|

  When the appliance is downloaded a template with the same name it's created. It's recomended to update the template with a network for make the vm accessible from outside, set the disk size, the root password or the ssh key.

  You can also create a template and include the appliance image in it.

  Once the template is ready you can instantiate it.

vCenter
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

  Download the appliance from the apps marketplace:

  |img-marketplace-vcenter|

  Create a vCenter template or update an existing one, the template must have a network for make the vm accessible from outside. Once the template is ready import both the template and the network into OpenNebula.

  From OpenNebula update the template and attach the appliance disk to the vm. Also it's recommended to update the context of the template for set the root password or to include an SSH key.

Step 2 - Customize The Template
---------------------------------------------------------

If you want to make any changes in the appliance and save them for latter use, you can set the image as persistent before launching the appliance. After the changes are performed, you need to shut the VM down and remove the persistent option from the image. This way you can create a golden image and new instances of the appliance won't overwrite it.

|image-persistent|

If you have already run the appliance as non persistent image you can take a look at Step 4 from the :ref:`Docker Application Usage <docker_appliance_usage>` guide.

.. _docker_appliance_usage:

Docker Appliance Usage
=========================================================

In order to use the Docker Appliance it is needed to have the appliance configured at the OpenNebula installation. You can find more info at :ref:`Docker Appliance Configuration <docker_appliance_configuration>`.

Step 1 - Instantiate the template
---------------------------------------------------------

First of all you need to identify the template. The default name is ``Ubuntu 16.04 with Docker``.

Then you just need to instantiate it and wait for it to be in ``running`` state.

|vm-running|


Step 2 - Running Hello World
---------------------------------------------------------

Once the VM is running you can connect over SSH or VNC. You can run the docker "Hello world" and make sure every thing it's running well.

.. prompt:: bash # auto

    # ssh root@<vm_ip>
    # root@ubuntu:~# docker run hello-world
      Unable to find image 'hello-world:latest' locally
      latest: Pulling from library/hello-world
      ca4f61b1923c: Pull complete
      Digest: sha256:97ce6fa4b6cdc0790cda65fe7290b74cfebd9fa0c9b8c38e979330d547d22ce1
      Status: Downloaded newer image for hello-world:latest

      Hello from Docker!
      This message shows that your installation appears to be working correctly.

Now you can see the "hello-world" image has been included in your images:

.. prompt:: bash # auto

    # root@ubuntu:~# docker images
      REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
      hello-world         latest              f2a91732366c        4 months ago        1.85kB

You can also see the container using the -a option (for show all the containers, including the ones that are not running):

.. prompt:: bash # auto

    # root@ubuntu:~# docker ps -a
      CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
      3a0189f6b0af        hello-world         "/hello"            9 minutes ago       Exited (0) 9 minutes ago                       flamboyant_mirzakhani


Step 3 - Update the Docker Version
---------------------------------------------------------

You can check your Docker version:

.. prompt:: bash # auto

    # root@ubuntu:~# docker version
      Client:
        Version:	    18.03.0-ce
        API version:	1.37
        Go version:	    go1.9.4
        Git commit:	    0520e24
        Built:	Wed Mar 21 23:10:01 2018
        OS/Arch:	    linux/amd64
        Experimental:	false
        Orchestrator:	swarm

      Server:
       Engine:
        Version:	    18.03.0-ce
        API version:	1.37 (minimum version 1.12)
        Go version:	    go1.9.4
        Git commit:	    0520e24
        Built:	Wed Mar 21 23:08:31 2018
        OS/Arch:	    linux/amd64
        Experimental:	false

And update it using the OS packages manager:

.. prompt:: bash # auto

    # root@ubuntu:~#apt-get update
    # root@ubuntu:~#apt-get upgrade

Step 4 - Update a Docker Image
---------------------------------------------------------

You can get an existing image and change it:

.. prompt:: bash # auto

    # root@ubuntu:~#docker run -i -t ubuntu /bin/bash
      Unable to find image 'ubuntu:latest' locally
      latest: Pulling from library/ubuntu
      a48c500ed24e: Pull complete
      1e1de00ff7e1: Pull complete
      0330ca45a200: Pull complete
      471db38bcfbf: Pull complete
      0b4aba487617: Pull complete
      Digest: sha256:c8c275751219dadad8fa56b3ac41ca6cb22219ff117ca98fe82b42f24e1ba64e
      Status: Downloaded newer image for ubuntu:latest
    # root@0ac23d115db8:/# apt-get update
    # root@0ac23d115db8:/# apt-get install ruby-full
    # root@ubuntu:~#docker commit 0ac23d115db8  one/ubuntu-with-ruby
      sha256:eefdc54faeb5bafebd27012520a55b70c6818808997be2986d16b85b6c6f56e2
    # root@ubuntu:~#docker image ls
      REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
      one/ubuntu-with-ruby   latest              eefdc54faeb5        22 seconds ago      79.6MB


Step 5 - Save the Image
---------------------------------------------------------

If you want to save changes like the ones performed in Step 3 and Step 4, the disk saveas functionality can be used to save this image as a new one. This option is available at the storage tab of the VM, this will automatically create a new image with the performed changes.

|disk-saveas|


.. |img-marketplace-kvm| image:: /images/docker/ubuntu1604-docker-kvm-marketplace.png
.. |img-marketplace-vcenter| image:: /images/docker/ubuntu1604-docker-vcenter-marketplace.png
.. |image-persistent| image:: /images/docker/ubuntu-docker-image-persistent.png
.. |disk-saveas| image:: /images/docker/disksaveas-docker.png
.. |vm-running| image:: /images/docker/docker-appliance-running.png
.. |image-download| image:: /images/docker/docker-download.png
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
