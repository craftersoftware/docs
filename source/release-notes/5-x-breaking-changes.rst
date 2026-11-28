:is-up-to-date: True
:last-updated: 5.0.0
:orphan:

.. _breaking-changes-5-x:

================================
Breaking Changes in CrafterCMS 5
================================
This section covers changes that might affect your CrafterCMS projects, as well as other considerations
before upgrading. Please review the following and apply changes as required.

|hr|

.. _breaking-changes-in-craftercms-5-0:

----------------------------------
Breaking Changes in CrafterCMS 5.0
----------------------------------
.. version_tag::
    :label: Since
    :version: 5.0.0

.. important::

    **Action required if Studio uses an external database.** External databases for Crafter Studio are deprecated and unsupported. After upgrading, Studio won't start against an external database until you add ``studio.db.allowExternal: true`` to ``studio-config-override.yaml``. Studio then starts and logs a deprecation warning at every start. Embedded-database installs need no change. Setting ``MARIADB_HOST`` to a remote host still works in this release but logs a warning. Use ``CRAFTER_DEPRECATED_EXTERNAL_DB_HOST`` instead. Both the setting and the env vars will be removed in a future release (`#9536 <https://github.com/craftersoftware/craftercms/issues/9536>`__). Plan your migration to the embedded database.

Crafter Studio uses its own embedded MariaDB. The default in ``studio-config.yaml`` is ``studio.db.allowExternal: false``.
The property is YAML only. It is not mapped from an environment variable, so a Docker or Kubernetes install has to supply
``studio-config-override.yaml`` (a bind mount, or a ConfigMap). Crafter Engine is unaffected.

Studio checks ``studio.db.url``, ``studio.db.scriptRunnerUrl``, and ``studio.db.initializer.url``. A URL is external
when its host is not ``127.0.0.1``, ``localhost``, or ``::1``, when its port is not ``studio.db.port``, or when the URL
cannot be parsed. An unparseable URL fails closed. Without the opt-in, Studio logs one ERROR that names the property
and ``studio-config-override.yaml``, then fails to start. With the opt-in, Studio logs one deprecation warning at every
start.

The shipped bundle and Kubernetes override files no longer include the commented AWS RDS example
(``studio.db.initializer.url: ${studio.db.url}``). An existing override that still points those URLs at a remote
database, including AWS RDS, is subject to this check.

See :ref:`studio-external-database` for the opt-in on a binary install, Docker, and Kubernetes, the
``CRAFTER_DEPRECATED_EXTERNAL_DB_HOST`` and ``MARIADB_HOST`` behavior, and the warning text printed at startup.
