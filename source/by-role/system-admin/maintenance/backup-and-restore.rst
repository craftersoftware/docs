:is-up-to-date: True
:last-updated: 4.1.4

.. meta::
   :description: Backup and restore CrafterCMS environments using the provided scripts to protect indexes, repositories, and other critical data.
   :keywords: backup, restore, CrafterCMS, Crafter Studio, clustered, non-clustered, repos, system admin

.. _backup-and-restore:

==================
Backup and Restore
==================

CrafterCMS out of the box comes with a script to backup and restore your environment. The script provided will backup your environment including indexes, repos, and other data. If you use Crafter Profile or Crafter Social |enterpriseOnly|, MongoDB data is included as well. You can then use your favorite backup tools for backing up the script-generated CrafterCMS environment backup file.

.. TODO add a note that Delivery can be easily reconstituted

------
Backup
------
To backup CrafterCMS, you only need to backup the Authoring environment (Crafter Studio). The Delivery tier (Crafter Engine) can easily be reconstituted by instantiating new nodes that sync themselves. Alternatively, they can be backed up and restored as well.

^^^^^^^^^^^^^^^^^^^^
Non-clustered Studio
^^^^^^^^^^^^^^^^^^^^
For non-clustered Studio instances, shutdown CrafterCMS then perform the backup. Once the backup is done,
start it up again. This implies some downtime for authors.

^^^^^^^^^^^^^^^^
Clustered Studio
^^^^^^^^^^^^^^^^
For clustered instances, shutdown a Replica, then perform the backup against that Replica.
Once the backup is done, start up the Replica. This doesn't result in any downtime for authors.

In some instances, you may want to backup all the nodes (Primary and Replicas) in case restoration of all nodes
is necessary. In this case, you will need to shutdown the cluster first to perform the backup of all the nodes.

.. WARNING::

   Performing a backup while CrafterCMS is running is technically possible, but the resulting backup may not be viable.

|hr|

.. _running-the-backup-script:

-------------------------
Running the Backup Script
-------------------------
To backup your authoring or delivery environment, go to ``{Crafter-CMS-install-directory}/crafter-{env}/bin``, where ``{env}`` is the environment (either authoring or delivery) then run the following:

    .. code-block:: bash

       ./crafter.sh backup {filename}


where ``{filename}`` is optional.

Your backup file will be located in ``$CRAFTER_HOME/backups/`` by default (where ``$CRAFTER_HOME`` is ``{Crafter-CMS-install-directory}/crafter-{env}``).

You can change the backup file location by opening **{Crafter-CMS-install-directory}/{Crafter-ENV}/bin/crafter-setenv.sh** and change ``$CRAFTER_HOME/backups`` in the following line to your desired location:

   .. code-block:: bash

      export CRAFTER_BACKUPS_DIR=${CRAFTER_BACKUPS_DIR:="$CRAFTER_HOME/backups"}

   |


If a filename has been specified, your backup file will be named ``{filename}-{yyyy-MM-dd-hh-mm-ss}.zip`` where:
    - ``yyyy`` is the year, ex. 2017
    - ``MM`` is the month, from 01 to 12 ex. 07 for July
    - ``dd`` is the day, from 00 to 30 or 31 ex. 28
    - ``hh`` is the hour from 00 to 23
    - ``mm`` is the minutes from 00 to 59
    - ``ss`` is the seconds from 00 to 59

If no filename has been specified, the backup file will be named either ``crafter-authoring-backup-{yyyy-MM-dd-hh-mm-ss}.zip`` or ``crafter-delivery-backup-{yyyy-MM-dd-hh-mm-ss}.zip``, depending on which environment you are backing up.

|hr|

-------
Restore
-------
To restore your backup, make sure that CrafterCMS is not running. To restore your authoring or delivery environment, go to ``{Crafter-CMS-install-directory}/crafter-{env}/bin``, where ``{env}`` is the environment (either authoring or delivery) then run the following:

    .. code-block:: bash

       ./crafter.sh restore {backup-file}


It will then prompt you with the following message:

   .. code-block:: text

       Warning, you're about to restore CrafterCMS from a backup, which will wipe the existing sites and associated database and replace everything with the restored data. If you care about the existing state of the system then stop this process, backup the system, and then attempt the restore. Are you sure you want to proceed? (yes/no)

Enter ``yes`` if you want to proceed with the restore, otherwise, enter ``no``.

If you're restoring the authoring environment, Studio will be started by the restore process. If you're restoring the delivery environment, you will need to start the delivery environment after the restore process is done.

.. important::
    **When restoring the authoring environment, to avoid conflicts, please make sure you do one of the following for the delivery environment:**

    * Restore the delivery environment by following the steps listed above

    **OR**

    * Re-initialize the delivery environment by deleting the ``data`` folder under ``{Crafter-CMS-install-directory}/crafter-delivery/`` and running the init-site.sh script for all sites

      .. include:: /includes/ssh-private-key.rst

^^^^^^^^^^^^^^^^
Clustered Studio
^^^^^^^^^^^^^^^^
For clusters, you have a couple of options on restoring your backup/s:

- Restore both Primary and Replica node backups when necessary
- Restore only 1 node (Primary or Replica), which will become Primary. You then have to add a Replica using
  the instructions :ref:`here <adding-a-new-node-to-cluster>`.
