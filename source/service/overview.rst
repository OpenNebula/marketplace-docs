.. _service_overview:

========
Overview
========

.. _one_service_appliance:

The service appliances extend the OpenNebula `Marketplace <https://marketplace.opennebula.systems/>`_ with easy-to-use deployable services. These are the images with all necessary packages installed for the service run, but they go through the final configuration on the very first run on the users' side on. It allows to modify the required service state by the user through new appliance specific contextualization parameters.

Thanks to the dynamic nature of the service appliances, no security credentials are persisted in the distributed images. Initial passwords are provided via the contextualization parameters or are dynamically generated for each new virtual machine. No two virtual machines with default contextualization parameters share same passwords, database credentials etc.

Each of service appliance comes with our script which does the heavy lifting of bringing the service up and running.

.. _one_service_script:

Life Cycle
----------

Every appliance goes through following stages:

1. :ref:`install <one_service_stage_install>` (build time)
2. :ref:`configure <one_service_stage_configure>` (instantiaton time)
3. :ref:`bootstrap <one_service_stage_bootstrap>` (instantiaton time)

Each stage is handled by the shell script ``/etc/one-appliance/service`` installed in the appliance. For the **install** stage, it's triggered externally. For the rest stages, it's triggered as part of the regular OS contextualization. The selected stage is an argument of this script.

To find more about this service script and about the important contextualization variables (per appliance), run with argument ``help``:

.. prompt:: text $ auto

    $ /etc/one-appliance/service help

.. _one_service_stage_install:

Install
~~~~~~~

This stage is already done on the service applinaces downloaded from the OpenNebula Marketplace. It runs during the image build.

It's responsible for configuration of the package repositories, installation of all required packages, and download anything the service would need for the proper start. Service isn't started nor configured, it's left on the next stages which run on the very first instantion in the users' environment.

.. _one_service_stage_configure:

Configure
~~~~~~~~~

This is the crucial stage and one which will be run on every instantiation. Also for this step to be successful the user can provide contextualization parameters which will drive the configuration process in a direction the user wants.

Some appliances will not require any variables to be set (all variables will have working default values), some appliances may require a few variables which would be mandatory.

At the end of this stage the service is actually provisioned and running - but of course only if all mandatory contextualization variables have been provided and all used contextualization variables have been set with valid values.

.. _one_service_stage_bootstrap:

Bootstrap
~~~~~~~~~

We call the last stage bootstrap and it kind of overlaps with the previous configuration stage. Similarly to the configure stage here are also recognized some contextualization parameters. We intended this stage as an opportunity to streamline the appliance start up to a full service by bootstrapping some necessary manual steps. To understand better what we mean here, imagine the service where upon your first use you are forced to create some user first. The bootstrap stage can do this for you if you provide relevant contextualization variables.

Another responsibility of this stage is to report ``READY=yes`` to the OneGate, but only when the service is ready and properly working. The time needed to do so depends on the type of an appliance. Some appliances can configure and bootstrap very quickly, but others can take more time - usually in a matter of minutes (this also will depend on a power of your virtual machines).

.. _one_service_logs:

Reports and Logs
----------------

After the successful run of :ref:`configure <one_service_stage_configure>` and  :ref:`bootstrap <one_service_stage_bootstrap>` stages, you can find service related information (credentials, connection settings) in the file:

.. code::

    /etc/one-appliance/config

If any of the stages fail, there are debug logs for each stage you can troubleshoot the problem:

- ``/var/log/one-appliance/ONE_install.log``
- ``/var/log/one-appliance/ONE_configure.log``
- ``/var/log/one-appliance/ONE_bootstrap.log``
