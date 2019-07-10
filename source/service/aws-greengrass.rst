.. _service_awsgg:

======================
Service AWS Greengrass
======================

OpenNebula `Marketplace Appliance (FIX LINK) <https://marketplace.opennebula.systems/appliance/placeholder>`_  with preinstalled `AWS Greengrass <https://docs.aws.amazon.com/greengrass/latest/developerguide/what-is-gg.html>`_ service.

If no parameters are provided then the appliance will not provision any Greengrass Group, Core or any device, but it will provide the user with a ready environment. User then can proceed with `the official guide <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html>`_ by skipping the `first module <https://docs.aws.amazon.com/greengrass/latest/developerguide/module2.html>`_ and start from the `module two <https://docs.aws.amazon.com/greengrass/latest/developerguide/module2.html>`_.

Though the real power and benefits of this appliance manifest when user *does* provide the :ref:`contextualization <awsgg_context_param>` parameters. Thanks to them, the appliance supports multiple ways of deployment (including the full automation).

.. _awsgg_features:

.. include:: shared/features.txt
* Running on CentOS 7 ELRepo LTS kernel.
* Appliance can store *thing* names of Core devices and IoT devices in OpenNebula `OneFlow service <http://docs.opennebula.org/stable/advanced_components/application_flow_and_auto-scaling/appflow_use_cli.html>`_ (if applicable).
* Multiple ways of deployment - **semi-manual** and **automatic** for Core, device and combination of both.

Requirements
============

* You must have a valid AWS account - to be able to log into `AWS Console <https://console.aws.amazon.com/>`_
* In the case of the automatic mode, you also must create `API credentials <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html>`_

Helpful links to AWS Greengrass documentation
=============================================

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
AWS Greengrass Core           1.9.1
python2                       2.7
greengrasssdk (python 2)      1.4.0
AWSIoTPythonSDK (python 2)    1.4.7
boto3 (python 2)              1.9.183
awscli                        1.16.193
Contextualization package     5.8.0
============================= ==================

Requirements:

* OpenNebula 4.14 - 5.8
* Min. Memory: 2 GB
* Min. Cores (VCPU): 1

.. note::

   AWS Greengrass cannot run on the default distribution kernel - therefore we are installing the ELRepo kernel (LTS).

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <awsgg_context_param>` parameters:

|image-context-vars|

.. note::

    The first input **Deployment role** determines if the appliance creates a Core device (possibly with Group), IoT device or both (bulk) and based on that it will expect certain inputs to be set. If you don't intend to provision the appliance either by configuration files or by providing the API credentials then leave **\*ALL*** inputs empty - otherwise the appliance will fail. In the case of provisioned appliance (most of the cases) jump to :ref:`contextualization <awsgg_context_param>` where are descriptions of valid contexts and modes.

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

===================================== ========= ==================== ========= ================ ===========
Parameter                             Mandatory Default              Stage     Role             Description
===================================== ========= ==================== ========= ================ ===========
``ONEAPP_AWSGG_ROLE``                           none/empty           configure all              Deployment role (core/device/bulk)
``ONEAPP_AWSGG_BULK_DEVICES``                   ``1``                configure bulk             Number of devices created with the bulk role
``ONEAPP_AWSGG_SSL_CERT``                                            configure core/device      Core/Device certificate
``ONEAPP_AWSGG_SSL_PRIVKEY``                                         configure core/device      Core/Device private key
``ONEAPP_AWSGG_SSL_PUBKEY``                                          configure core/device      Core/Device public key
``ONEAPP_AWSGG_CONFIG``                                              configure core             Greengrass Group config file
``ONEAPP_AWSGG_GROUP``                                               configure all (automatic)  Greengrass Group name (only in **automatic** mode)
``ONEAPP_AWSGG_DEVICE_NAME``                                         configure device/bulk      Device name for device/bulk role (auto-postfixed for bulk role)
``ONEAPP_AWSGG_DEVICE_HASH``                    ``device``           configure device (manual)  Hash prefix (created by AWS, only in **semi-manual** mode)
``ONEAPP_AWSGG_ENDPOINT``                                            configure all              API endpoint (it can be located in the AWS Console)
``ONEAPP_AWSGG_ACCESS_KEY_ID``                                       configure all              API access key id (AWS Console)
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``                                   configure all              API secret access key (AWS Console)
``ONEAPP_AWSGG_GGC_USER``                       ``ggc_user``         configure                  Local user name to own process/files
``ONEAPP_AWSGG_GGC_GROUP``                      ``ggc_group``        configure                  Local user group to own process/files
``ONEAPP_AWSGG_IOTMQTTPORT``                    ``8883``             configure core             MQTT port to IoT (8883 or 443)
``ONEAPP_AWSGG_IOTHTTPPORT``                    ``8443``             configure core             HTTPS port to IoT (8443 or 443)
``ONEAPP_AWSGG_GGHTTPPORT``                     ``8443``             configure core             HTTPS port to GG (8443 or 443)
===================================== ========= ==================== ========= ================ ===========

If no contextualization parameter is provided, the appliance will not deploy/create anything and it will only provide an AWS Greengrass compatible system environment (due to preinstalled software and compliant kernel).

If you *do* provide contextualization parameters then the appliance supports multiple ways of deployment:

* **Semi-manual setup** (via configuration files)
   * Deploy already created Core device or IoT device (in already created Greengrass Group)
   * Deploy Core device (like above) but also create multiple new IoT devices at once (bulk role)
* **Automatic setup** (no files provided)
   * Create new Greengrass Group and Core device
   * Create IoT device (in already created Greengrass Group)
   * Create Greengrass Group, Core and one or more IoT devices all at once (bulk role)

.. important::

   AWS Greengrass cannot function without a Core device and no Greengrass Group can operate without its Core - these two elements are fundamental to the concept and must be always present. It means that you should first create (through our appliance or by other means) Greengrass Group and Core device. Only then you can add one or more devices.

.. important::

   For the semi-manual way you don't need to setup aws CLI, but in the case of the full automation, the credential parameters are mandatory. Also, if you provide contextualization parameters for both methods then **THE SEMI-MANUAL METHOD WILL TAKE PRECEDENCE**.

.. _awsgg_manual_mode:

Semi-manual method (with configuration files)
---------------------------------------------

In this type of deployment, you provide certficate files and a config file (in the case of a Core device). API is used only in bulk role when we create one or more devices on top of the Core deployment.

Deploy Core device (role: core)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Greengrass Group and Core device must be already created (by tool, API, AWS Console etc.) and you should have ready all the necessary files (certs and config). These files can be extracted from the archive which should have been downloaded when `the Group and Core device was created in AWS Console <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html>`_. Of course both the Group and Core could be created in another way (via API for example) so it is your responsibility to have these files somewhere safely stored.

====================================== ===================================
Contextualization parameter            Comment
====================================== ===================================
``ONEAPP_AWSGG_ROLE=core``             **Role "core"**
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

If everything was correct then ``greengrassd`` daemon should start. You can check its status with:

.. prompt:: text [remote]$ auto

    $ systemctl status greengrassd

All Greengrass related files are in ``/greengrass/`` and the Core device specific are:

   * ``/greengrass/certs/``
   * ``/greengrass/config/config.json``

.. _awsgg_device_file:

Deploy IoT device (role: device)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Similar to the previous case - Greengrass Group must exist and so does the IoT device which we want to deploy. You must also have ready the associated certs for this device (for example downloaded as an archive during `the device creation in AWS Console <https://docs.aws.amazon.com/greengrass/latest/developerguide/device-group.html>`_).

====================================== ===================================================
Contextualization parameter            Comment
====================================== ===================================================
``ONEAPP_AWSGG_ROLE=device``           **Role "device"**
``ONEAPP_AWSGG_SSL_CERT``              Greengrass device certificate
``ONEAPP_AWSGG_SSL_PRIVKEY``           Greengrass device private key
``ONEAPP_AWSGG_SSL_PUBKEY``            Greengrass device public key
``ONEAPP_AWSGG_DEVICE_HASH``           (Optional) AWS hash prefix (defaults to 'device')
``ONEAPP_AWSGG_ENDPOINT``              (Optional)
``ONEAPP_AWSGG_ACCESS_KEY_ID``         (Optional)
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     (Optional)
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== ===================================================

There is no service/daemon to be started for the device - basically the appliance will only store device's certs and it is up to you to create some publisher/subscriber logic with provided SDK(s). Check out `the device documentation <https://docs.aws.amazon.com/greengrass/latest/developerguide/module4.html>`_ to understand this better.

Device certs are stored in: ``/greengrass/device/``

Deploy Core device and create IoT device(s) (role: bulk)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This behaves identically to the deployment of a Core device but it also creates one or more devices (via AWS API) and stores their certs in: ``/greengrass/device/``.

====================================== ========================================================================================================
Contextualization parameter            Comment
====================================== ========================================================================================================
``ONEAPP_AWSGG_ROLE=bulk``             **Role "bulk"**
``ONEAPP_AWSGG_CONFIG``                Greengrass Core config file
``ONEAPP_AWSGG_SSL_CERT``              Greengrass Core certificate
``ONEAPP_AWSGG_SSL_PRIVKEY``           Greengrass Core private key
``ONEAPP_AWSGG_SSL_PUBKEY``            Greengrass Core public key
``ONEAPP_AWSGG_ENDPOINT``              The API endpoint - it can be found in the AWS Console
``ONEAPP_AWSGG_ACCESS_KEY_ID``         AWS account API credential key id
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     AWS account API credential secret
``ONEAPP_AWSGG_DEVICE_NAME``           Name prefix for created bulk of devices (they will be appended with random string to avoid collisions)
``ONEAPP_AWSGG_BULK_DEVICES``          Count of created devices (default 1)
``ONEAPP_AWSGG_IOTMQTTPORT``           (Optional)
``ONEAPP_AWSGG_IOTHTTPPORT``           (Optional)
``ONEAPP_AWSGG_GGHTTPPORT``            (Optional)
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== ========================================================================================================

Provisioned files are stored in these locations:

   - ``/greengrass/certs/``
   - ``/greengrass/config/config.json``
   - ``/greengrass/device/``

.. important::

   Context to configure aws CLI is optional when you provision Core and device via files but it is **mandatory** for **bulk** role:

  - ``ONEAPP_AWSGG_ENDPOINT``
  - ``ONEAPP_AWSGG_ACCESS_KEY_ID``
  - ``ONEAPP_AWSGG_ACCESS_SECRET_KEY``

Automatic method (API provisioned)
----------------------------------

In this fully automated deployment, you don't need to create Greengrass Group, Core or IoT device before you can deploy the appliance (but the Group must be created **BEFORE** the **device** deployment/creation). You must have a valid `AWS API credentials <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html>`_ - including a correct endpoint with `a proper supported region <https://docs.aws.amazon.com/general/latest/gr/rande.html#greengrass_region>`_. The appliance will create all needed artifacts in the cloud and it will provision itself.

Create Core device (role: core)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

With this set of contextualization parameters the appliance will connect to the AWS API and it will create a Greengrass Group and its Core device in the designated AWS region (defined by your endpoint). If a Greengrass Group of such name already exists then the deployment will abort and fail.

====================================== =======================================================
Contextualization parameter            Comment
====================================== =======================================================
``ONEAPP_AWSGG_ROLE=core``             **Role "core"**
``ONEAPP_AWSGG_GROUP``                 Name of the Greengrass group you want to create
``ONEAPP_AWSGG_ENDPOINT``              The API endpoint - it can be found in the AWS Console
``ONEAPP_AWSGG_ACCESS_KEY_ID``         AWS account API credential key id
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     AWS account API credential secret
``ONEAPP_AWSGG_IOTMQTTPORT``           (Optional)
``ONEAPP_AWSGG_IOTHTTPPORT``           (Optional)
``ONEAPP_AWSGG_GGHTTPPORT``            (Optional)
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== =======================================================

If everything was correct then ``greengrassd`` daemon should start (as was the case with a file provisioning). You can check its status with:

.. prompt:: text [remote]$ auto

    $ systemctl status greengrassd

All Greengrass related files are in ``/greengrass/`` and the Core device specific are:

   - ``/greengrass/core/``
   - ``/greengrass/certs/``
   - ``/greengrass/config/config.json``

Create IoT device (role: device)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Greengrass Group has to be created beforehand, but the device itself is created by the appliance.

====================================== ======================================================================================
Contextualization parameter            Comment
====================================== ======================================================================================
``ONEAPP_AWSGG_ROLE=device``           **Role "device"**
``ONEAPP_AWSGG_GROUP``                 Name of the Greengrass Group to which the new device assign (it must exist already)
``ONEAPP_AWSGG_ENDPOINT``              The API endpoint - it can be found in the AWS Console
``ONEAPP_AWSGG_ACCESS_KEY_ID``         AWS account API credential key id
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     AWS account API credential secret
``ONEAPP_AWSGG_DEVICE_NAME``           Name of the device you want to create (must be unique across all IoT things)
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== ======================================================================================

The result is practically the same as with :ref:`the file provisioning <awsgg_device_file>`:

   - There is no service/daemon to be started - check out `the device documentation <https://docs.aws.amazon.com/greengrass/latest/developerguide/module4.html>`_.
   - Device certs are stored in: ``/greengrass/device/``.

Create Core device and IoT device(s) (role: bulk)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Simply put: this appliance will create Greengrass Group, Core device and one or more IoT devices - if there will be a Group of the same name or there will be any collision amongs the *thing* names then the appliance will abort and fail.

=================================== ==========================================================================================================
Contextualization parameter            Comment
=================================== ==========================================================================================================
``ONEAPP_AWSGG_ROLE=bulk``          **Role "bulk"**
``ONEAPP_AWSGG_GROUP``              Name of the Greengrass group you want to create
``ONEAPP_AWSGG_ENDPOINT``           The API endpoint - it can be found in the AWS Console
``ONEAPP_AWSGG_ACCESS_KEY_ID``      AWS account API credential key id
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``  AWS account API credential secret
``ONEAPP_AWSGG_DEVICE_NAME``        Name prefix for created bulk of devices (they will be appended with random string to avoid collisions)
``ONEAPP_AWSGG_BULK_DEVICES``       Count of created devices (default 1)
``ONEAPP_AWSGG_IOTMQTTPORT``        (Optional)
``ONEAPP_AWSGG_IOTHTTPPORT``        (Optional)
``ONEAPP_AWSGG_GGHTTPPORT``         (Optional)
``ONEAPP_AWSGG_GGC_USER``           (Optional)
``ONEAPP_AWSGG_GGC_GROUP``          (Optional)
=================================== ==========================================================================================================

Successful run results in running ``greengrassd`` service and with device certs stored in ``/greengrass/device/``.

Provisioned files are created and stored in these locations:

   - ``/greengrass/core/``
   - ``/greengrass/certs/``
   - ``/greengrass/config/config.json``
   - ``/greengrass/device/``

.. important::

   Using the automatic mode you **MUST** provide the credentials for aws CLI (for all roles: core, device and bulk):

  - ``ONEAPP_AWSGG_ENDPOINT``
  - ``ONEAPP_AWSGG_ACCESS_KEY_ID``
  - ``ONEAPP_AWSGG_ACCESS_SECRET_KEY``

Examples
========

In this section, we demonstrate the usage of this appliance.

.. important::

   You cannot continue and deploy our appliance if you don't have any AWS account.

Official AWS Greengrass tutorial
--------------------------------

AWS provides a nice `Getting Started tutorial <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html>`_ how to start with Greengrass. We actually start from `module 2 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module2.html>`_ but for your own benefit you should read it from beginning.

1. Create Greengrass Group and Core device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Log into `AWS Console <https://console.aws.amazon.com/>`_

2. Start with `the module 2 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module2.html>`_ of AWS Greengrass tutorial and follow `the instructions <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html>`_ which help you to create **MyFirstGroup**.

You should reach this screen (files/hashes will differ):

|image-console-create-group-1|

3. **BEFORE** you hit the finish button you **MUST** click on **"Download these resources as a tar.gz"** and store this archive somewhere.
4. Now you can press **Finish** button and you should see the same screen as is in the tutorial:

|image-console-create-group-2|

5. Ignore the step **10.** of the instructions and jump to `the next module 3 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module3-I.html>`_ - to create Lambda function.

.. _awsgg_create_lambda_tutorial:

2. Create Lambda function
~~~~~~~~~~~~~~~~~~~~~~~~~

1. Create Lambda as described in the tutorial - `create Lambda <https://docs.aws.amazon.com/greengrass/latest/developerguide/create-lambda.html>`_.
2. Continue on the next page and do all steps there - `config Lambda <https://docs.aws.amazon.com/greengrass/latest/developerguide/config-lambda.html>`_.

.. note::

   To get back to Greengrass UI: Services -> AWS IoT -> Greengrass -> Groups -> MyFirstGroup

3. OpenNebula AWS Greengrass appliance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For the next part we must first prepare our appliance (finally). The time has come for that tar archive which you downloaded few moments ago.

1. Unpack the content of tar archive and locate: **config.json**, **certificate**, **private key** and **public key**
2. Content of these files put into the right contextualization parameters, like so:

|image-context-core-files|

After a minute or two the appliance should be ready:

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

4. Deploy and test
~~~~~~~~~~~~~~~~~~

1. Return to AWS Console and deploy your lambda to the Core device as is shown in the tutorial - `configs Core <https://docs.aws.amazon.com/greengrass/latest/developerguide/configs-core.html>`_.

You should see **Successfully completed**:

|image-console-deploy|

2. The last step is to check that lambda is really running on your Core. Execute `these steps (Lambda check) <https://docs.aws.amazon.com/greengrass/latest/developerguide/lambda-check.html>`_ to verify your setup.

Your output should resemble the following:

|image-console-test|

Official tutorial revisited - OpenNebula style
----------------------------------------------

The previous example was rather tedious - a lot of mouse clicking and writing. Many manual and error-prone steps.

We can do it better - introducing our killer-feature (or we think) of the ONE service apliance: **Automated Greengrass Group, Core and Device creation and deployment**

1. Create Greengrass Group and Core device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Create your appliance with this context for automated setup:

|image-context-core-api|

2. When the appliance is finished bootstrapping, you can check it out:

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

.. note::

   At this point you already have Greengrass Group and Core device up and running. Now you can fastforward to `the module 3 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module3-I.html>`_ No more jumping from AWS Console to VM and back.

2. Create Lambda function
~~~~~~~~~~~~~~~~~~~~~~~~~

This is the same as before with: :ref:`Create Lambda function <awsgg_create_lambda_tutorial>`

3. Deploy and test
~~~~~~~~~~~~~~~~~~

Again the same procedure as before: :ref:`Deploy and Test <awsgg_deploy_test_lambda_tutorial>`

.. note::

   The ONE service appliance with automated deployment managed to cut the steps and relieved us of managing some tar archives. The creation of Greengrass Group and Core device is fully automated and made easy. **The Lambda functions are too use-case specific to be reasonably automated and provisioned with the appliance's bootstrap.**

Official AWS Greengrass tutorial - Devices
------------------------------------------

We will continue with the official tutorial and we will proceed to `the module 4 (Devices) <https://docs.aws.amazon.com/greengrass/latest/developerguide/module4.html>`_.

.. important::

   We expect that you have the Greengrass Group ``MyFirstGroup`` and Core device created and deployed as instructed above.

1. Create and deploy devices
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::

   To leverage the automation and to skip some more tedious manual steps - we will not follow the first set of `instructions <https://docs.aws.amazon.com/greengrass/latest/developerguide/device-group.html>`_.

1. Create the first device ``HelloWorld_Publisher`` with this context:

|image-context-device-publisher-api|

Result:

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

   [greengrass device]
   device_dir = //greengrass/device
   device_name = HelloWorld_Publisher
   device_id = 9887956e-a849-4c14-9c1f-d099e5cfb325
   device_arn = arn:aws:iot:eu-central-1:999999999999:thing/HelloWorld_Publisher
   one_device_status = //greengrass/device/one-aws-gg-MyFirstGroup-device-status-b0c6c9c1fa.json

2. Create the second device ``HelloWorld_Subscriber`` with this context:

|image-context-device-subscriber-api|

Result:

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

   [greengrass device]
   device_dir = //greengrass/device
   device_name = HelloWorld_Subscriber
   device_id = d286c99a-18ad-4a3d-a67e-6216b65e3afc
   device_arn = arn:aws:iot:eu-central-1:999999999999:thing/HelloWorld_Subscriber
   one_device_status = //greengrass/device/one-aws-gg-MyFirstGroup-device-status-33ff050ab0.json

3. You should see them in AWS Console:

|image-console-device|

2. Subscriptions
~~~~~~~~~~~~~~~~

Create `subscriptions <https://docs.aws.amazon.com/greengrass/latest/developerguide/config-subs.html>`_.

3. Distribute the test script to the both devices
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. We will not follow the next set of `instructions (IoT SDK) <https://docs.aws.amazon.com/greengrass/latest/developerguide/IoT-SDK.html>`_ exactly and instead just somewhere execute:

.. prompt:: text [remote]$ auto

    $ git clone https://github.com/aws/aws-iot-device-sdk-python.git

2. Save the file ``aws-iot-device-sdk-python/samples/greengrass/basicDiscovery.py`` - it will be needed soon.
3. Login to your device ``HelloWorld_Publisher`` (over ssh or through OpenNebula Sunstone Console)
4. Copy the file ``aws-iot-device-sdk-python/samples/greengrass/basicDiscovery.py`` into ``/greengrass/device/``.
5. In a new window: login to your device ``HelloWorld_Subscriber`` (over ssh or through OpenNebula Sunstone Console)
6. Again copy the file ``aws-iot-device-sdk-python/samples/greengrass/basicDiscovery.py`` into ``/greengrass/device/``.
7. Now we have the script on both devices and we will loosely follow the last set of `instructions (Test Communications) <https://docs.aws.amazon.com/greengrass/latest/developerguide/test-comms.html>`_.

4. Test publish/subscribe communication
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. As is noted in `the tutorial (Test Communications) <https://docs.aws.amazon.com/greengrass/latest/developerguide/test-comms.html>`_ we will need the endpoint - it is the same we used during the creation of the devices.
2. In the window where you are logged to the ``HelloWorld_Publisher`` - execute (**change endpoint and certificate filenames to yours**):

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

3. In another window where you are logged to the ``HelloWorld_Subscriber`` - execute (**change endpoint and certificate filenames to yours**):

.. prompt:: text [remote]$ auto

    $ python /greengrass/device/basicDiscovery.py \
    --endpoint XXXXXXXXXXXXXX-ats.iot.eu-central-1.amazonaws.com \
    --rootCA /greengrass/certs/root.ca.pem \
    --cert /greengrass/device/certs/33ff050ab0.cert.pem \
    --key /greengrass/device/certs/33ff050ab0.private.key \
    --thingName HelloWorld_Subscriber \
    --topic 'hello/world/pubsub' \
    --mode subscribe

4. You should see the same output as is in the tutorial: `Test Communications <https://docs.aws.amazon.com/greengrass/latest/developerguide/test-comms.html>`_ - step **4.**

Automated bulk mode
-------------------

As the last example will serve this demonstration of the **bulk** mode.

This will create Greengrass Group, Core device and a bunch of IoT devices added to this group. And all is done by the appliance itself - no interacting with the AWS Console or other tools.

Set the contextualization parameters similar to this:

|image-context-bulk-api|

After a few moments all work should be done:

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
    aws_secret_access_key = XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

    [greengrass core]
    core_config = //greengrass/config/config.json
    core_thing_name = one_core_1_Core
    core_thing_arn = arn:aws:iot:eu-central-1:999999999999:thing/one_core_1_Core
    group_name = one core 1
    group_id = ce2a7e63-05ab-4614-bb32-7f30f1470321
    group_arn = arn:aws:greengrass:eu-central-1:999999999999:/greengrass/groups/ce2a7e63-05ab-4614-bb32-7f30f1470321
    group_latest_version = d2fa35b3-f06c-4403-8ef0-4c2fdf0fb8c9
    group_latest_version_arn = arn:aws:greengrass:eu-central-1:999999999999:/greengrass/groups/ce2a7e63-05ab-4614-bb32-7f30f1470321/versions/d2fa35b3-f06c-4403-8ef0-4c2fdf0fb8c9
    iot_mqtt_port = 8883
    iot_http_port = 8443
    gg_http_port = 8443
    one_core_status = //greengrass/core/one-aws-gg-one_core_1-core-status-04d76cb07a.json

    [greengrass devices]
    devices_dir = /greengrass/device
    devices_count = 5

.. |image-download| image:: /images/kubernetes/kubernetes-download.png
.. |image-context-vars| image:: /images/aws-greengrass/awsgg-context-vars.png
.. |image-context-bulk-api| image:: /images/aws-greengrass/awsgg-bulk-api-context-values.png
.. |image-context-core-api| image:: /images/aws-greengrass/awsgg-core-api-context-values.png
.. |image-context-device-api| image:: /images/aws-greengrass/awsgg-device-api-context-values.png
.. |image-context-device-publisher-api| image:: /images/aws-greengrass/awsgg-device-publisher-api-context-values.png
.. |image-context-device-subscriber-api| image:: /images/aws-greengrass/awsgg-device-subscriber-api-context-values.png
.. |image-context-core-files| image:: /images/aws-greengrass/awsgg-core-files-context-values.png
.. |image-console-create-group-1| image:: /images/aws-greengrass/awsgg-console-create-group-1.png
.. |image-console-create-group-2| image:: /images/aws-greengrass/awsgg-console-create-group-2.png
.. |image-console-deploy| image:: /images/aws-greengrass/awsgg-console-deploy.png
.. |image-console-test| image:: /images/aws-greengrass/awsgg-console-test.png
.. |image-console-device| image:: /images/aws-greengrass/awsgg-console-device.png
.. |image-console-lambda-1| image:: /images/aws-greengrass/awsgg-console-lambda-1.png
.. |image-console-lambda-2| image:: /images/aws-greengrass/awsgg-console-lambda-2.png
.. |image-console-lambda-3| image:: /images/aws-greengrass/awsgg-console-lambda-3.png
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
