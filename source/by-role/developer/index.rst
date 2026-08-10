:is-up-to-date: True
:last-updated: 4.2.2

.. meta::
   :description: CrafterCMS developer guide covering content modeling, headless and templated projects, composable CMS, DevContentOps, Groovy, GraphQL, and security.
   :keywords: CrafterCMS developer, content modeling, headless, FreeMarker, Groovy, GraphQL, composable, DevContentOps, Crafter Engine

.. _developer:

=========
Developer
=========
This section focuses on how to build content rich applications backed by CrafterCMS. It covers
the basic topics you need to define a content type and render that content as part of a page,
component or service. It also covers topics like building queries, accessing services and extending
the core platforms and the developer workflow (DevContentOps).

Developers need to be comfortable with Content Modeling (how to represent content in CrafterCMS
for your application to retrieve said content), and how to access content items from your application/project.

.. note::
    For local development, you'll need to install CrafterCMS locally and that's best done via the local bundle. Instructions on how to do that are in :ref:`install-craftercms-via-binary-bundles`. Other options include running CrafterCMS in :ref:`Docker <local-dev-with-docker>` or installing CrafterCMS `from source <https://github.com/craftersoftware/craftercms>`_.


CrafterCMS supports both headless and templated projects (templated projects are the traditional web sites with a view layer).

Regardless of type of project, a developer needs to be familiar with the following core concepts.

-------------
Core Concepts
-------------
.. list-table:: Developer Core Concepts
    :header-rows: 1

    * - Topic
      - Description
    * - :ref:`content-modeling`
      - Provides you information on giving structure and organization to your content
    * - :ref:`information-architecture`
      - Shows you the CrafterCMS repository structure
    * - :ref:`content-inheritance`
      - Allows you to augment or override existing content using inheritance
    * - :ref:`content-retrieval-apis`
      - Provides you information on how to access your content via APIs
    * - :ref:`static-content-access`
      - Provides you information on how to access static contents such as images, CSS files, etc.
    * - :ref:`experience-builder`
      - Provides information on the UI layer on top of your applications that enables authors with in-context editing (ICE)
    * - :ref:`Server-side Development with Groovy <groovy-java-api>`
      - Provides you information on how to use Groovy to develop server-side services and applications
    * - :ref:`project-security`
      - Provides you information on security features that can be used to secure your project/site
    * - :ref:`content-search`
      - Provides you information on how to perform content queries

.. toctree::
    :maxdepth: 1
    :titlesonly:
    :hidden:

    common/content-modeling/content-modeling
    common/content-modeling/information-architecture
    common/content-modeling/content-inheritance
    common/content-access
    common/static-content-access
    common/experience-builder
    common/security

-------------------
Additional Concepts
-------------------
Aside from the core concepts listed above, a developer needs to be familiar with the following additional general concepts.

.. list-table:: Developer General Concepts
    :header-rows: 1

    * - Topic
      - Description
    * - :ref:`scheduled-jobs`
      - Provides you information on scheduling jobs
    * - :ref:`page-and-component-controllers`
      - Provides you information on page and component controllers
    * - :ref:`targeting`
      - Provides you information on rendering content adapted to different things
    * - :ref:`servlet-filters`
      - Provides you information on creating and configuring a filter
    * - :ref:`working-with-dates-in-groovy`
      - Provides you information on performing date operations in Groovy
    * - :ref:`engine-url-rewrite-configuration`
      - Provides you information on performing redirects (send users from one URL to another URL)
    * - :ref:`working-with-sass`
      - Provides you information on working with Sass in CrafterCMS
    * - :ref:`multi-environment-support`
      - Provides you information on multi-environment support
    * - :ref:`custom-error-pages`
      - Provides you information on creating custom error pages
    * - :ref:`adding-a-new-language`
      - Provides you information on adding a new language to Studio
    * - :ref:`localization`
      - Provides you information on localizing content
    * - :ref:`developer-environment-variable-access`
      - Provides you information on accessing custom environment variables
    * - :ref:`adding-a-default-screenshot-to-a-project`
      - Provides you information on adding a default screenshot to a project

.. toctree::
    :maxdepth: 1
    :titlesonly:
    :hidden:

    common/scheduled-jobs
    common/page-and-component-controllers
    common/targeting
    common/servlet-filters
    common/working-with-dates-groovy
    common/working-with-sass
    ../common/multi-environment-support
    common/custom-error-pages
    common/adding-a-new-language
    common/localization
    common/environment-variable-access
    common/add-default-screenshot

.. _developer-headless:

--------
Headless
--------
CrafterCMS is natively headless. CrafterCMS simply retrieves content that the developer models in the
modeling tool, applies actions/rules like inheritance, security, versioning, etc. and returns the
transformed content to the caller as JSON. Developers can use whatever front-end technology they want including, but not limited to: React, Vue, Angular, Flutter
or similar SPAs, Android and iOS applications, etc.

^^^^^^^^^^^^^^^^^
Headless Examples
^^^^^^^^^^^^^^^^^
- `Video Center Blueprint <https://github.com/craftercms/video-center-blueprint>`_
- `Headless Store Blueprint <https://github.com/craftersoftware/craftercms/tree/support/4.x/studio/src/main/webapp/repo-bootstrap/global/blueprints/2000_headless_store>`_
- `Headless Blog Blueprint <https://github.com/craftersoftware/craftercms/tree/support/4.x/studio/src/main/webapp/repo-bootstrap/global/blueprints/5000_headless_blog>`_

---------
Templated
---------
CrafterCMS embeds FreeMarker to provide a high-performance, clean, flexible, and tolerant of syntax
variance, templating engine to render HTML directly from CrafterCMS.

It allows developers to model the content as general reusable items, and fold those into pages.
Pages aggregate content from components as needed and are associated with a FreeMarker template that
can render the final page. The choice of HTML tools and frameworks doesn't matter to CrafterCMS.

The article, :ref:`templating`, provides more details on CrafterCMS provided tools for creating your templated project.

.. toctree::
    :maxdepth: 1
    :hidden:
    :titlesonly:

    templated/templating


^^^^^^^^^^^^^^^^^^
Templated Examples
^^^^^^^^^^^^^^^^^^
- `Editorial Blueprint <https://github.com/craftersoftware/craftercms/tree/support/4.x/studio/src/main/webapp/repo-bootstrap/global/blueprints/1000_website_editorial>`_
- `commerceTools Blueprint <https://github.com/craftercms/commercetools-blueprint>`_
- `Wordify Blueprint <https://github.com/craftercms/wordify-blueprint>`_

----------
Composable
----------
CrafterCMS is a composable CMS and provides a modular and flexible approach to building and managing websites and digital experiences. Unlike traditional monolithic CMSs, which come with pre-built features and a fixed structure, CrafterCMS allows users to assemble and configure their own system by choosing and integrating individual components or services.

The article, :ref:`composable`, provides more details on the composable nature of CrafterCMS and how to take advantage of it.

.. toctree::
    :hidden:

    composable/index


-------------
DevContentOps
-------------
DevContentOps is a set of tools and processes that allow teams of software developers, content authors, and system administrators to work together on a single
project across multiple environments. These tools and processes are fully described in :ref:`devcontentops`.

Studio is a great tool for content authors and for quick changes, but for development, you'll want to use your IDE. Learn how to use your IDE with Studio in the :ref:`working-in-your-ide` article.

.. toctree::
    :hidden:

    devcontentops
    common/working-in-your-ide

-------
Upgrade
-------
Upgrading CrafterCMS from a developer's perspective is covered in :ref:`developers-upgrade`.

.. toctree::
    :hidden:

    upgrade/index


----------------
Managing Secrets
----------------
Credentials may be required in some cases when accessing content. For more information on how to manage/encode your secrets such as
AWS credentials, please see :ref:`managing-secrets`.

.. toctree::
    :hidden:

    ../system-admin/security/index

-------
Logging
-------
While developing your project, there are times when you’d like to see more log details, to figure out what
is happening and address it as needed. Overriding the logging levels allows you to see more or less
details depending on your needs.

Please review the :ref:`logging` article for more information.

.. toctree::
    :hidden:

    ../common/logging

|

|hr|

--------------
More Resources
--------------
Below is a short set of links to other documentation for technology that CrafterCMS leverages:

+----------------+------------------------------+-----------------------------------------------+
| Attribute Name |             Usage            | Links                                         |
+================+==============================+===============================================+
|| Spring Beans  || Server App Framework        || https://docs.spring.io/spring-framework      |
|| Spring MVC    ||                             ||                                              |
+----------------+------------------------------+-----------------------------------------------+
|| Freemarker    || Server side templates       || http://freemarker.org/                       |
||               ||                             ||                                              |
+----------------+------------------------------+-----------------------------------------------+
|| Groovy        || Server side scripting       || http://groovy-lang.org/documentation.html    |
||               ||                             ||                                              |
+----------------+------------------------------+-----------------------------------------------+
|| OpenSearch    || Search and content query    || https://opensearch.org/docs/latest/          |
||               ||                             ||                                              |
+----------------+------------------------------+-----------------------------------------------+
|| MongoDB       || Crafter Profile and Social  || https://docs.mongodb.com                     |
||               || data store                  ||                                              |
+----------------+------------------------------+-----------------------------------------------+
|| GIT           || Repository Implementation   || https://git-scm.com/docs                     |
+----------------+------------------------------+-----------------------------------------------+
|| RST           || Documentation source format || http://docutils.sourceforge.net/rst.html     |
||               ||                             ||                                              |
+----------------+------------------------------+-----------------------------------------------+

.. note::
   CrafterCMS is a Git-based CMS. Please note that **your system's global git ignore file (list of
   rules for ignoring files in every Git repository on your computer) is respected by CrafterCMS**.
   In case you find some of your content not being committed into CrafterCMS, check your global
   git ignore file to verify the content you're committing is not being ignored.


.. TODO Smaller topics that are needed but not sure how to represent/wrap these yet
    .. asset-processing
