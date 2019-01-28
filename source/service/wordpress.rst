.. _service_wp:

=================
Service WordPress
=================

ONE service appliance which deploys a `WordPress <https://wordpress.org/>`_ instance.

If you don't provide any :ref:`contextualization <wordpress_context_param>` then the WordPress appliance will still be autoconfigured properly, but it will not attempt a bootstrap and it will greet you with it's web setup wizard.

.. include:: shared/general.txt

|

Quick Start
===========

Download WordPress appliance image
----------------------------------

Open your OpenNebula's web interface (Sunstone). After the login, go to **Storage->Apps** and import the WordPress appliance to your datastore (blue button with a cloud):

|image-download|

Tweak WordPress VM template
---------------------------

After the download go to **Templates->VMs**, click on the new WordPress template and update it via the button on the top (**Update**). On the first screen you can customize memory and cpu (and other) how you see fit - or leave it as it is - it will be sufficient to start off.

When you are happy with the values here switch to the tab **Context**. You can add your SSH public key here:

|image-ssh-context|

.. include:: shared/ssh.txt

We can also just set the root's password in the **Custom vars** (pick a better one than what is on the picture):

|image-custom-vars-password|

There is a better way via **User Inputs** but we will not cover this here.

Whether you setup SSH or just password is up to you, the point is that we can login to the VM now if we need.

We are done with the template for now so just click the green button called **Update**.

Instantiation of the WordPress appliance
----------------------------------------

We can instantiate our template now - click on the blue button **Instantiate**. You must fill in the **VM name** and you should setup the network properly with a routable IP address.

There are also :ref:`contextualization <wordpress_context_param>` variables which you can set:

|image-context-vars|

The last four of those are so-called :ref:`bootstrap parameters <wordpress_bootstrap_param>` - all of them must be set for the :ref:`bootstrap <wordpress_bootstrap>` to work. You don't need to set any of them, but in that case the WordPress initial setup wizard will await you.

After you are done, click the green button **Instantiate**. If you did everything correctly then you should have WordPress ready on the provided IP address in a minute or two (may take longer in your scenario).

|

.. _wordpress_context_param:

Contextualization Parameters
============================

Table of configure & bootstrap contextualization parameters

===================================== ========= ==== ========= ===========
Parameter                             Mandatory Auto Stage     Description
===================================== ========= ==== ========= ===========
``ONEAPP_SITE_HOSTNAME``                        yes  configure Fully qualified domain name or IP
``ONEAPP_DB_NAME``                              yes  configure Name of the WordPress database
``ONEAPP_DB_USER``                              yes  configure Database's owner username
``ONEAPP_DB_PASSWORD``                          yes  configure Database's owner password
``ONEAPP_DB_ROOT_PASSWORD``                     yes  configure Database's root password
``ONEAPP_SSL_CERT``                                  configure SSL certificate
``ONEAPP_SSL_PRIVKEY``                               configure SSL private key
``ONEAPP_SSL_CHAIN``                                 configure SSL CA chain
``ONEAPP_SITE_TITLE``                                bootstrap Site Title
``ONEAPP_ADMIN_USERNAME``                            bootstrap Site Administrator Login
``ONEAPP_ADMIN_PASSWORD``                            bootstrap Site Administrator Password
``ONEAPP_ADMIN_EMAIL``                               bootstrap Site Administrator E-mail
===================================== ========= ==== ========= ===========

Site address
------------

The appliance will listen on the address defined in ``ONEAPP_SITE_HOSTNAME``. If you don't provide this contextualization parameter then it will try to use the first routable address of the VM instance. If there are only loopback address(es) then it will fallback to ``localhost``, but at that point the appliance will function only from within the VM instance - which is meaningless in most of the cases. Therefore assign a proper IP address to your ONE service appliance. And to make everything certain - set this address as a value to ``ONEAPP_SITE_HOSTNAME``.

Value of the ``ONEAPP_SITE_HOSTNAME`` once provisioned is stored in the settings of the running appliance. It can be changed in the settings of the appliance after you login as an admin.

Certificates
------------

If ``ONEAPP_SSL_CERT`` and ``ONEAPP_SSL_PRIVKEY`` are set then the appliance will be configured to listen on both http (tcp port 80) and https (tcp port 443). No redirection is made.

Provided values must be valid of course. The ``ONEAPP_SSL_CHAIN`` is not mandatory for service script to setup SSL (https), but that mainly depends on the certs which you will be using (:ref:`self-signed vs signed by CA <wordpress_ssl>`). For example the `Let's Encrypt <https://letsencrypt.org>`_ certificate will need all three variables to be set.

.. _wordpress_bootstrap_param:

Bootstrap
---------

The bootstrap's stage contextualization parameters must be defined all together to take effect. Otherwise the bootstrap cannot be done:

* ``ONEAPP_SITE_TITLE``
* ``ONEAPP_ADMIN_USERNAME``
* ``ONEAPP_ADMIN_PASSWORD``
* ``ONEAPP_ADMIN_EMAIL``

Advanced Usage
==============

.. _wordpress_bootstrap:

Bootstrap
---------

Providing the bootstrapping values during instantiation of the appliance:

|image-bootstrap-values|

.. _wordpress_ssl:

SSL
---

If you provide SSL :ref:`contextualization <wordpress_context_param>` then your appliance will be reacheable over **https** too. There is no redirection http -> https - for that you must manually modify the **apache** config inside your ONE service appliance.

.. include:: shared/cert.txt

Filling up the the contextualization parameters with our self-signed certificate:

|image-ssl-context-values|

The browser will complain because of our self-signed certificate but you can add the exception for this site and it will function properly.

Let's Encrypt automation
~~~~~~~~~~~~~~~~~~~~~~~~

Not supported yet.

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
   [DB connection info]
   host     = localhost
   database = wordpress
   
   [DB root credentials]
   username = root
   password = LNSjsubElGShuVnp
   
   [DB wordpress credentials]
   username = wordpress
   password = 6NpcbOCIsPWv5I4C
   
   [Wordpress]
   site_url = 192.168.122.10
   username = osp
   password = m7w5Q3MzU

.. |image-download| image:: /images/wordpress/wordpress-download.png
.. |image-ssl-context-values| image:: /images/wordpress/wordpress-ssl-context-values.png
.. |image-bootstrap-values| image:: /images/wordpress/wordpress-bootstrap-values.png
.. |image-context-vars| image:: /images/wordpress/wordpress-context-vars.png
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
