.. _service_wp:

=================
Service WordPress
=================

General service description.

.. include:: shared/general.txt

Quick Start
===========

Sunstone and/or CLI.

Contextualization Parameters
============================

Table of config/bootstrap contextualization parameters

===================================== ============= ===========
Parameter                             Stage         Description
===================================== ============= ===========
``ONEAPP_SITE_HOSTNAME``              config        Fully qualified domain name or IP
``ONEAPP_SSL_CERT``                   config        SSL certificate
``ONEAPP_SSL_PRIVKEY``                config        SSL private key
``ONEAPP_SSL_CHAIN``                  config        SSL CA chain
``ONEAPP_SITE_TITLE``                 bootstrap     Site Title
``ONEAPP_ADMIN_USERNAME``             bootstrap     Site Administrator Login
``ONEAPP_ADMIN_PASSWORD``             bootstrap     Site Administrator Password
``ONEAPP_ADMIN_EMAIL``                bootstrap     Site Administrator E-mail
===================================== ============= ===========

Mention limitations (all bootstrap must be set, etc.)

Advanced Usage
==============

SSL
---

Mention how is it with HTTP to HTTPS redirection.

.. include:: shared/cert.txt

Let's Encrypt
~~~~~~~~~~~~~

Not supported for now. Better to explicitly mention that, so the users know what they are on.

Bootstrap
---------

Site title, Admin name/password/e-mail.
