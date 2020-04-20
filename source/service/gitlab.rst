.. _service_gl:

==============
Service GitLab
==============

OpenNebula `Marketplace Appliance <https://marketplace.opennebula.systems/appliance/6b54a412-03a5-11e9-8652-f0def1753696>`_  with preinstalled `GitLab <https://about.gitlab.com/>`_ service.

Without any parameters provided, the appliance boots into the web setup wizard and user has to finish the GitLab initial configuration manually in the browser. This part can be automated (as part of the bootstrap process) with :ref:`contextualization <gitlab_context_param>` parameters.

.. include:: shared/features.txt

Platform Notes
==============

Appliance components versions:

============================= ==================
Component                     Version
============================= ==================
GitLab                        12.9.3 CE
Contextualization package     5.10.0
============================= ==================

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <gitlab_context_param>` parameters:

|image-context-vars|

.. note::

    The last three inputs are the :ref:`bootstrap parameters <gitlab_bootstrap>` - **all of them must be set** for the bootstrap stage to work. The stage is skipped if any of them are missing.

After you are done, click on the button **Instantiate**. Virtual machine with running service should be ready in a few minutes.

.. include:: shared/report.txt

.. code::

       ___   _ __    ___
      / _ \ | '_ \  / _ \   OpenNebula Service Appliance
     | (_) || | | ||  __/
      \___/ |_| |_| \___|

    All set and ready to serve 8)

   [root@localhost ~]# cat /etc/one-appliance/config
   [GitLab]
   site_url = https://192.168.122.10
   username = john
   password = 18lv0wBG5
   email = john@example.com

.. _gitlab_context_param:

Contextualization
=================

Contextualization parameters provided in the Virtual Machine template controls the initial VM configuration. Except for the `common set <http://docs.opennebula.org/stable/operation/references/template.html#context-section>`_ of parameters supported by every appliance on the OpenNebula Marketplace, there are few specific to the particular service appliance. The parameters should be provided in the ``CONTEXT`` section of the Virtual Machine template, read the OpenNebula `Operation Guide <http://docs.opennebula.org/stable/operation/vm_setup/kvm.html#set-up-the-virtual-machine-template>`__ for more details.

===================================== ========= ============== ========= ===========
Parameter                             Mandatory Default        Stage     Description
===================================== ========= ============== ========= ===========
``ONEAPP_SITE_HOSTNAME``                        routable IP    configure Fully qualified domain name or IP
``ONEAPP_SITE_RELATIVE_URL``                    ``/``          configure Relative URL to the service
``ONEAPP_HTTP_REDIRECT``                        ``yes``        configure Automatic HTTP to HTTPS redirection
``ONEAPP_SSL_CERT``                                            configure SSL certificate
``ONEAPP_SSL_PRIVKEY``                                         configure SSL private key
``ONEAPP_SSL_CHAIN``                                           configure SSL CA chain
``ONEAPP_ADMIN_USERNAME``                       ``root``       bootstrap Site Administrator Login
``ONEAPP_ADMIN_PASSWORD``                                      bootstrap Site Administrator Password
``ONEAPP_ADMIN_EMAIL``                                         bootstrap Site Administrator E-mail
===================================== ========= ============== ========= ===========

.. include:: shared/site_address.txt

The initial value of ``ONEAPP_SITE_HOSTNAME`` is stored on your running virtual machine in the GitLab configuration. It can be changed in the file ``/etc/gitlab/gitlab.rb`` with the parameter ``external_url``. After the change, it's necessary to trigger the GitLab reconfiguration:

.. prompt:: text $ auto

    $ gitlab-ctl reconfigure

SSL
---

If ``ONEAPP_SSL_CERT`` and ``ONEAPP_SSL_PRIVKEY`` are set, the service will be configured to listen to both on HTTP (port 80) and secured HTTPS (port 443). The ``ONEAPP_SSL_CHAIN`` is optional for the service configuration but may be necessary for the connection verification by the clients.

.. note::

    Automatic redirection from HTTP to HTTPS **is enabled by default**. You can disable this behaviour by setting the contextualization parameter ``ONEAPP_HTTP_REDIRECT`` to ``no``.

.. include:: shared/cert.txt

Let's Encrypt automation
~~~~~~~~~~~~~~~~~~~~~~~~

Not supported yet.

.. GitLab has it builtin - see ``ONEAPP_SSL_LETSENCRYPT`` in :ref:`contextualization parameters <gitlab_context_param>`.

.. GitLab has builtin support for `Let's Encrypt <https://letsencrypt.org>`_ certificates. You can just set the contextualization parameter ``ONEAPP_SSL_LETSENCRYPT`` to **yes** and ``ONEAPP_SITE_ADDRESS`` to a valid fully qualified domain name and if your appliance is reachable from the internet then GitLab should automate Let's Encrypt certificates for you.

.. **NOTE**: If you set ``ONEAPP_SSL_CERT`` and ``ONEAPP_SSL_PRIVKEY`` then ``ONEAPP_SSL_LETSENCRYPT`` is ignored.

.. _gitlab_bootstrap:

Bootstrap
---------

.. note::

    All parameters must be defined all together to take effect. Otherwise, the bootstrap is skipped.

Bootstrap configures the initial parameters inside the GitLab service itself (GitLab user name, password and e-mail of the administrator). It's controlled by following contextualization parameters:

* ``ONEAPP_ADMIN_USERNAME`` - cannot be **admin** - `reserved words <https://docs.gitlab.com/ce/user/reserved_names.html>`_
* ``ONEAPP_ADMIN_PASSWORD`` - at least 8 characters long
* ``ONEAPP_ADMIN_EMAIL``

or, can be specified in the OpenNebula Sunstone on VM template instantiation:

|image-bootstrap-values|

For more information about GitLab's configuration - visit their `documentation <https://docs.gitlab.com/omnibus/settings/configuration.html>`_.

.. |image-download| image:: /images/gitlab/gitlab-download.png
.. |image-ssl-context-values| image:: /images/gitlab/gitlab-ssl-context-values.png
.. |image-bootstrap-values| image:: /images/gitlab/gitlab-bootstrap-values.png
.. |image-context-vars| image:: /images/gitlab/gitlab-context-vars.png
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
