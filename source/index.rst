:is-up-to-date: True
:last-updated: 5.0.0

########################
CrafterCMS Documentation
########################

.. default-domain:: craftercms
.. meta::
    :description lang=en:
        Home page for the official documentation for CrafterCMS.


This documentation introduces key concepts in CrafterCMS, details the decoupled authoring and development systems, and provides operational and administrative considerations and procedures as well as a comprehensive reference section.

==================
What is CrafterCMS
==================
CrafterCMS is a modern content management platform for building content-centric digital experience applications including:

* Enterprise websites, corporate intranets, customer portals
* Single Page Applications (SPAs) using frameworks like React, Vue, and Angular
* Native mobile apps and headless applications (consumer mobile, IOT, digital signage, wearables, etc.)
* HTML5 websites (Freemarker templating built-in)
* E-Commerce front-ends
* OTT video experiences on AWS Elemental Media Services
* E-Learning sites
* AR/VR applications (e.g., using A-Frame)
* Search-driven sites and apps (OpenSearch built-in)
* AI-enabled applications
* and lots more (See https://craftercms.com/headless-cms-solutions/enterprise).

CrafterCMS's authoring system is built on Git and supports DevContentOps processes for content, code, and configuration. It is also a hybrid-headless, API-first (GraphQL, REST, in-process) CMS, and that allows developers to use their favorite UI frameworks and tools.

CrafterCMS differentiates itself from existing CMSs with its architecture: microservices-based, serverless, elastic and planet-wide scalability. To learn more, see the :ref:`general-architecture`.

|hr|

============================
When/where to use CrafterCMS
============================
CrafterCMS is best suited for:

* Large-scale sites and apps that require a high degree of performance, security, availability, and scalability.
* Content-rich applications that are authored by a non-technical content authors and consumed by many, many site visitors.
* Enterprise-grade, complex, and integration-rich applications.

|hr|

===================
Who uses CrafterCMS
===================
CrafterCMS is used by three main constituencies:

* Developers who build applications using CrafterCMS.
* Authors who create, manage and publish content using CrafterCMS.
* DevOps who deploy and manage CrafterCMS.

.. toctree::
    :hidden:

    getting-started/index
    by-role/index
    reference/index
    security/index
    accessibility
    roadmap
    support
    release-notes/index
    contribute/index
    faq

|hr|

=========================
Latest CrafterCMS Release
=========================
The latest version of CrafterCMS is `version 5.0.0 <https://craftercms.com/download>`__ and the latest
enterprise version is ``5.0.0E``.

Start exploring the latest version of CrafterCMS in a few minutes using Docker by simply running the following command:

.. code-block:: bash

    docker run -p 8080:8080 craftercms/authoring_local:latest

Just point your browser to ``http://localhost:8080/studio`` and it's ready for you to use!
The default username is ``admin`` and the default password is ``admin``.

See :ref:`getting-started` for more information on getting started quickly on using CrafterCMS.

For more information on the release, see the :ref:`release-notes`.
