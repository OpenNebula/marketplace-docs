.. _service_awsgg:

==========================
Service AWS IoT Greengrass
==========================

OpenNebula `Marketplace Appliance <https://marketplace.opennebula.systems/appliance/13449c22-17c3-46cd-b1bf-7cbec55b504b>`_  with preinstalled `AWS IoT Greengrass <https://aws.amazon.com/greengrass/>`_ SDK and services to run IoT Core, device, or both.

If no parameters are provided, the appliance boots without any Greengrass services running and the user can manage the deployment on his own leveraging all the preinstalled AWS IoT Greengrass SDKs and AWS CLI. The real power is in the :ref:`contextualization <awsgg_context_param>` parameters, which can specify what and how to configure and start in the appliances automatically on first boot.

.. _awsgg_features:

.. include:: shared/features.txt
* Running on CentOS 7 ELRepo LTS kernel.
* Appliance can report IoT *thing* names of Core and IoT devices via OpenNebula `OneGate service <http://docs.opennebula.org/stable/advanced_components/application_insight/>`_ (if applicable).
* Instance Greengrass roles: Core, IoT device, a combination of both
* Provision modes of Greengrass entities: **manual** and **automatic**

.. _awsgg_requirements:

Requirements
============

* Valid AWS account and be able to log into `AWS Console <https://console.aws.amazon.com/>`_
* Known :ref:`IoT Greengrass endpoint <awsgg_endpoint>`
* **Only for Automatic Mode**: have `API credentials <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html>`_

Official Documentation
======================

If you are new to the AWS IoT Greengrass, check the official documentation and tutorials first:

* `What is AWS Greengrass <https://docs.aws.amazon.com/greengrass/latest/developerguide/what-is-gg.html>`_
* `Getting Started with AWS Greengrass <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html>`_
* `AWS Greengrass Core Configuration <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-core.html>`_
* `Supported regions/endpoints for AWS Greengrass <https://docs.aws.amazon.com/general/latest/gr/rande.html#greengrass_region>`_

Platform Notes
==============

Appliance components versions:

============================= ==================
Component                     Version
============================= ==================
AWS Greengrass Core           1.10.2
AWS CLI                       1.18.84
AWS Lambda Runtimes           Python 2.7
Python 2 - greengrasssdk      1.5.0
Python 2 - AWSIoTPythonSDK    1.4.8
Python 2 - boto3              1.14.7
Contextualization package     5.12.0
============================= ==================

Requirements:

* OpenNebula 4.14 - 5.12
* `OneGate <http://docs.opennebula.org/stable/advanced_components/application_insight/index.html>`_ for optional reporting back from the VM
* Min. Memory: 2 GB
* Min. Cores (VCPU): 1

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <awsgg_context_param>` parameters:

|image-context-vars|

.. note::

    The first input **Deployment Role** determines if the appliance creates a Core device (possibly with Group), IoT device or both (bulk) and based on that it will expect certain inputs to be set. If you don't intend to provision the appliance either by configuration files or by providing the API credentials then leave **\*ALL*** inputs empty - otherwise the appliance will fail. In the case of provisioned appliance (most of the cases) jump to :ref:`contextualization <awsgg_context_param>` where are descriptions of valid contexts and modes.

After you are done, click on the button **Instantiate**. A virtual machine with the running service should be ready in a few minutes.

.. include:: shared/report.txt

.. _report_file_example:

.. code::

       ___   _ __    ___
      / _ \ | '_ \  / _ \   OpenNebula Service Appliance
     | (_) || | | ||  __/
      \___/ |_| |_| \___|

    All set and ready to serve 8)

    [root@localhost ~]# cat /etc/one-appliance/config
    [aws credentials]
    user = ggc_user
    group = ggc_group
    config_dir =
    region =
    custom_endpoint =
    aws_access_key_id =
    aws_secret_access_key =

.. note::

   This *Quick Start* example had no contextualization and so no Core, device or Greengrass Group was created or deployed (that is the reason why the report file has barely any info). To make use of such appliance you must visit `the official guide <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html>`_ of the AWS Greengrass and follow the instructions there - you can skip the `module 1 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module1.html>`_ and go directly to `module 2 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module2.html>`_.

.. _awsgg_context_param:

Contextualization
=================

Contextualization parameters provided in the Virtual Machine template controls the initial VM configuration. Except for the `common set <http://docs.opennebula.org/stable/operation/references/template.html#context-section>`_ of parameters supported by every appliance on the OpenNebula Marketplace, there are few specific to the particular service appliance. The parameters should be provided in the ``CONTEXT`` section of the Virtual Machine template, read the OpenNebula `Operation Guide <http://docs.opennebula.org/stable/operation/vm_setup/kvm.html#set-up-the-virtual-machine-template>`__ for more details.

==================================== ============== ========= ============== =========== ===========
Parameter                            Default        Stage     Roles          Modes       Description
==================================== ============== ========= ============== =========== ===========
``ONEAPP_AWSGG_ROLE``                               configure                            Deployment role: ``core``, ``device``, ``bulk``, empty
``ONEAPP_AWSGG_BULK_DEVICES``        ``1``          configure bulk                       Number of devices to be created in **bulk** role
``ONEAPP_AWSGG_SSL_CERT``                           configure core,device    manual      Core/Device certificate
``ONEAPP_AWSGG_SSL_PRIVKEY``                        configure core,device                Core/Device private Key
``ONEAPP_AWSGG_SSL_PUBKEY``                         configure core,device                Core/Device public Key
``ONEAPP_AWSGG_CONFIG``                             configure core                       Greengrass Group config file
``ONEAPP_AWSGG_GROUP``                              configure                automatic   Greengrass Group Name to create
``ONEAPP_AWSGG_DEVICE_NAME``                        configure device,bulk                Device name (postfixed by random for bulk role)
``ONEAPP_AWSGG_DEVICE_HASH``         ``device``     configure device         manual      Device Hash prefix (created by AWS)
``ONEAPP_AWSGG_ENDPOINT``                           configure                            IoT Greengrass Endpoint
``ONEAPP_AWSGG_ACCESS_KEY_ID``                      configure                            AWS Access Key ID
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``                  configure                            AWS Access Secret Key
``ONEAPP_AWSGG_GGC_USER``            ``ggc_user``   configure                            Instance OS user (files/processes owner)
``ONEAPP_AWSGG_GGC_GROUP``           ``ggc_group``  configure                            Instance OS group (files/processes owner)
``ONEAPP_AWSGG_IOTMQTTPORT``         ``8883``       configure core                       MQTT port to IoT (8883 or 443)
``ONEAPP_AWSGG_IOTHTTPPORT``         ``8443``       configure core                       HTTPS port to IoT (8443 or 443)
``ONEAPP_AWSGG_GGHTTPPORT``          ``8443``       configure core                       HTTPS port to GG (8443 or 443)
==================================== ============== ========= ============== =========== ===========

If no suitable contextualization parameters are provided, the virtual machines boot only into the AWS IoT Greengrass compatible system environment and don't configure anything.

Appliance on-boot configuration for the AWS IoT Greengrass via contextualization parameters support 2 main ways of deployment:

**Manual** (pregenerated configuration files):

* Greengrass Group, Core and (optionally) IoT device(s) are already preconfigured in AWS
* Certificates and configuration is exported from AWS
* Appliance gets these certificates and config to put on right path inside
* Supported instance roles:
   * Role **core**: Deploy preconfigured Core device
   * Role **device**: Deploy preconfigured IoT device
   * Role **bulk**: Deploy preconfigured Core device and create multiple new IoT devices at once (bulk role)

**Automatic** (API provisioned):

* Appliance gets AWS API credentials
* Creates all required entities ad-hoc in AWS over API
* Supported intance roles:
   * Role **core**: Create new Greengrass Group and deploy new Core device
   * Role **device**: Deploy new IoT device (in existing Greengrass Group with Core)
   * Role **bulk**: Create new Greengrass Group, deploy Core and one or more IoT devices at once (bulk role)

.. important::

   AWS IoT Greengrass deployment cannot exist without a Greengrass Group with the Core device - these two entities are fundamental and must be always present. You must ensure they are precreated (for manual mode), created by the appliance (in automatic mode), or by other means. Only after that can add one or more IoT devices.

.. important::

   For the manual mode, you don't need to provide AWS credentials to configure AWS CLI. In the case of automatic mode, the AWS credentials are mandatory. If you provide contextualization parameters for both modes, then the **manual mode will take precedence**.

.. _awsgg_manual_mode:

Manual Mode
-----------

In this deployment mode, the certificate files and a config file (in the case of a Core device) are provided by contextualization parameters. API is used only in the bulk role when we create one or more devices on top of the Core deployment.

Deploy Core Device
~~~~~~~~~~~~~~~~~~

Greengrass Group and Core device must be already created (by tool, API, AWS Management Console, etc.) and you should have prepared all the necessary files (certificates and config). These files can be extracted from the archive which should have been downloaded when the Greengrass Group and Core device were created, e.g. steps to follow when `using the AWS Management Console <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html>`__.

Relevant contextualization parameters:

====================================== ===================================
Parameter                              Comment
====================================== ===================================
``ONEAPP_AWSGG_ROLE=core``             Set instance role to **core**
``ONEAPP_AWSGG_CONFIG``                Greengrass Core config file
``ONEAPP_AWSGG_SSL_CERT``              Greengrass Core certificate
``ONEAPP_AWSGG_SSL_PRIVKEY``           Greengrass Core private key
``ONEAPP_AWSGG_SSL_PUBKEY``            Greengrass Core public key
``ONEAPP_AWSGG_ENDPOINT``              (Optional)
``ONEAPP_AWSGG_ACCESS_KEY_ID``         (Optional)
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     (Optional)
``ONEAPP_AWSGG_IOTMQTTPORT``           (Optional)
``ONEAPP_AWSGG_IOTHTTPPORT``           (Optional)
``ONEAPP_AWSGG_GGHTTPPORT``            (Optional)
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== ===================================

If the instance was configured correctly, the Greengrass Core service ``greengrassd`` should be running. Check the status:

.. prompt:: text [remote]$ auto

    $ systemctl status greengrassd.service

All Greengrass related files are in ``/greengrass/`` and the Core device specific files are in

* ``/greengrass/certs/``
* ``/greengrass/config/config.json``

.. _awsgg_device_file:

Deploy IoT Device
~~~~~~~~~~~~~~~~~

Similar to the previous case - IoT device we want to deploy must be already created. You must have prepared also the associated certificates for the device (e.g., downloaded as an archive when creating the device in the `AWS Management Console <https://docs.aws.amazon.com/greengrass/latest/developerguide/device-group.html>`__).

Relevant contextualization parameters:

====================================== ===================================================
Parameter                              Comment
====================================== ===================================================
``ONEAPP_AWSGG_ROLE=device``           Set instance role to **device**
``ONEAPP_AWSGG_SSL_CERT``              Greengrass Device certificate
``ONEAPP_AWSGG_SSL_PRIVKEY``           Greengrass Device private key
``ONEAPP_AWSGG_SSL_PUBKEY``            Greengrass Device public key
``ONEAPP_AWSGG_DEVICE_HASH``           (Optional)
``ONEAPP_AWSGG_ENDPOINT``              (Optional)
``ONEAPP_AWSGG_ACCESS_KEY_ID``         (Optional)
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     (Optional)
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== ===================================================

Device certificates are stored on running instance in ``/greengrass/device/`` directory.

There is no dedicated service/daemon running on the instance with a device role. The appliance only provides the preinstalled SDK(s) and runtimes, and configures the instance with provided device certificates. The deployment and run of the code on a device are up to the user. Check out the `device documentation <https://docs.aws.amazon.com/greengrass/latest/developerguide/module4.html>`_.

Deploy Core and automatically IoT device(s)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Behaves as the manual deployment of the precreated Core device, but also creates one or more IoT devices over AWS API and stores their certificates in ``/greengrass/device/`` directory.

Relevant contextualization parameters:

====================================== ===================================================
Parameter                              Comment
====================================== ===================================================
``ONEAPP_AWSGG_ROLE=bulk``             Set instance role to **bulk**
``ONEAPP_AWSGG_CONFIG``                Greengrass Core config file
``ONEAPP_AWSGG_SSL_CERT``              Greengrass Core certificate
``ONEAPP_AWSGG_SSL_PRIVKEY``           Greengrass Core private key
``ONEAPP_AWSGG_SSL_PUBKEY``            Greengrass Core public key
``ONEAPP_AWSGG_ENDPOINT``              IoT Greengrass Endpoint
``ONEAPP_AWSGG_ACCESS_KEY_ID``         AWS Access Key ID
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     AWS Access Secret Key
``ONEAPP_AWSGG_DEVICE_NAME``           Unique Device Name (postfixed by random)
``ONEAPP_AWSGG_BULK_DEVICES``          Numer of created devices (default=1)
``ONEAPP_AWSGG_IOTMQTTPORT``           (Optional)
``ONEAPP_AWSGG_IOTHTTPPORT``           (Optional)
``ONEAPP_AWSGG_GGHTTPPORT``            (Optional)
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== ===================================================

Provisioned files are stored in these locations:

* ``/greengrass/certs/``
* ``/greengrass/config/config.json``
* ``/greengrass/device/``

.. note::

   Parameters configure AWS CLI are optional when you provision Core or Device in manual mode, but it's mandatory for manual **bulk** role:

   * ``ONEAPP_AWSGG_ENDPOINT``
   * ``ONEAPP_AWSGG_ACCESS_KEY_ID``
   * ``ONEAPP_AWSGG_ACCESS_SECRET_KEY``

Automatic Mode
--------------

In this deployment mode, there is no precreated Greengrass Group, Core, or IoT device(s). The automation inside the appliance ensures the required entities are created over the AWS API on the fly. Appliance must be contextualized with the required role and AWS connection parameters (IoT endpoint in the `supported region <https://docs.aws.amazon.com/general/latest/gr/rande.html#greengrass_region>`_ and `AWS credentials <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html>`_).

.. important::

   New dedicated Greengrass Group is created for each new Core device on the instance boot. Any IoT device instances must be run only after the (automatically deployed) Core device is fully and successfully started. This deployment order must be followed.

Deploy Core Device
~~~~~~~~~~~~~~~~~~

The appliance will create a Greengrass Group and the Core device entities in the designated IoT endpoint over AWS API. If Greengrass Group with the same name already exists, the deployment fails.

Relevant contextualization parameters:

====================================== =======================================================
Parameter                              Comment
====================================== =======================================================
``ONEAPP_AWSGG_ROLE=core``             Set instance role to **core**
``ONEAPP_AWSGG_GROUP``                 Greengrass Group name to create
``ONEAPP_AWSGG_ENDPOINT``              IoT Greengrass Endpoint
``ONEAPP_AWSGG_ACCESS_KEY_ID``         AWS Access Key ID
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     AWS Access Secret Key
``ONEAPP_AWSGG_IOTMQTTPORT``           (Optional)
``ONEAPP_AWSGG_IOTHTTPPORT``           (Optional)
``ONEAPP_AWSGG_GGHTTPPORT``            (Optional)
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== =======================================================

If the instance was configured correctly, the Greengrass Core service ``greengrassd`` should be running. Check the status:

.. prompt:: text [remote]$ auto

    $ systemctl status greengrassd.service

All Greengrass related files are in ``/greengrass/`` and the Core device specific files are in

* ``/greengrass/core/``
* ``/greengrass/certs/``
* ``/greengrass/config/config.json``

Deploy IoT Device
~~~~~~~~~~~~~~~~~

Starts instance as an IoT device into existing Greengrass Group. Device on AWS is created over the API.

Relevant contextualization parameters:

====================================== ======================================================================================
Parameter                              Comment
====================================== ======================================================================================
``ONEAPP_AWSGG_ROLE=device``           Set instance role to **device**
``ONEAPP_AWSGG_GROUP``                 Greengrass Group name deploy device into (must exist)
``ONEAPP_AWSGG_ENDPOINT``              IoT Greengrass Endpoint
``ONEAPP_AWSGG_ACCESS_KEY_ID``         AWS Access Key ID
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     AWS Access Secret Key
``ONEAPP_AWSGG_DEVICE_NAME``           Unique Device Name
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== ======================================================================================

Device certificates are stored on running instance in ``/greengrass/device/`` directory.

There is no dedicated service/daemon running on the instance with device role. The appliance only provides the preinstalled SDK(s) and runtimes, creates IoT device over AWS API and configures the instance with new device certificates. The deployment and run of the code on device are up to the user. Check out the `device documentation <https://docs.aws.amazon.com/greengrass/latest/developerguide/module4.html>`_.

Deploy Core and IoT device(s)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Automated deployment of the all-in-one instance acting as Greengrass Core device and IoT device deployed into the new dedicated Greengrass Group. If Greengrass Group with same name already exists, the deployment fails.

Relevant contextualization parameters:

=================================== ==========================================================================================================
Parameter                              Comment
=================================== ==========================================================================================================
``ONEAPP_AWSGG_ROLE=bulk``          Set instance role to **bulk**
``ONEAPP_AWSGG_GROUP``              Greengrass Group name to create
``ONEAPP_AWSGG_ENDPOINT``           IoT Greengrass Endpoint
``ONEAPP_AWSGG_ACCESS_KEY_ID``      AWS Access Key ID
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``  AWS Access Secret Key
``ONEAPP_AWSGG_DEVICE_NAME``        Device Name (postfixed by random to avoid collision)
``ONEAPP_AWSGG_BULK_DEVICES``       Number of created device (default=1)
``ONEAPP_AWSGG_IOTMQTTPORT``        (Optional)
``ONEAPP_AWSGG_IOTHTTPPORT``        (Optional)
``ONEAPP_AWSGG_GGHTTPPORT``         (Optional)
``ONEAPP_AWSGG_GGC_USER``           (Optional)
``ONEAPP_AWSGG_GGC_GROUP``          (Optional)
=================================== ==========================================================================================================

If the instance was configured correctly, the Greengrass Core service ``greengrassd`` should be running.

Provisioned files are created and stored in these locations:

* ``/greengrass/core/``
* ``/greengrass/certs/``
* ``/greengrass/config/config.json``
* ``/greengrass/device/``

.. important::

   When using automatic deployment mode, the AWS API credentials must be provided for all roles:

   - ``ONEAPP_AWSGG_ENDPOINT``
   - ``ONEAPP_AWSGG_ACCESS_KEY_ID``
   - ``ONEAPP_AWSGG_ACCESS_SECRET_KEY``

.. _awsgg_endpoint:

IoT Endpoint
------------

For the deployment modes and roles which require known custom IoT Greengrass Endpoint, you can find that in the **Settings** section of your AWS IoT Management Console. For example:

|image-console-endpoint|

Examples
========

In this section, we demonstrate the practical use of the appliance. Please ensure to meet the :ref:`requirements <awsgg_requirements>` before you start.

We follow the official AWS Greengrass `Getting Started <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html>`_ tutorial.

Deploy Core in Manual Mode
--------------------------

A. Prepare AWS
~~~~~~~~~~~~~~

1. Log into `AWS Management Console <https://console.aws.amazon.com/>`_
2. Follow `steps 2-9 <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html>`_ to create new Greengrass group **MyFirstGroup**.

  - Skip step 10
  - IMPORTANT step 8: Download the security resources by clicking on **"Download these resources as a tar.gz"** and archive for future use.
  - When the group is ready, you get a similar page:

|image-console-create-group-2|

.. _awsgg_create_lambda_tutorial:

3. Create AWS Lambda function by following `all steps <https://docs.aws.amazon.com/greengrass/latest/developerguide/create-lambda.html>`__.
4. Configure AWS Lambda for Greengrass by following `all steps <https://docs.aws.amazon.com/greengrass/latest/developerguide/config-lambda.html>`__.

.. note::

   To get back to Greengrass click on **Services → AWS IoT → Greengrass → Groups → MyFirstGroup**.

B. Run Core Instance
~~~~~~~~~~~~~~~~~~~~

To be able to run the AWS IoT Greengrass appliance, we need to prepare the contextualization data.

Get the security resources tar.gz archive from the previous section.

1. Unpack the content and find files matching

  - ``config.json``
  - ``*.cert.pem`` - certificate
  - ``*.private.key`` - private key file
  - ``*.public.key`` - public key file

2. Copy and paste the content of those files into appropriate contextualization parameters. For example:

|image-context-core-files|

3. Run the instance.

After a while, the instance should be ready to be used as Core device. Log in the virtual machine and check the status and configuration. For example:

.. code::

       ___   _ __    ___
      / _ \ | '_ \  / _ \   OpenNebula Service Appliance
     | (_) || | | ||  __/
      \___/ |_| |_| \___|

    All set and ready to serve 8)

   [root@localhost ~]# cat /etc/one-appliance/config
   [aws credentials]
   user = ggc_user
   group = ggc_group
   config_dir =
   region = eu-central-1
   custom_endpoint = XXXXXXXXXXXXXX-ats.iot.eu-central-1.amazonaws.com
   aws_access_key_id =
   aws_secret_access_key =

   [greengrass core]
   core_config = //greengrass/config/config.json
   core_thing_name =
   core_thing_arn =
   group_name =
   group_id =
   group_arn =
   group_latest_version =
   group_latest_version_arn =
   iot_mqtt_port = 8883
   iot_http_port = 8443
   gg_http_port = 8443


.. _awsgg_deploy_test_lambda_tutorial:

C. Deploy Lambda Function
~~~~~~~~~~~~~~~~~~~~~~~~~

The Core device is now running and ready to deploy the AWS Lambda function on it.

1. Trigger deployment on Greengrass group by following `all steps <https://docs.aws.amazon.com/greengrass/latest/developerguide/configs-core.html>`__.

You should see **Successfully completed** deployment:

|image-console-deploy|

2. Check the Lambda function is working on your Core device by following `all steps <https://docs.aws.amazon.com/greengrass/latest/developerguide/lambda-check.html>`__.

You should get a similar output:

|image-console-test|

Deploy Core in Automatic Mode
-----------------------------

We'll reproduce the above example of deployment of the Core device by leveraging the automation provided by the OpenNebula AWS IoT Greengrass appliance.

A. Prepare AWS
~~~~~~~~~~~~~~

1. Log into `AWS Management Console <https://console.aws.amazon.com/>`__
2. Create AWS Lambda function by following `all steps <https://docs.aws.amazon.com/greengrass/latest/developerguide/create-lambda.html>`__.
3. Configure AWS Lambda for Greengrass by following `all steps <https://docs.aws.amazon.com/greengrass/latest/developerguide/config-lambda.html>`__.

B. Run Core Instance
~~~~~~~~~~~~~~~~~~~~

1. Provide contextualization parameters for the automated Core deployment. For example:

|image-context-core-api|

2. Run the instance.

After a while, the instance should be ready to be used as Core device without the need to precreate the Greengrass group or Core device. Log in the virtual machine and check the status and configuration. For example:

.. code::

       ___   _ __    ___
      / _ \ | '_ \  / _ \   OpenNebula Service Appliance
     | (_) || | | ||  __/
      \___/ |_| |_| \___|

    All set and ready to serve 8)

   [root@localhost ~]# cat /etc/one-appliance/config
   [aws credentials]
   user = ggc_user
   group = ggc_group
   config_dir = /home/ggc_user/.aws
   region = eu-central-1
   custom_endpoint = XXXXXXXXXXXXXX-ats.iot.eu-central-1.amazonaws.com
   aws_access_key_id = XXXXXXXXXXXXXXXXXXXX
   aws_secret_access_key = XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

   [greengrass core]
   core_config = //greengrass/config/config.json
   core_thing_name = MySecondGroup_Core
   core_thing_arn = arn:aws:iot:eu-central-1:999999999999:thing/MySecondGroup_Core
   group_name = MySecondGroup
   group_id = 187894ad-d700-4c03-8205-7698ea541410
   group_arn = arn:aws:greengrass:eu-central-1:999999999999:/greengrass/groups/187894ad-d700-4c03-8205-7698ea541410
   group_latest_version = 0e4380ed-e210-4309-9249-b1a81c49c2a2
   group_latest_version_arn = arn:aws:greengrass:eu-central-1:999999999999:/greengrass/groups/187894ad-d700-4c03-8205-7698ea541410/versions/0e4380ed-e210-4309-9249-b1a81c49c2a2
   iot_mqtt_port = 8883
   iot_http_port = 8443
   gg_http_port = 8443
   one_core_status = //greengrass/core/one-aws-gg-MySecondGroup-core-status-73d3c7991d.json

C. Deploy Lambda Function
~~~~~~~~~~~~~~~~~~~~~~~~~

Follow the same procedure as before in :ref:`Deploy Lambda Function <awsgg_deploy_test_lambda_tutorial>`.

.. note::

   The OpenNebula AWS IoT Greengrass appliance with automated deployment managed to cut some steps - the creation of Greengrass Group and Core device on AWS is fully automated and easy to use. Deployment of custom code in your new Greengrass platform is out of the scope of the appliance automation.

Deploy Device in Automatic Mode
-------------------------------

In the following section we'll automatically deploy 2 IoT devices into existing Greengrass Group with running Core device. The steps described below follow the official `tutorial <https://docs.aws.amazon.com/greengrass/latest/developerguide/module4.html>`_. In case of problems, consult the commands with the referenced tutorial.

.. important::

   We expect that you have the Greengrass Group **MyFirstGroup** and Core device created and deployed as instructed above.

A. Run Device Instances
~~~~~~~~~~~~~~~~~~~~~~~

1. Run 1st IoT device instance ``HelloWorld_Publisher``, provide relevant context parameters. For example:

|image-context-device-publisher-api|

Log in the virtual machine and check the deployment status.

2. Run 2nd IoT device instance ``HelloWorld_Subscriber``, provide relevant context parameters. For example:

|image-context-device-subscriber-api|

Log in the virtual machine and check the deployment status.

B. Prepare AWS
~~~~~~~~~~~~~~

1. Log into `AWS Management Console <https://console.aws.amazon.com/>`__
2. Check the new automatically deployed devices are created in the group. For example:

|image-console-device|

3. Create messaging subscription between devices and trigger again group deployment by following `all steps <https://docs.aws.amazon.com/greengrass/latest/developerguide/config-subs.html>`__.

C. Test Code
~~~~~~~~~~~~

We are going to deploy a test code inside both devices. The test code generates messages on one device and demonstrates the working deployment by receiving the same message on the second device.

1. On both IoT device instances, run the following commands:

.. prompt:: text [remote]$ auto

    $ git clone https://github.com/aws/aws-iot-device-sdk-python.git
    $ cp aws-iot-device-sdk-python/samples/greengrass/basicDiscovery.py /greengrass/device/

2. On ``HelloWorld_Publisher`` device, run following command

.. important::

   Change the ``--endpoint`` with your custom IoT endpoint, and
   certificate paths in ``--cert``/``--key`` with suitable
   filenames available on instance.

.. prompt:: text [remote]$ auto

    $ python /greengrass/device/basicDiscovery.py \
    --endpoint XXXXXXXXXXXXXX-ats.iot.eu-central-1.amazonaws.com \
    --rootCA /greengrass/certs/root.ca.pem \
    --cert /greengrass/device/certs/b0c6c9c1fa.cert.pem \
    --key /greengrass/device/certs/b0c6c9c1fa.private.key \
    --thingName HelloWorld_Publisher \
    --topic 'hello/world/pubsub' \
    --mode publish \
    --message 'Hello, World! Sent from HelloWorld_Publisher'

3. On ``HelloWorld_Subscreber`` device, run following command:

.. important::

   Change the ``--endpoint`` with your custom IoT endpoint, and
   certificate paths in ``--cert``/``--key`` with suitable
   filenames available on instance.

.. prompt:: text [remote]$ auto

    $ python /greengrass/device/basicDiscovery.py \
    --endpoint XXXXXXXXXXXXXX-ats.iot.eu-central-1.amazonaws.com \
    --rootCA /greengrass/certs/root.ca.pem \
    --cert /greengrass/device/certs/33ff050ab0.cert.pem \
    --key /greengrass/device/certs/33ff050ab0.private.key \
    --thingName HelloWorld_Subscriber \
    --topic 'hello/world/pubsub' \
    --mode subscribe

4. Your receiver now should get periodically receive and dump new message.

Deploy Bulk in Automatic Mode
-----------------------------

The last example is dedicated to the deployment of **bulk** role in the automatic mode. A new dedicated Greengrass Group, Core device and a set of IoT devices will be deployed on a single dedicated instance.

Provide relevant contextualization parameters, for example:

|image-context-bulk-api|

and, start the instance. After a while, log in the virtual machine and check the deployment status.

.. |image-download| image:: /images/aws-greengrass/awsgg-download.png
.. |image-context-vars| image:: /images/aws-greengrass/awsgg-context-vars.png
.. |image-context-bulk-api| image:: /images/aws-greengrass/awsgg-bulk-api-context-values.png
.. |image-context-core-api| image:: /images/aws-greengrass/awsgg-core-api-context-values.png
.. |image-context-device-api| image:: /images/aws-greengrass/awsgg-device-api-context-values.png
.. |image-context-device-publisher-api| image:: /images/aws-greengrass/awsgg-device-publisher-api-context-values.png
.. |image-context-device-subscriber-api| image:: /images/aws-greengrass/awsgg-device-subscriber-api-context-values.png
.. |image-context-core-files| image:: /images/aws-greengrass/awsgg-core-files-context-values.png
.. |image-console-create-group-1| image:: /images/aws-greengrass/awsgg-console-create-group-1.png
.. |image-console-create-group-2| image:: /images/aws-greengrass/awsgg-console-create-group-2.png
.. |image-console-endpoint| image:: /images/aws-greengrass/awsgg-console-endpoint.png
.. |image-console-deploy| image:: /images/aws-greengrass/awsgg-console-deploy.png
.. |image-console-test| image:: /images/aws-greengrass/awsgg-console-test.png
.. |image-console-device| image:: /images/aws-greengrass/awsgg-console-device.png
.. |image-console-lambda-1| image:: /images/aws-greengrass/awsgg-console-lambda-1.png
.. |image-console-lambda-2| image:: /images/aws-greengrass/awsgg-console-lambda-2.png
.. |image-console-lambda-3| image:: /images/aws-greengrass/awsgg-console-lambda-3.png
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
