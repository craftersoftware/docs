:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
    :description lang=en:
        CrafterCMS documentation to help users get started with running CrafterCMS, and creating your sites whether it's headless or templated.

.. _getting-started:

===============
Getting Started
===============
This section lets you get started quickly on using CrafterCMS.

--------------
Run CrafterCMS
--------------
^^^^^^^^^^^^^^^^^^^^^^^^
Try CrafterCMS in Docker
^^^^^^^^^^^^^^^^^^^^^^^^
If you have `Docker <https://www.docker.com/>`_ installed, you can start exploring CrafterCMS locally in a few minutes. Simply run the following command:

.. code-block:: bash

    docker run -p 8080:8080 craftercms/authoring_local:latest

Then you can just point your browser to ``http://localhost:8080/studio`` and you will be presented with the Crafter Studio login page. The default username is ``admin`` and the default password is ``admin``.

If you are ready to start development, consult the :ref:`documentation for developers <developer>` for advice on environment setup.

For all published images and tags, see :ref:`docker-images`.

|hr|

^^^^^^^^^^^^^^^^^^^^^
Run CrafterCMS on AWS
^^^^^^^^^^^^^^^^^^^^^
You can use a `prebuilt AMI <https://aws.amazon.com/marketplace/seller-profile?id=6d75ffca-9630-44bd-90b4-ac0e99058995>`_ from the AWS marketplace.

|hr|

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Run CrafterCMS SaaS in the Cloud
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You can sign up for `Crafter Cloud <https://craftercms.com/products/crafter-cloud>`_.

|hr|

For more ways to install, please see the article :ref:`system-admin-installation`.

-------------------------------
Start Crafting Your Experiences
-------------------------------
CrafterCMS is an API-first CMS, and as such, it can be used as a headless CMS or a templated (traditional) CMS. The following sections will help you get started with both.

.. toctree::
   :maxdepth: 1

   your-first-project/headless.rst
   your-first-project/templated.rst

If you're ready to jump into serious development, then proceed to learn more about CrafterCMS by reviewing the documentation based on :ref:`your role or interest <by-role>`.
