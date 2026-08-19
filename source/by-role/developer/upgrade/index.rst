:is-up-to-date: True
:last-updated: 4.1.0
:orphan:

.. meta::
   :description: Developer guide for upgrading CrafterCMS with notes on features, configuration, and migration steps.
   :keywords: upgrade, CrafterCMS, version upgrade, developer upgrade, migration

.. _developers-upgrade:

=======
Upgrade
=======
Each new version of CrafterCMS brings new features and/or improvements to one or more of the following: performance,
security, UI, configuration, and more.
Upgrading your CrafterCMS install allows you to take advantage of these new features/improvements.

Upgrading CrafterCMS may vary based on how it was installed (on a server or via Kubernetes/Docker Compose).
See :ref:`upgrading-craftercms` for more information on upgrading your CrafterCMS install.

Remember to review the :ref:`release-notes` for the release you are upgrading to for additional instructions that may
need to be performed depending on the install you're upgrading..

Below are some things to note when upgrading:

---------------
From 4.0 to 4.1
---------------
Upgrading to CrafterCMS 4.1 from 4.0 is a simple process. The upgrade script will handle most of the changes for you.


.. _upgrade-search:

--------------
Upgrade Search
--------------
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Upgrade from Elasticsearch to OpenSearch
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Most of the upgrade to OpenSearch is transparent to the developer. CrafterCMS automatically updates the minor changes to the import statements, and for the most part this should work automatically.

The indexes may need to be regenerated, and the upgrade script will handle this for you.

When upgrading from 4.0.x (running ES 7) the indices are not compatible at all, so the content needs to be reprocessed and indices rebuilt completely, which is handled by the post-upgrade script, as mentioned above.

See :ref:`upgrading-search` for more information on upgrading your search from Elasticsearch to OpenSearch along with some manual updates that may be required in some instances.

--------------------------
Upgrade In-Context Editing
--------------------------
If upgrading from CrafterCMS 4.0 with Experience Builder (XB) already in your project/site, there is nothing to upgrade.

However, if upgrading from 3.0 or 3.1 In-Context Editing (ICE) or 4.0 with ICE in compatibility mode, then you may consider upgrading to XB. This is not required, but is recommended. To upgrade to XB, see this guide: :ref:`upgrading-in-context-editing`.