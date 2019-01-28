.. _service_gl:

==============
Service GitLab
==============

ONE service appliance which deploys a `GitLab <https://about.gitlab.com/>`_ instance.

If you don't provide any :ref:`contextualization <gitlab_context_param>` then the GitLab appliance will still be autoconfigured properly, but it will not attempt a bootstrap and it will greet you with it's initial web page for setting up the password.

.. include:: shared/general.txt

|

Quick Start
===========

Download GitLab appliance image
-------------------------------

Open your OpenNebula's web interface (Sunstone). After the login, go to **Storage->Apps** and import the GitLab appliance to your datastore (blue button with a cloud):

|image-download|

Tweak GitLab VM template
------------------------

After the download go to **Templates->VMs**, click on the new GitLab template and update it via the button on the top (**Update**). On the first screen you can customize memory and cpu (and other) how you see fit - or leave it as it is - it will be sufficient to start off.

When you are happy with the values here switch to the tab **Context**. You can add your SSH public key here:

|image-ssh-context|

.. include:: shared/ssh.txt

We can also just set the root's password in the **Custom vars** (pick a better one than what is on the picture):

|image-custom-vars-password|

There is a better way via **User Inputs** but we will not cover this here.

Whether you setup SSH or just password is up to you, the point is that we can login to the VM now if we need.

We are done with the template for now so just click the green button called **Update**.

Instantiation of the GitLab appliance
----------------------------------------

We can instantiate our template now - click on the blue button **Instantiate**. You must fill in the **VM name** and you should setup the network properly with a routable IP address.

There are also :ref:`contextualization <gitlab_context_param>` variables which you can set.

The last three of those are so-called :ref:`bootstrap parameters <gitlab_bootstrap_param>` - all of them must be set for the :ref:`bootstrap <gitlab_bootstrap>` to work. You don't need to set any of them, but in that case the GitLab will force you to setup password first.

|image-context-vars|

After you are done, click the green button **Instantiate**. If you did everything correctly then you should have GitLab ready on the provided IP address in a few minutes.

|

.. _gitlab_context_param:

Contextualization Parameters
============================

Table of configure & bootstrap contextualization parameters

===================================== ========= ==== ========= ===========
Parameter                             Mandatory Auto Stage     Description
===================================== ========= ==== ========= ===========
``ONEAPP_SITE_HOSTNAME``                        yes  configure Fully qualified domain name or IP
``ONEAPP_SITE_RELATIVE_URL``                    yes  configure Relative URL to the service (default: /)
``ONEAPP_HTTP_REDIRECT``                        yes  configure Automatic HTTP to HTTPS redirection (default: yes)
``ONEAPP_SSL_LETSENCRYPT``                      yes  configure Enable Let's Encrypt for SSL (default: no)
``ONEAPP_SSL_CERT``                                  configure SSL certificate
``ONEAPP_SSL_PRIVKEY``                               configure SSL private key
``ONEAPP_SSL_CHAIN``                                 configure SSL CA chain
``ONEAPP_ADMIN_USERNAME``                            bootstrap Site Administrator Login
``ONEAPP_ADMIN_PASSWORD``                            bootstrap Site Administrator Password
``ONEAPP_ADMIN_EMAIL``                               bootstrap Site Administrator E-mail
===================================== ========= ==== ========= ===========

Site address
------------

The appliance will listen on the address defined in ``ONEAPP_SITE_HOSTNAME``. If you don't provide this contextualization parameter then it will try to use the first routable address of the VM instance. If there are only loopback address(es) then it will fallback to ``localhost``, but at that point the appliance will function only from within the VM instance - which is meaningless in most of the cases. Therefore assign a proper IP address to your ONE service appliance. And to make everything certain - set this address as a value to ``ONEAPP_SITE_HOSTNAME``.

Value of the ``ONEAPP_SITE_HOSTNAME`` once provisioned is stored in the settings of the running appliance. It can be changed in the ``/etc/gitlab/gitlab.rb`` on the line with ``external_url`` after which you must run::

   $ gitlab-ctl reconfigure

Certificates
------------

If ``ONEAPP_SSL_CERT`` and ``ONEAPP_SSL_PRIVKEY`` are set then the appliance will be configured to listen on both http (tcp port 80) and https (tcp port 443). Redirection http -> https is enabled by default - you can disable it with ``ONEAPP_HTTP_REDIRECT`` setting to **no**.

Provided values must be valid of course. The ``ONEAPP_SSL_CHAIN`` is not mandatory for service script to setup SSL (https), but that mainly depends on the certs which you will be using (:ref:`self-signed vs signed by CA <gitlab_ssl>`).

GitLab has builtin support for `Let's Encrypt <https://letsencrypt.org>`_ certificates. You can just set the contextualization parameter ``ONEAPP_SSL_LETSENCRYPT`` to **yes** and ``ONEAPP_SITE_ADDRESS`` to a valid fully qualified domain name and if your appliance is reachable from the internet then GitLab should automate Let's Encrypt certificates for you.

**NOTE**: If you set ``ONEAPP_SSL_CERT`` and ``ONEAPP_SSL_PRIVKEY`` then ``ONEAPP_SSL_LETSENCRYPT`` is ignored.

.. _gitlab_bootstrap_param:

Bootstrap
---------

The bootstrap's stage contextualization parameters must be defined all together to take effect. Otherwise the bootstrap cannot be done:

* ``ONEAPP_ADMIN_USERNAME``: cannot be **admin** - `reserved words <https://docs.gitlab.com/ce/user/reserved_names.html>`_
* ``ONEAPP_ADMIN_PASSWORD``: must be at least eight characters long
* ``ONEAPP_ADMIN_EMAIL``

For more information about GitLab's configuration - visit their `documentation <https://docs.gitlab.com/omnibus/settings/configuration.html>`_.

Advanced Usage
==============

.. _gitlab_bootstrap:

Bootstrapping
-------------

Providing the bootstrapping values during instantiation of the appliance:

|image-bootstrap-values|

.. _gitlab_ssl:

SSL
---

If you provide SSL :ref:`contextualization <gitlab_context_param>` then your appliance will be reacheable over **https**.

.. include:: shared/cert.txt

Filling up the the contextualization parameters with our self-signed certificate:

|image-ssl-context-values|

The browser will complain because of our self-signed certificate but you can add the exception for this site and it will function properly.

Let's Encrypt automation
~~~~~~~~~~~~~~~~~~~~~~~~

GitLab has it builtin - see ``ONEAPP_SSL_LETSENCRYPT`` in :ref:`contextualization parameters <gitlab_context_param>`.

Config report
-------------

When ONE service appliance's configure & bootstrap stage is finished then there is created a :ref:`report config file <one_service_logs>` which stores many important information about the running appliance.

Login to the VM of the appliance::

       ___   _ __    ___
      / _ \ | '_ \  / _ \   OpenNebula Service Appliance
     | (_) || | | ||  __/
      \___/ |_| |_| \___|
   
    All set and ready to serve 8)
   
   [root@localhost ~]# cat /etc/one-appliance/config
   [GitLab]
   site_url = https://192.168.122.10
   username = osp
   password = 18lv0wBG5
   email = pospaly@opennebula.systems

.. |image-download| image:: /images/gitlab/gitlab-download.png
.. |image-ssl-context-values| image:: /images/gitlab/gitlab-ssl-context-values.png
.. |image-bootstrap-values| image:: /images/gitlab/gitlab-bootstrap-values.png
.. |image-context-vars| image:: /images/gitlab/gitlab-context-vars.png
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
