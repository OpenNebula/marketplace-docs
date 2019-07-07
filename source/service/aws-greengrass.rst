.. _service_awsgg:

======================
Service AWS GreenGrass
======================

OpenNebula `Marketplace Appliance (FIX LINK) <https://marketplace.opennebula.systems/appliance/placeholder>`_  with preinstalled `AWS GreenGrass <https://docs.aws.amazon.com/greengrass/latest/developerguide/what-is-gg.html>`_ service.

If no parameters are provided then the appliance will not provision any GreenGrass Group, Core or any device, but it will provide the user with a ready environment. User then can proceed with `the official guide <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html>`_ by skipping the first module and start from `module 2 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module2.html>`_.

Though the real power and benefits of this appliance manifest when user *does* provide the :ref:`contextualization <awsgg_context_param>` parameters. Thanks to them, the appliance supports multiple ways of deployment:

* **Semi-manual setup** (via configuration files)
   * Deploy already created Core device or IoT device (in already created GreenGrass Group)
   * Deploy Core device (like above) but also create multiple new IoT devices at once (bulk role)
* **Automatic setup** (no files provided)
   * Create new GreenGrass Group and Core device
   * Create IoT device (in already created GreenGrass Group)
   * Create GreenGrass Group, Core and one or more IoT devices all at once (bulk role)

AWS GreenGrass appliance can also store *thing* names of Core devices and IoT devices in OpenNebula `OneFlow service <http://docs.opennebula.org/stable/advanced_components/application_flow_and_auto-scaling/appflow_use_cli.html>`_ (if applicable).

.. _awsgg_features:

.. include:: shared/features.txt

Platform Notes
==============

Appliance components versions:

============================= ==================
Component                     Version
============================= ==================
AWS GreenGrass Core           1.9.1
python                        2.7
greengrasssdk                 1.4
AWSIoTPythonSDK               1.4.6
awscli                        1.16.174
boto3                         1.9.164
node.js                       8.10
Contextualization package     5.8.0
============================= ==================

Requirements:

* OpenNebula 4.14 - 5.8
* Min. Memory: 2 GB
* Min. Cores (VCPU): 1

.. note::

   AWS Greengrass cannot run on the default distribution kernel - therefore we are installing the ElRepo kernel (Long Term).

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <awsgg_context_param>` parameters:

|image-context-vars|

.. note::

    The first input **Deployment role** determines if the appliance creates a Core device, IoT device or both (bulk) and based on that it will expect certain inputs to be set. If you don't intend to provision the appliance either by configuration files or by providing the API credentials then leave **\*ALL*** inputs empty - otherwise the appliance will fail. In the case of provisioned appliance (most of the cases) jump to :ref:`contextualization <awsgg_context_param>` where are descriptions of valid contexts and modes.

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

   This *Quick Start* example had no contextualization and so no Core, Device or GreenGrass Group was created or deployed (that is the reason why the report file has barely any info). To make use of such appliance you must visit `the official guide <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html>`_ of the AWS GreenGrass and follow the instructions there - you can skip the `module 1 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module1.html>`_ and go directly to `module 2 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module2.html>`_.

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
``ONEAPP_AWSGG_CONFIG``                                              configure core             GreenGrass Group config file
``ONEAPP_AWSGG_GROUP``                                               configure all (automatic)  GreenGrass Group name (only in **automatic** mode)
``ONEAPP_AWSGG_DEVICE_NAME``                                         configure device/bulk      Device name for device/bulk role (auto-postfixed for bulk role)
``ONEAPP_AWSGG_DEVICE_HASH``                    ``device``           configure device (manual)  Hash prefix (created by AWS, only in **semi-manual** mode)
``ONEAPP_AWSGG_ENDPOINT``                                            configure all              API endpoint (it can be located in the AWS UI Console)
``ONEAPP_AWSGG_ACCESS_KEY_ID``                                       configure all              API access key id (AWS UI Console)
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``                                   configure all              API secret access key (AWS UI Console)
``ONEAPP_AWSGG_GGC_USER``                       ``ggc_user``         configure                  Local user name to own process/files
``ONEAPP_AWSGG_GGC_GROUP``                      ``ggc_group``        configure                  Local user group to own process/files
``ONEAPP_AWSGG_IOTMQTTPORT``                    ``8883``             configure core             MQTT port to IoT (8883 or 443)
``ONEAPP_AWSGG_IOTHTTPPORT``                    ``8443``             configure core             HTTPS port to IoT (8443 or 443)
``ONEAPP_AWSGG_GGHTTPPORT``                     ``8443``             configure core             HTTPS port to GG (8443 or 443)
===================================== ========= ==================== ========= ================ ===========

If no contextualization parameter is provided, the appliance will not deploy/create anything and it will only provide an AWS GreenGrass compatible system environment (due to preinstalled software and compliant kernel).

.. note::

   AWS GreenGrass cannot function without a Core device and no GreenGrass Group can operate without its Core - these two elements are fundamental to the concept and must be always present. More info about configuration of AWS Greengrass Core is `here <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-core.html>`_.

.. important::

   For the semi-manual way you don't need to setup aws CLI, but in the case of the full automation, the credential parameters are mandatory. Also, if you provide contextualization parameters for both methods then **THE SEMI-MANUAL METHOD WILL TAKE PRECEDENCE**.

.. _awsgg_manual_mode:

Semi-manual method (configuration files)
----------------------------------------

In this type of deployment, you provide certficate files and a config file (in the case of a Core device). API is used only in bulk role when we create one or more devices on top of the Core deployment.

Deploy Core device (role: core)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

GreenGrass Group and Core device must be already created (by tool, API, AWS Console etc.) and you should have ready all the necessary files (certs and config). These files can be extracted from the archive which should have been downloaded when `the Group and Core device was created in AWS Console <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html>`_. Of course both the Group and Core could be created in another way (via API for example) so it is your responsibility to have these files somewhere safely stored.

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

All GreenGrass related files are in ``/greengrass/`` and the Core device specific are:

   * ``/greengrass/certs/*``
   * ``/greengrass/config/config.json``

.. _awsgg_device_file:

Deploy IoT device (role: device)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Similar to the previous case - GreenGrass Group must exist and so does the IoT device which we want to deploy. You must also have ready the associated certs for this device (for example downloaded as an archive during `the device creation in AWS Console <https://docs.aws.amazon.com/greengrass/latest/developerguide/device-group.html>`_).

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

Device certs are stored in: ``/greengrass/device/``.

Deploy Core device and create IoT device(s) (role: bulk)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This behaves identically to the deployment of a Core device but it also creates one or more devices (via AWS API) and stores their certs in: ``/greengrass/device/``.

====================================== ========================================================================================================
``ONEAPP_AWSGG_ROLE=bulk``             **Role "bulk"**
``ONEAPP_AWSGG_CONFIG``                Greengrass Core config file
``ONEAPP_AWSGG_SSL_CERT``              Greengrass Core certificate
``ONEAPP_AWSGG_SSL_PRIVKEY``           Greengrass Core private key
``ONEAPP_AWSGG_SSL_PUBKEY``            Greengrass Core public key
``ONEAPP_AWSGG_ENDPOINT``              The API endpoint - it can be found in the UI
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

.. important::

   Context to configure aws CLI is optional when you provision Core and device via files but **mandatory** for **bulk** role:

  - ``ONEAPP_AWSGG_ENDPOINT``
  - ``ONEAPP_AWSGG_ACCESS_KEY_ID``
  - ``ONEAPP_AWSGG_ACCESS_SECRET_KEY``

Automatic method (API provisioned)
----------------------------------

In this fully automated deployment, you don't need to create GreenGrass Group, Core or IoT device before you can deploy the appliance (but the Group still must already exist for the IoT device deployment/creation). You need to have valid AWS API credentials including a correct endpoint with `a proper supported region <https://docs.aws.amazon.com/general/latest/gr/rande.html#greengrass_region>`_. The appliance will create all needed artifacts in the cloud and it will provision itself.

Create Core device (role: core)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

With this set of contextualization parameters the appliance will connect to the AWS API and it will create a GreenGrass Group and its Core device in the designated AWS region (defined by your endpoint). If a GreenGrass Group of such a name exists already then the deployment will abort and fail.

====================================== =======================================================
``ONEAPP_AWSGG_ROLE=core``             **Role "core"**
``ONEAPP_AWSGG_GROUP``                 Name of the Greengrass group you want to create
``ONEAPP_AWSGG_ENDPOINT``              The API endpoint - it can be found in the UI
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

All GreenGrass related files are in ``/greengrass/`` and the Core device specific are:

   - ``/greengrass/core/``
   - ``/greengrass/certs/``
   - ``/greengrass/config/config.json``

Create IoT device (role: device)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

GreenGrass Group has to be created already, but the device itself is created by the appliance.

====================================== ======================================================================================
``ONEAPP_AWSGG_ROLE=device``           **Role "device"**
``ONEAPP_AWSGG_GROUP``                 Name of the Greengrass Group to which the new device assign (it must exist already)
``ONEAPP_AWSGG_ENDPOINT``              The API endpoint - it can be found in the UI
``ONEAPP_AWSGG_ACCESS_KEY_ID``         AWS account API credential key id
``ONEAPP_AWSGG_ACCESS_SECRET_KEY``     AWS account API credential secret
``ONEAPP_AWSGG_DEVICE_NAME``           Name of the device you want to create (must be unique across all iot things)
``ONEAPP_AWSGG_GGC_USER``              (Optional)
``ONEAPP_AWSGG_GGC_GROUP``             (Optional)
====================================== ======================================================================================

The result is practically the same as with :ref:`the file provisioning <awsgg_device_file>`:

   - There is no service/daemon to be started - check out `the device documentation <https://docs.aws.amazon.com/greengrass/latest/developerguide/module4.html>`_.
   - Device certs are stored in: ``/greengrass/device/``.

Create Core device and IoT device(s) (bulk)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Simply put: this appliance will create GreenGrass Group, Core device and one or more IoT devices - if there will be a Group of the same name or there will be a collision amongs the *thing* names then the appliance will abort and fail.

=================================== ==========================================================================================================
``ONEAPP_AWSGG_ROLE=bulk``          **Role "bulk"**
``ONEAPP_AWSGG_GROUP``              Name of the Greengrass group you want to create
``ONEAPP_AWSGG_ENDPOINT``           The API endpoint - it can be found in the UI
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

Important files are created in these locations:

   - ``/greengrass/core/``
   - ``/greengrass/certs/``
   - ``/greengrass/config/config.json``
   - ``/greengrass/device/``

.. important::

   Using the automatic mode you **MUST** provide the credentials for aws CLI (core, device and bulk role):

  - ``ONEAPP_AWSGG_ENDPOINT``
  - ``ONEAPP_AWSGG_ACCESS_KEY_ID``
  - ``ONEAPP_AWSGG_ACCESS_SECRET_KEY``

Examples
========

In this section, we demonstrate the usage of this appliance.

Official AWS GreenGrass tutorial
--------------------------------

AWS provides a nice `Getting Started <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html>`_ tutorial how to start with GreenGrass. We actually start from `module 2 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module2.html>`_ but for your own benefit you should read it from beginning.

Create GreenGrass Group and Core device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Open in your browser `the module 2 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module2.html>`_ of AWS GreenGrass tutorial and also log into AWS Console in other window or a tab.

.. important::

   You cannot continue and deploy our appliance if you don't have any AWS account.

Follow `the instructions <https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html>`_ which help you to create **MyFirstGroup**. You don't need to bother with anything else but the wizard in AWS Console.

You should reach this screen (files/hashes will differ):

|image-console-create-group-1|

**BEFORE** you hit the finish button you **MUST** click on **"Download these resources as a tar.gz"** and store this archive somewhere. Now you can press **Finish** button and you should see the same screen as is in the tutorial:

|image-console-create-group-2|

Ignore the step **10.** and jump to `the next module 3 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module3-I.html>`_.

Create Lambda function
~~~~~~~~~~~~~~~~~~~~~~

You can either follow instructions there (no harm in it) or follow ours instead (we are skipping the uploading of a zip file).

Go to AWS Lambda:

|image-console-lambda-1|

And create a new lambda function **Greengrass_HelloWorld**:

|image-console-lambda-2|

On the next screen scroll little down to **Function code** and set:

   - Runtime: ``Python 2.7``
   - Handler: ``lambda_function.function_handler``

Delete the content of **lambda_function** and instead copy there this:

.. code::

   import greengrasssdk
   import platform
   from threading import Timer
   import time

   client = greengrasssdk.client('iot-data')
   my_platform = platform.platform()

   def greengrass_hello_world_run():
       if not my_platform:
           client.publish(topic='hello/world', payload='Hello world! Sent from Greengrass Core.')
       else:
           client.publish(topic='hello/world', payload='Hello world! Sent from Greengrass Core running on platform: {}'.format(my_platform))
       Timer(5, greengrass_hello_world_run).start()

   greengrass_hello_world_run()

   def function_handler(event, context):
       return

It should look like in this picture:

|image-console-lambda-3|

Press the **Save** button in the top right of the page.

Now go to `the tutorial (create lambda) <https://docs.aws.amazon.com/greengrass/latest/developerguide/create-lambda.html>`_ again and do the rest of steps from **9.** included.

Continue and do all steps on `the next page (config lambda) <https://docs.aws.amazon.com/greengrass/latest/developerguide/config-lambda.html>`_.

.. note::

   To get back to GreenGrass UI: Services -> AWS IoT -> GreenGrass -> Groups -> MyFirstGroup

OpenNebula AWS GreenGrass appliance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For the next part we must first prepare our appliance (finally). The time has come for that tar archive which you downloaded few moments ago. Unpack the content and locate **config.json**, **certificate**, **private key** and **public key**. Content of these files put into the right contextualization parameters, like so:

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

Deploy and test
~~~~~~~~~~~~~~~

Return to `the tutorial (configs core) <https://docs.aws.amazon.com/greengrass/latest/developerguide/configs-core.html>`_ and deploy your lambda to the Core device. You should see **Successfully completed**:

|image-console-deploy|

The last step is to check that lambda is really running on your Core. Execute `these steps (lambda check) <https://docs.aws.amazon.com/greengrass/latest/developerguide/lambda-check.html>`_ to verify your setup. Your output should resemble the following:

|image-console-test|

Official tutorial revisited - OpenNebula style
----------------------------------------------

The previous example was rather tedious - a lot of mouse clicking and writing. Many manual and error-prone steps.

We can do it better - introducing our killer-feature (or we think) of the ONE service apliance: **Automated GreenGrass Group, Core and Device creation and deployment**

Context for automated setup:

|image-context-core-api|

When the appliance is finished bootstrapping, you can check it out:

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

At this point you already have GreenGrass Group and Core device up and running. Now you can fastforward to `the module 3 <https://docs.aws.amazon.com/greengrass/latest/developerguide/module3-I.html>`_. Just create the lambda function as is in the tutorial, create subscription and deploy. No more jumping from AWS Console to VM and back.

Automated device creation/deployment
------------------------------------

Context:

|image-context-device-api|

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
   device_name = group1-dev
   device_id = 470371c8-122c-4153-a298-3e9de20b57c3
   device_arn = arn:aws:iot:eu-central-1:999999999999:thing/group1-dev
   one_device_status = //greengrass/device/one-aws-gg-MyFirstGroup-device-status-7b50ee32f7.json

And in AWS Console:

|image-console-device|

Automated bulk mode
-------------------

As the last example will serve this demonstration of the **bulk** mode.

This will create GreenGrass Group, Core device and a bunch of IoT devices added to this group. And all is done by the appliance itself - no interacting with the AWS Console or other tools - just set the contextualization parameters similar to this:

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
