:is-up-to-date: True
:last-updated: 4.1.1

.. index:: Upgrading Studio Cluster, Cluster Upgrade

.. meta::
   :description: Step-by-step guide to upgrading a CrafterCMS Studio cluster, covering backup, node upgrade order, and cluster-specific considerations.
   :keywords: Studio cluster upgrade, CrafterCMS upgrade, cluster nodes, backup before upgrade, enterprise upgrade, Crafter Studio, high availability

.. _upgrading-studio-cluster:

=========================================
Upgrading Studio Cluster |enterpriseOnly|
=========================================
This section details how to upgrade your Studio cluster.
Please read through the upgrade instructions :ref:`here <upgrading-craftercms-on-a-server>` first as
some of the steps are the same for upgrading your cluster.
To upgrade the cluster, a node will be upgraded and brought up as the primary, then the other node/s will be added.

------------
Upgrade Node
------------
The first step in upgrading your Studio cluster is to upgrade a node and startup your cluster with just the one
upgraded node.  Here are the steps for  upgrading a node:

#. Stop the Studio cluster
#. Do a backup of the primary and replica nodes using the ``crafter.sh`` script by running the following:

   .. code-block:: bash
       :caption: *Backup Studio by running the crafter.sh script*

       ./bin/crafter.sh backup

#. Download the CrafterCMS version you'd like to upgrade to, and extract the files on one of the nodes

#. Run the ``upgrade-target.sh`` script from the new bundle

   .. code-block:: bash
       :caption: *Run the upgrade-target script*

       ./upgrade-target.sh /path/of/install/to/be/upgraded

#. Review the following configuration files to start Studio in cluster mode as described
   :ref:`here <setup-a-two-node-cluster-with-studio>`:

   - ``bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml``
   - ``bin/apache-tomcat/shared/classes/crafter/studio/extension/hazelcast-config.yaml``
   - ``bin/crafter-setenv.sh`` |br|
     Remember to change the ``MARIADB_CLUSTER_NODE_COUNT`` to ``1`` so that our cluster comes up with just one node

     .. code-block:: bash
         :caption: *Set MARIADB_CLUSTER_NODE_COUNT to 1 in bin/crafter-setenv.sh*

         export MARIADB_CLUSTER_NODE_COUNT=${MARIADB_CLUSTER_NODE_COUNT:="1"}

#. The next step is to run the ``post-upgrade`` script from our target install ``/path/of/install/to/be/upgraded/bin/upgrade``

   .. note:: If you are not upgrading from a Studio version 4.1.x install, remember to switch to Java 17 before
             running the ``post-upgrade.sh`` script.

   The ``post-upgrade.sh`` script will give you a prompt to continue.  Before typing in ``Y``, make sure that Studio
   has started successfully by monitoring the tomcat logs.

   .. code-block:: bash
       :caption: *Post-upgrade script beginning output*
       :emphasize-lines: 4-5

       ./post-upgrade.sh
       ...

       Please make sure Crafter has started successfully before continuing
       > Continue? [(Y)es/(N)o]:

   Please note that Studio may take a while to start up because of upgrade manager updates performed when starting up Studio.
   Your tomcat log should look similar to below when Studio has started successfully:

   .. code-block:: bash
       :caption: *Tomcat log of Studio successfully started*
       :emphasize-lines: 6

       [INFO] 2023-07-27T15:57:39,603 [pool-19-thread-1] [remote01] [context.SiteContext] | --------------------------------------------------
       [INFO] 2023-07-27T15:57:39,602 [pool-18-thread-1] [editorial123] [context.SiteContext] | </Initializing context site: editorial123>
       [INFO] 2023-07-27T15:57:39,604 [pool-18-thread-1] [editorial123] [context.SiteContext] | --------------------------------------------------
       27-Jul-2023 15:57:40.119 INFO [main] org.apache.catalina.startup.HostConfig.deployWAR Deployment of web application archive [/opt/crafter/cluster/crafter/bin/apache-tomcat/webapps/ROOT.war] has finished in [7,357] ms
       27-Jul-2023 15:57:40.121 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["http-nio-8080"]
       27-Jul-2023 15:57:40.130 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [113638] milliseconds

   Here's the output of the ``post-upgrade.sh`` script after successfully completing the post-upgrade:

   .. code-block:: bash
       :caption: *Post-upgrade script output*

       Please make sure Crafter has started successfully before continuing
       > Continue? [(Y)es/(N)o]: Y
       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       Re-creating Search indexes for sites
       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       WARNING: This will delete the current Search site indexes and recreate them.
       This is necessary because of a major Search upgrade. Don't proceed
       if you can't have any search downtime.
       > Proceed? [(Y)es/(N)o]: Y
       Re-index succesfully triggered for 'editorial123-authoring'
       Re-index succesfully triggered for 'editorial123-preview'
       Re-index succesfully triggered for 'remote01-authoring'
       Re-index succesfully triggered for 'remote01-preview'
       Re-index succesfully triggered for 'vc01-authoring'
       Re-index succesfully triggered for 'vc01-preview'
       ========================================================================
       Post-upgrade completed
       ========================================================================
       Crafter has already been started, you can use the system again


#. Monitor the tomcat logs until the upgrade is finished.  Check in Studio that your node is up:

   .. image:: /_static/images/system-admin/primary-node-upgraded.webp
       :width: 80 %
       :align: center
       :alt: Upgrading Studio Cluster - Primary Node

  Once the upgrade is finished shutdown Studio.

|hr|

.. _adding-a-new-node-to-cluster:

-------------------------
Add a New Node to Cluster
-------------------------
After upgrading a node, we can now add the rest of the node/s to the cluster.
Here are the steps for adding a new node to the cluster:

#. Stop the Studio cluster (the primary server)

#. In the Replica server, review the following configuration files to start Studio in cluster mode as described
   :ref:`here <setup-a-two-node-cluster-with-studio>`:

   - ``bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml``
   - ``bin/apache-tomcat/shared/classes/crafter/studio/extension/hazelcast-config.yaml``
   - ``bin/crafter-setenv.sh`` |br|
     Remember to set the ``MARIADB_CLUSTER_NODE_COUNT`` to ``2``

     .. code-block:: bash
         :caption: *Set MARIADB_CLUSTER_NODE_COUNT to 2*

         export MARIADB_CLUSTER_NODE_COUNT=${MARIADB_CLUSTER_NODE_COUNT:="2"}

#. Start the Primary first and wait until startup has completed by monitoring the tomcat logs
   and looking for something like below:

   .. code-block:: bash
       :caption: *Primary Tomcat startup logs*
       :emphasize-lines: 3

       [INFO] 2023-07-27T16:19:45,369 [main] [cluster.DbPrimaryReplicaClusterSynchronizationServiceImpl] | Synchronizing startup of node 'cluster1.craftercms.org' with DB cluster 'studio_db_cluster'
       27-Jul-2023 16:19:45.377 INFO [main] com.hazelcast.internal.partition.impl.PartitionStateManager.null [172.31.70.118]:5701 [dev] [5.2.3] Initializing cluster partition table arrangement...
       [INFO] 2023-07-27T16:19:45,523 [main] [cluster.DbPrimaryReplicaClusterSynchronizationServiceImpl] | All '1' cluster members have started up
       [INFO] 2023-07-27T16:19:45,547 [main] [cluster.DbPrimaryReplicaClusterSynchronizationServiceImpl] | Local DB cluster node will start primary.

#. Start up the new Replica and wait until startup has completed by monitoring the tomcat logs
   and looking for something like below:

   .. code-block:: bash
       :caption: *New Replica tomcat startup logs*
       :emphasize-lines: 5,8

       [INFO] 2023-07-27T16:22:26,357 [main] [cluster.DbPrimaryReplicaClusterSynchronizationServiceImpl] | Context refreshed. Status of DB cluster node will switch to 'Active'
       [INFO] 2023-07-27T16:22:26,354 [studioTaskExecutor-1] [cluster.ClusterSandboxRepoSyncTask] | Received event 'SiteEvent{siteId='editorial123', timestamp=1690474946353, user=null}'
       [INFO] 2023-07-27T16:22:26,363 [studioTaskExecutor-3] [cluster.ClusterSandboxRepoSyncTask] | Received event 'SiteEvent{siteId='vc01', timestamp=1690474946354, user=null}'
       [INFO] 2023-07-27T16:22:26,365 [studioTaskExecutor-2] [cluster.ClusterSandboxRepoSyncTask] | Received event 'SiteEvent{siteId='remote01', timestamp=1690474946353, user=null}'
       [INFO] 2023-07-27T16:22:29,440 [main] [cluster.StudioClusterUtils] | This server is a replica node in a cluster, it will not perform any write
       27-Jul-2023 16:22:29.471 INFO [main] org.apache.catalina.startup.HostConfig.deployWAR Deployment of web application archive [/opt/crafter/cluster/crafter/bin/apache-tomcat/webapps/studio.war] has finished in [28,847] ms
       27-Jul-2023 16:22:29.474 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["http-nio-8080"]
       27-Jul-2023 16:22:29.487 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [50244] milliseconds

   You will notice that when both servers have started successfully, Studio will begin to populate the data directory on the Replica.

   .. image:: /_static/images/system-admin/cluster-upgraded.webp
       :width: 80 %
       :align: center
       :alt: Upgrading Studio Cluster - Cluster Upgraded and Running

