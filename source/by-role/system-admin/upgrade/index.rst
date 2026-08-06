:is-up-to-date: True
:last-updated: 4.5.0

.. meta::
   :description: Complete guide to upgrading CrafterCMS on servers, Docker, Kubernetes, and clusters, including search migration to OpenSearch and MongoDB upgrades.
   :keywords: CrafterCMS upgrade, upgrade guide, server upgrade, Docker upgrade, Kubernetes upgrade, OpenSearch migration, MongoDB upgrade, cluster upgrade

.. _upgrading-craftercms:

====================
Upgrading CrafterCMS
====================
This section details the steps required to upgrade your CrafterCMS install.
The following CrafterCMS versions are able to upgrade to the latest release:

.. list-table::

    * - **4.5**
      - **4.4**
      - **4.3**
      - **4.2**
      - **4.1**
      - **4.0**
      - **3.1**
    * - All
      - All
      - All
      - All
      - All
      - All
      - ``3.1.9``
    * -
      -
      -
      -
      -
      -
      - ``3.1.12``
    * -
      -
      -
      -
      -
      -
      - ``3.1.13``
    * -
      -
      -
      -
      -
      -
      - ``3.1.17`` and onward

If you are upgrading from a CrafterCMS 3.1.x version other than the ones listed above, you will need to upgrade to one of the above listed supported 3.1.x upgrade paths release version first before upgrading. See the Upgrading CrafterCMS page for 3.1 here: :docs_base_url:`Upgrading CrafterCMS <3.1/system-administrators/upgrade/index.html>` for more information.

|

.. WARNING::
   * This guide assumes that you're trying to upgrade from a stock Crafter Studio version with some slight Studio configuration changes. If your project/site configuration is heavily customized or your Studio is a custom overlay you might need additional work that is not specified here.

|

---------------------
Verifying the Upgrade
---------------------
.. important::
    CrafterCMS will attempt to upgrade your project (site), which includes your configuration, code, and other assets. After the upgrade, perform a `git diff` on your project to see exactly what changed. This will help you identify any undesired upgrades that the system made and revert them if needed.

|

----------------------------
Upgrade by Installation Type
----------------------------
Here are the instructions for upgrading CrafterCMS based on how it was installed (on a server or via Kubernetes/Docker Compose):

.. important::

   Remember to read the release notes for the version you're upgrading to and all other release notes in between the
   version you currently have and the version you are upgrading to, as it contains information on all the changes that
   have occurred and steps you might have to take when upgrading to a specific version.


.. toctree::
   :maxdepth: 1
   :titlesonly:

   upgrading-server.rst
   upgrading-container.rst
   upgrading-cluster.rst

|hr|

.. _upgrade-notes-for-craftercms-4.5.1:

----------------------------------
Upgrade Notes for CrafterCMS 4.5.1
----------------------------------
Before upgrading to CrafterCMS version ``4.5.1``, if you have users born in CrafterCMS version ``3.x``, verify if there's a foreign key name containing ``FK`` in your installation by logging in to the database and running the following:

.. code-block:: bash
    :caption: *Verify if a foreign key name containing FK exists before upgrading*

    MariaDB [crafter]> SELECT TABLE_NAME, CONSTRAINT_NAME, REFERENCED_TABLE_NAME
    ->         FROM INFORMATION_SCHEMA.REFERENTIAL_CONSTRAINTS
    ->         WHERE CONSTRAINT_SCHEMA = DATABASE()
    ->             AND TABLE_NAME = 'group'
    ->             AND REFERENCED_TABLE_NAME = 'organization';
    +------------+-----------------+-----------------------+
    | TABLE_NAME | CONSTRAINT_NAME | REFERENCED_TABLE_NAME |
    +------------+-----------------+-----------------------+
    | group      | group_ibfk_1    | organization          |
    +------------+-----------------+-----------------------+

If the query returns any foreign key name (CONSTRAINT_NAME) that isn't exactly `group_ix_org_id`, then you need to drop the foreign key name containing ``FK``.

Update the query to match `group_ibfk_1` or any other foreign key name value returned from the query above:

.. code-block:: bash
    :caption: *Drop the constraint name returned from the previous query*

    ALTER TABLE `group` DROP FOREIGN KEY `group_ibfk_1`;

You may now proceed to upgrade your installation by following the instructions listed above for upgrading based on your installation type.

----------------
Upgrading Search
----------------

Starting version 4.1.0, CrafterCMS now uses OpenSearch instead of Elasticsearch. The reasons for this change are legal rather than technical given the new licensing of Elasticsearch. Please follow the article :ref:`upgrading-search` to learn all about upgrading your search from Elasticsearch to OpenSearch.

.. toctree::
    :hidden:

    upgrading-search

|hr|

.. _upgrading-mongodb:

-----------------
Upgrading MongoDB
-----------------
.. note::
    The following section only applies to Crafter Profile and Social installs.

Crafter Profile and Social version 4.1.0 and later uses MongoDB v6 in the install.
When upgrading your 3.1.x install of Crafter Profile and Social to 4.1.0 and later, MongoDB will need to be manually upgraded.

This section describes how to upgrade MongoDB in your Crafter Profile and Social version 3.1.x installed on a server.

Please read through the upgrade instructions :ref:`here <upgrading-craftercms-on-a-server>` first.
The steps for upgrading MongoDB in your Crafter Profile and Social install follows almost exactly
the same steps as listed in that document.

.. important::
    Remember to **manually shut down and backup CrafterCMS** before beginning your upgrades!

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Upgrading 3.1.x -> 4.1.x (from MongoDB 3.x)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
#. Download CrafterCMS version 4.1.x, and extract the files.
#. Run the ``upgrade-search.sh`` script from your newly extracted files.

   .. code-block:: bash
       :caption: *Run the upgrade-search script*
       :emphasize-lines: 1

       > ./upgrade-search.sh /path/of/install/to/be/upgraded --stay-alive
       ========================================================================
       Search upgrade started
       ========================================================================
       Downloading Elasticsearch 7 from https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.0-linux-x86_64.tar.gz
       ...
       All indices reindex complete
       Reindex finished in 12.843 seconds
       'stay-alive' flag on, hit enter to stop Elasticsearch
       >
       End process. Stop Elasticsearch
       Move indexes from 'data/indexes-es' to 'indexes'
       ========================================================================
       Search upgrade completed
       ========================================================================

   |

   See :ref:`upgrading-search` for more information on upgrading search.

#. Upgrade using the ``upgrade-target.sh`` script from your newly extracted files. First, we'll need to set ``MONGODB_HOME`` so that the backup script will run with the ``mongod`` version ``3.4.4`` in the old installation before running the ``upgrade-target.sh``:

   .. code-block:: bash
       :caption: *Set MONGODB_HOME and Run the upgrade-target script from the newly extracted files*

       > MONGODB_HOME=/path/of/3.1.x/install/bin/mongodb ./upgrade-target.sh /path/of/install/to/be/upgraded
       ...
       [o] Overwriting file apache-tomcat/LICENSE with the new release version
       [o] Overwriting file apache-tomcat/BUILDING.txt with the new release version
       ========================================================================
       Upgrade completed
       ========================================================================
       !!! Please read the release notes and make any necessary manual changes, then run the post upgrade script:

  |

#. Manually upgrade MongoDB from version ``3.4.4`` to version ``6.0.12``.
   We need to upgrade from each release version and run the ``setFeatureCompatibilityVersion`` command against the admin database.

   Please see https://www.mongodb.com/docs/manual/release-notes/6.0-upgrade-standalone/ for more information on upgrading MongoDB.

   Download and extract the following MongoDB versions available from https://www.mongodb.com/try/download/community:

   - 3.6.23 (http://downloads.mongodb.org/linux/mongodb-linux-x86_64-3.6.0.tgz)
   - 4.0.28 (http://downloads.mongodb.org/linux/mongodb-linux-x86_64-4.0.28.tgz)
   - 4.2.25 (https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu1804-4.2.25.tgz)
   - 4.4.26 (https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu2004-4.4.26.tgz)
   - 5.0.23 (https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu2004-5.0.23.tgz)
   - 6.0.12 (https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu2204-6.0.12.tgz)

   Run ``mongod`` from each release version ``/extracted/location/of/mongodb-version-to-be-upgraded-to/bin/mongod`` starting from the oldest one:

   .. code-block:: bash
       :caption: *Run mongod from MongoDB version to be upgraded to*
       :emphasize-lines: 9

       $ /extracted/location/of/mongodb-linux-x86_64-3.6.23/bin/mongod --dbpath=/path/of/3.1.x/install/data/mongodb --directoryperdb --journal --port 27020
       ...
       2023-12-20T02:38:20.546-0500 I CONTROL  [initandlisten] db version v3.6.23
       2023-12-20T12-03T02:38:20.546-0500 I CONTROL  [initandlisten] git version: d352e6a4764659e0d0350ce77279de3c1f243e5c
       2023-12-20T02:38:20.546-0500 I CONTROL  [initandlisten] OpenSSL version: OpenSSL 1.1.1g  21 Apr 2020
       2023-12-20T02:38:20.546-0500 I CONTROL  [initandlisten] allocator: system
       ...
       2023-12-20T02:38:21.687-0500 I NETWORK  [initandlisten] listening via socket bound to /tmp/mongodb-27020.sock
       2023-12-20T02:38:21.687-0500 I NETWORK  [initandlisten] waiting for connections on port 27020
       2023-12-20T02:38:21.687-0500 I STORAGE  [LogicalSessionCacheRefresh] createCollection: config.system.sessions with generated UUID: fee195e0-b337-498f-8ad9-1c410748bce1

   |

   Then login as user admin in mongoDB in your 3.1.x install and set the compatibility version using the command
   ``db.adminCommand( { setFeatureCompatibilityVersion: "{version}" } )``

   .. code-block:: bash
       :caption: *Set compatibility version from /path/of/3.1.x/install/bin/mongodb/bin/*
       :emphasize-lines: 1,7

       > ./mongosh mongodb://localhost:27020/admin
       Current Mongosh Log ID:	659511008267cb24aff1427e
       Connecting to:		mongodb://localhost:27020/admin?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+1.8.0
       Using MongoDB:		3.6.23
       Using Mongosh:		1.8.0

       admin> db.adminCommand( { setFeatureCompatibilityVersion: "3.6" } )
       { ok: 1 }
       admin>

   |

   Repeat the steps above for all the other versions of MongoDB downloaded in ascending order of version, e.g.
   run ``mongod`` version ``4.0.28``, then set the feature compatibility version to ``4.0``.  Next run
   ``mongod`` version ``4.2.25``, then set the feature compatibility version to ``4.2``, and so on and so forth

#. Before starting CrafterCMS, you'll need to configure the installation root directory to use Java version 17.  Remember to read the release notes or any relevant upgrade articles and make any necessary manual changes before running the `post-upgrade.sh`` script as described next

#. Run the ``post-upgrade.sh`` script. This will:

   - Remove old *data/indexes-es* directory (old indexes are not usable by OpenSearch)
   - Start CrafterCMS and ask for signal to continue
   - Once started and CrafterCMS is up (including UM execution), let the post-upgrade continue by typing ``Y``:

     .. code-block:: bash

         Please make sure Crafter has started successfully before continuing
         > Continue? [(Y)es/(N)o]:

   - Post-upgrade will continue to trigger the reindex of all targets by calling the Deployer API ``/api/1/target/deploy-all``

#. Your install is now upgraded and you may login to Studio to check the version.

|hr|

------------------------------
Troubleshooting Upgrade Issues
------------------------------
When upgrading your Studio install fails, the first thing to do is to inspect data from the logs and figure out which statement failed when completing the upgrade. Using the results of the inspection, we then have to recover the database manually.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Failure Executing a Statement
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Let's take a look at an example log:

.. code-block:: bash
    :caption: Failed statement during upgrade
    :linenos:

    Caused by: org.craftercms.studio.api.v2.exception.UpgradeException: Error executing sql script upgrade-3.1.0.6-to-3.1.0.7.sql
    	at org.craftercms.studio.impl.v2.upgrade.operations.DbScriptUpgradeOperation.execute(DbScriptUpgradeOperation.java:122)
    	at org.craftercms.studio.impl.v2.upgrade.pipeline.DefaultUpgradePipelineImpl.execute(DefaultUpgradePipelineImpl.java:67)
    	at org.craftercms.studio.api.v2.upgrade.UpgradePipeline.execute(UpgradePipeline.java:42)
    	at org.craftercms.studio.impl.v2.upgrade.DefaultUpgradeManagerImpl.upgradeDatabaseAndConfiguration(DefaultUpgradeManagerImpl.java:87)
    	at org.craftercms.studio.impl.v2.upgrade.DefaultUpgradeManagerImpl.init(DefaultUpgradeManagerImpl.java:182)
    	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    	at java.lang.reflect.Method.invoke(Method.java:498)
    	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeCustomInitMethod(AbstractAutowireCapableBeanFactory.java:1763)
    	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1700)
    	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1630)
    	... 45 more
    Caused by: org.apache.ibatis.jdbc.RuntimeSqlException: Error executing: ALTER TABLE `cluster` ADD UNIQUE IF NOT EXISTS `uq_cl_git_remote_name` (`git_remote_name`). Cause: java.sql.SQLIntegrityConstraintViolationException: (conn:12) Duplicate entry '' for key 'uq_cl_git_remote_name'

|

Looking at the log above, we can see that the upgrade failed when trying to execute a statement because of a duplicate entry: `Error executing: ALTER TABLE \`cluster\` ADD UNIQUE IF NOT EXISTS \`uq_cl_git_remote_name\` (\`git_remote_name\`)`

Here, the upgrade failed because of a special case with existing data in a table that needed to be changed as part of the upgrade.

For this case, the solution would be to manually set unique values for the existing rows in the table and then execute the remaining statements in the script.

|hr|

^^^^^^^^^^^^^^^^^^^^^
Upgrade Not Supported
^^^^^^^^^^^^^^^^^^^^^
When the system is in an undefined state between two versions, you may see the following message in the logs:

.. code-block:: text
    :caption: System in an undefined state between two versions

    Caused by: org.craftercms.studio.api.v2.exception.UpgradeNotSupportedException: The current database version can't be upgraded
	    at org.craftercms.studio.impl.v2.upgrade.operations.DbScriptUpgradeOperation.execute(DbScriptUpgradeOperation.java:105)
	    at org.craftercms.studio.impl.v2.upgrade.pipeline.DefaultUpgradePipelineImpl.execute(DefaultUpgradePipelineImpl.java:67)
	    at org.craftercms.studio.api.v2.upgrade.UpgradePipeline.execute(UpgradePipeline.java:42)

|

The above message may occur, if you had an error upgrading because of a failed statement, then you restarted the system again. Because the script was not executed completely, the system is in an undefined state between the two versions. So, the next time the system is restarted, the upgrade will be attempted again resulting in a different message in the logs as seen above. The solution here would be to look some more in the logs for the failed statement before the system was restarted and manually recover, like the previous example.

|hr|

.. _db-upgrades-timeout:

^^^^^^^^^^^^^^^^^^^
DB Upgrades Timeout
^^^^^^^^^^^^^^^^^^^
On start, Crafter Studio will run its Upgrade Manager. When migrating large sites
with a large content base, some DB operations can timeout and throw an error like the following:

.. code-block:: text
    :caption: System in an undefined state between two versions

    Caused by: java.lang.NullPointerException: Cannot read field "lock" because "this.connection" is null
	    at org.mariadb.jdbc.MariaDbProcedureStatement.execute(MariaDbProcedureStatement.java:174) ~[mariadb-java-client-2.7.4.jar:?]
	    at org.apache.commons.dbcp2.DelegatingPreparedStatement.execute(DelegatingPreparedStatement.java:94) ~[commons-dbcp2-2.9.0.jar:2.9.0]
	    at org.apache.commons.dbcp2.DelegatingPreparedStatement.execute(DelegatingPreparedStatement.java:94) ~[commons-dbcp2-2.9.0.jar:2.9.0]
	    at org.craftercms.studio.impl.v2.upgrade.operations.db.PopulateItemTableUpgradeOperation.populateDataFromDB(PopulateItemTableUpgradeOperation.java:213) ~[classes/:4.1.0-SNAPSHOT]
	    at org.craftercms.studio.impl.v2.upgrade.operations.db.PopulateItemTableUpgradeOperation.processSite(PopulateItemTableUpgradeOperation.java:179) ~[classes/:4.1.0-SNAPSHOT]
	    at org.craftercms.studio.impl.v2.upgrade.operations.db.PopulateItemTableUpgradeOperation.doExecute(PopulateItemTableUpgradeOperation.java:156) ~[classes/:4.1.0-SNAPSHOT]
	    at org.craftercms.studio.impl.v2.upgrade.operations.AbstractUpgradeOperation.doExecute(AbstractUpgradeOperation.java:103) ~[classes/:4.1.0-SNAPSHOT]
	    at org.craftercms.commons.upgrade.impl.operations.AbstractUpgradeOperation.execute(AbstractUpgradeOperation.java:97) ~[crafter-commons-upgrade-manager-4.1.0-SNAPSHOT.jar:4.1.0-SNAPSHOT]
	    at org.craftercms.commons.upgrade.impl.pipeline.DefaultUpgradePipelineImpl.execute(DefaultUpgradePipelineImpl.java:82) ~[crafter-commons-upgrade-manager-4.1.0-SNAPSHOT.jar:4.1.0-SNAPSHOT]
	    at org.craftercms.studio.impl.v2.upgrade.StudioUpgradeManagerImpl.upgradeDatabaseAndConfiguration(StudioUpgradeManagerImpl.java:122) ~[classes/:4.1.0-SNAPSHOT]
	    at org.craftercms.studio.impl.v2.upgrade.StudioUpgradeManagerImpl.startUpgrade(StudioUpgradeManagerImpl.java:285) ~[classes/:4.1.0-SNAPSHOT]
	    at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:?]

|

To prevent the error, the environment variable `MARIADB_SOCKET_TIMEOUT <https://github.com/craftersoftware/craftercms/blob/support/4.x/craftercms/resources/env/authoring/bin/crafter-setenv.sh#L100>`__ in the ``{Crafter-CMS-install-directory}/bin/crafter-setenv.sh`` file of the CrafterCMS install you're running the upgrade script from may need to be increased depending on the size of the existing sites.

Here's an example of setting the timeout to 10 hours:

.. code-block:: sh
    :caption: *{Crafter-CMS-install-directory}/bin/crafter-setenv.sh*

    # Setting timeout to 10h = 3600 * 10 * 1000ms
    export MARIADB_SOCKET_TIMEOUT=${MARIADB_SOCKET_TIMEOUT:="36000000"}

|
