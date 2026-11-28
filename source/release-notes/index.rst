:is-up-to-date: True
:last-updated: 5.0.0

.. meta::
    :description lang=en:
        List of CrafterCMS release notes. The latest version of CrafterCMS (CMS) is listed below.

.. _release-notes:

=============
Release Notes
=============
This section contains release notes for CrafterCMS version 4. For other version release notes, please visit your
version release's release notes page :ref:`below <other-release-notes>`

.. note:: CrafterCMS requires Java 21

.. important::

    **Action required if Studio uses an external database** when upgrading to CrafterCMS 5.
    External databases for Crafter Studio are deprecated and unsupported. After upgrading, Studio won't start
    against an external database until you add ``studio.db.allowExternal: true`` to ``studio-config-override.yaml``.
    Studio then starts and logs a deprecation warning at every start. Embedded-database installs need no change.
    Setting ``MARIADB_HOST`` to a remote host still works in this release but logs a warning. Use
    ``CRAFTER_DEPRECATED_EXTERNAL_DB_HOST`` instead. Both the setting and the env vars will be removed in a future
    release (`#9536 <https://github.com/craftersoftware/craftercms/issues/9536>`__). Plan your migration to the
    embedded database.

    See :ref:`breaking changes in CrafterCMS 5 <breaking-changes-5-x>` and :ref:`studio-external-database`.

.. include:: /release-notes/4-7-1.rst
.. include:: /release-notes/4-7-0.rst
.. include:: /release-notes/4-6-1.rst
.. include:: /release-notes/4-6-0.rst
.. include:: /release-notes/4-5-2.rst
.. include:: /release-notes/4-5-1.rst
.. include:: /release-notes/4-5-0.rst
.. include:: /release-notes/4-4-8e.rst
.. include:: /release-notes/4-4-7e.rst
.. include:: /release-notes/4-4-6e.rst
.. include:: /release-notes/4-4-5e.rst
.. include:: /release-notes/4-4-4.rst
.. include:: /release-notes/4-4-3.rst
.. include:: /release-notes/4-4-2.rst
.. include:: /release-notes/4-4-1.rst
.. include:: /release-notes/4-4-0.rst
.. include:: /release-notes/4-3-2.rst
.. include:: /release-notes/4-3-1.rst
.. include:: /release-notes/4-3-0.rst
.. include:: /release-notes/4-2-2.rst
.. include:: /release-notes/4-2-0.rst

.. _other-release-notes:

For CrafterCMS version 4.1 release notes, see :docs_base_url:`4.1 Release Notes <4.1/release-notes/index.html>`.

|hr|

For CrafterCMS version 4.0 release notes, see :docs_base_url:`4.0 Release Notes <4.0/release-notes/index.html>`.

|hr|

For CrafterCMS version 3.1 release notes, see :docs_base_url:`3.1 Release Notes <3.1/release-notes/index.html>`.

