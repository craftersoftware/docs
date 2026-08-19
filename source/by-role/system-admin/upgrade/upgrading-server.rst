:is-up-to-date: True
:last-updated: 5.0.0

.. index:: Upgrade CrafterCMS, Upgrade

.. meta::
   :description: Step-by-step instructions for upgrading CrafterCMS on a server using the upgrade-target and start-upgrade scripts with backup and rollback guidance.
   :keywords: CrafterCMS server upgrade, upgrade-target script, start-upgrade, backup before upgrade, CrafterCMS upgrade steps, server upgrade, Crafter Studio upgrade

.. _upgrading-craftercms-on-a-server:

================================
Upgrading CrafterCMS on a Server
================================
This section details how to upgrade CrafterCMS installed on a server.

CrafterCMS provides a couple of scripts for upgrading your installation.

* :ref:`Running the upgrade script (upgrade-target) from a new binary archive <upgrade-using-new-binary-archive>`
* :ref:`Running the upgrade script (start-upgrade) from your current installation <upgrade-using-current-install>`

The upgrade script allows you to do an upgrade, where your ``bin`` directory is upgraded, keeping only Tomcat's ``shared`` folder, Tomcat's ``conf`` folder, the search config, the Deployer ``config`` folder, and the ``crafter-setenv`` scripts.

When performing an upgrade, CrafterCMS is shut down, then the script asks if the user wants to backup the ``data`` folder. It will then ask if the user wants to backup the ``bin`` folder, then perform the upgrade. After running the upgrade script (either *upgrade-target*  or *start-upgrade*), run the ``post-upgrade`` script. Finally, you can :ref:`start your CrafterCMS <start-crafter-after-upgrade>` install again.

Depending on how recent the version you are upgrading from, there may be files that do not exist in the new release and the script will give the user the option to delete or keep the files.

   .. code-block:: bash
      :force:
      :caption: *Example option to delete or keep files when running upgrade-target script*

      -----------------------------------------------------------------------------------------------------------------------------------------------
      Config file [apache-tomcat/shared/classes/crafter/search/extension/server-config.properties] doesn't exist in the new release. Delete the file?
      - (N)o
      - (Y)es
      - (A)lways delete files absent from new release and don't ask again
      - (Q)uit the upgrade script (this will stop the upgrade at this point)
      -----------------------------------------------------------------------------------------------------------------------------------------------
      > Enter your choice:

   |

For config files that are different in the new release, the script gives you the option to overwrite the config files with their new versions. When the script overwrites a file, it creates a backup version of the file with a timestamp and a bak file extension.

   .. code-block:: bash
      :force:
      :caption: *Example option to overwrite config files when running upgrade-target script*

      -------------------------------------------------------------------------------
      Config file [crafter-setenv.sh] is different in the new release. Please choose:
       - (D)iff file versions to see what changed
       - (E)dit the original file (with $EDITOR)
       - (K)eep the original file
       - (O)verwrite the file with the new version
       - (A)lways overwrite config files and don't ask again
       - (Q)uit the upgrade script (this will stop the upgrade at this point)
      -------------------------------------------------------------------------------
      > Enter your choice:

|


----------------
Before Upgrading
----------------
Before starting your upgrade:

#. **Review the** :ref:`release notes <release-notes>` **for the version you are upgrading to**. It contains specific information on the changes that have been made and how it may affect you when upgrading to that specific version.

   Remember to read all other release notes in between the version you currently have and the version you are upgrading to as it contains information on the changes that have occurred and steps you might have to take when upgrading to a specific version. Remember to also check :ref:`breaking changes <breaking-changes-5-x>` as it may require changes on your installation or system for the version you are upgrading to to work.

#. **Backup CrafterCMS** just in case something goes wrong with the upgrade.

   When upgrading CrafterCMS installed on a server, the upgrade scripts performs an automated backup of CrafterCMS, but it's recommended not to rely on the automated backup, just in case. See :ref:`backup-and-restore` for details on how to perform the backup of CrafterCMS

#. **Manually shut down CrafterCMS**   For CrafterCMS installed on a server, the upgrade scripts shuts down CrafterCMS as one of the first steps, but it's also recommended not to rely on the automated shutting down just in case.

   To shutdown CrafterCMS, run the ``shutdown.sh`` script from the ``{Crafter-CMS-install-directory}/bin`` directory


.. _upgrade-using-new-binary-archive:

---------------------------------------------------------------
Upgrade by running the upgrade script from a new binary archive
---------------------------------------------------------------
Download the CrafterCMS version you'd like to upgrade to, and extract the files.

To upgrade your CrafterCMS, we will use the ``upgrade-target.sh`` script. The upgrade script  is located in ``{Crafter-CMS-install-directory}/bin/upgrade`` of your newly downloaded binary archive. Here's the description for the script we are going to use:

    .. code-block:: bash

        usage: upgrade-target [options] <target-installation-path>
        -h,--help   Show usage information

|

where:
    ``<target-installation-path>`` is the path of your CrafterCMS install to be upgraded

    ``[options]`` is optional

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Steps for upgrading using the upgrade script from a new binary archive
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Here are the steps for upgrading your CrafterCMS install version from a new binary archive:

#. Download the CrafterCMS binary archive version you'd like to upgrade to
#. Extract the binary archive from the previous step and go into the ``bin/upgrade`` folder
#. Run the ``upgrade-target.sh`` script
#. Change to the target folder and run the ``post-upgrade.sh`` script

Here's an example of running the upgrade script ``upgrade-target`` from  a new binary archive:

    .. code-block:: bash

        ./upgrade-target.sh /path/of/install/to/be/upgraded

|

Here's an example of running the ``post-upgrade.sh`` script:

    .. code-block:: bash

       ./post-upgrade.sh

|

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Example upgrading using the upgrade script from a new binary archive
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Let's take a look at an example of upgrading a CrafterCMS version 4.4.4 install (located in ``/Users/myuser/crafter-4-4-4``) to version 5.0.0 using the upgrade script from 5.0.0

After downloading and extracting CrafterCMS version 5.0.0 to ``/Users/myuser/crafter-5.0.0``, we are now ready to start upgrading by running the ``upgrade-target`` script from the 5.0.0 binary archive.

    .. code-block:: bash
        :force:
        :emphasize-lines: 2,25-26,28,40-49,57,59

        ➜  cd crafter-5.0.0/bin/upgrade
        ➜  ./upgrade-target.sh /Users/myuser/crafter-4-4-4
        ========================================================================
        Shutting down Crafter
        ========================================================================
        Preflight check.
        Operating system is Mac, must use Docker to run OpenSearch.
        Running in this mode is for development purposes only.

         ██████╗ ██████╗   █████╗  ███████╗ ████████╗ ███████╗ ██████╗   ██████╗ ███╗   ███╗ ███████╗
        ██╔════╝ ██╔══██╗ ██╔══██╗ ██╔════╝ ╚══██╔══╝ ██╔════╝ ██╔══██╗ ██╔════╝ ████╗ ████║ ██╔════╝
        ██║      ██████╔╝ ███████║ █████╗      ██║    █████╗   ██████╔╝ ██║      ██╔████╔██║ ███████╗
        ██║      ██╔══██╗ ██╔══██║ ██╔══╝      ██║    ██╔══╝   ██╔══██╗ ██║      ██║╚██╔╝██║ ╚════██║
        ╚██████╗ ██║  ██║ ██║  ██║ ██║         ██║    ███████╗ ██║  ██║ ╚██████╗ ██║ ╚═╝ ██║ ███████║
        ╚═════╝ ╚═╝  ╚═╝ ╚═╝  ╚═╝ ╚═╝         ╚═╝    ╚══════╝ ╚═╝  ╚═╝  ╚═════╝ ╚═╝     ╚═╝ ╚══════╝

        ------------------------------------------------------------------------
        Stop Tomcat
        ------------------------------------------------------------------------
        Tomcat already shutdown
        ------------------------------------------------------------------------
        Stop Deployer
        ------------------------------------------------------------------------
        Deployer already shutdown
        > Backup the data folder before upgrade? [(Y)es/(N)o]: n
        > Backup the bin folder before upgrade? [(Y)es/(N)o]: n
        ========================================================================
        Upgrading Crafter 4.4.4 -> 5.0.0
        ========================================================================
        Synching files from /Users/myuser/crafter-4.1.0/bin to /Users/myuser/crafter-4.1.1/bin...
        [-] Deleting file cli/repo/org/craftercms/cli/4.1.0/cli-4.1.0.jar that doesn't exist in the new release
        [-] Deleting file cli/repo/org/craftercms/cli/4.1.0 that doesn't exist in the new release
        [-] Deleting file .java-version that doesn't exist in the new release
        [o] Overwriting file version.txt with the new release version
        [o] Overwriting file upgrade/hooks/StartCrafterHook.groovy with the new release version
        .
        .
        .
        ----------------------------------------------------------------------------------------------------
        Config file [apache-tomcat/conf/catalina.properties] is different in the new release. Please choose:
          - (D)iff file versions to see what changed
          - (E)dit the original file (with $EDITOR)
          - (K)eep the original file
          - (O)verwrite the file with the new version
          - (M)atching config files for regex [apache-tomcat/conf/.+] should always be overwritten
          - (A)lways overwrite config files and don't ask again
          - (Q)uit the upgrade script (this will stop the upgrade at this point)
        ----------------------------------------------------------------------------------------------------
        > Enter your choice: o

        [o] Overwriting config file apache-tomcat/conf/catalina.properties with the new release version (backup of the old one will be at apache-tomcat/conf/catalina.properties.20230810143118.bak)
        .
        .
        .

        ========================================================================
        Upgrade completed
        ========================================================================
        !!! Please read the release notes and make any necessary manual changes, then run the post upgrade script: /Users/myuser/crafter-4-4-4/bin/upgrade/post-upgrade.sh !!!

    |

After the ``upgrade-target`` script is finished running, the next step is to run the ``post-upgrade`` script from our target install ``/Users/myuser/crafter-4.4.4/bin/upgrade``

   .. code-block:: bash
      :force:
      :caption: *Example output when running the post-upgrade script*
      :emphasize-lines: 2,21

      ➜ cd /Users/myuser/crafter-4.4.4/bin/upgrade
      ➜ ./post-upgrade.sh
      =======================================================================
      Post-upgrade 4.4.4 -> 5.0.0
      ========================================================================
      Found hooks match for version >=4.1.0
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      Make published repo bare for all sites
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      Processing repository for site: ed
      No published repository found at /Users/myuser/crafter-4.4.4/data/repos/sites/ed/published, skipping...

      Processing repository for site: hello
      published repository for site hello is now bare

      All published repositories are now bare
      ========================================================================
      Post-upgrade completed
      ========================================================================
      !!! Crafter has not been started, please run /Users/myuser/crafter-4.4.4/bin/startup.sh to start it !!!

   |

You may :ref:`start CrafterCMS <start-crafter-after-upgrade>` now.

.. _upgrade-using-current-install:

---------------------------------------------------------------
Upgrade by running the upgrade script from your current install
---------------------------------------------------------------
CrafterCMS contain the upgrade scripts required to upgrade your install. Here's the description for the script we are going to use:

    .. code-block:: bash

        usage: start-upgrade [options]
        -h,--help                 Show usage information
        -p,--bundle-path <path>   The path of the Crafter binary archive in the
                                  filesystem. If you specify this path the URL
                                  and version parameter will be ignored
        -u,--bundle-url <url>     The URL of the Crafter binary archive to download. If
                                  you specify this URL the version parameter will
                                  be ignored
        -v,--version <version>    The community version of the Crafter binary archive to
                                  download

|

where:
   ``[options]`` is optional.

The ``start-upgrade.sh`` script downloads the CrafterCMS version or uses the CrafterCMS archive that you specify that you would like to upgrade to, then creates a script ``upgrade`` in ``{Crafter-CMS-install-directory}/temp/upgrade`` that performs the upgrade.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Steps for upgrading using the upgrade script from your current install
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To upgrade your current CrafterCMS install:

#. Go to your ``bin/upgrade`` folder
#. Run the ``start-upgrade.sh`` script. The ``start-upgrade.sh`` script downloads the CrafterCMS binary archive version you would like to upgrade to. This will create a script ``upgrade.sh`` in ``{Crafter-CMS-install-directory}/temp/upgrade``.
#. Go to ``{Crafter-CMS-install-directory}/temp/upgrade`` and run the ``upgrade.sh`` script
#. Go to ``{Crafter-CMS-install-directory}/bin/upgrade`` and run the ``post-upgrade.sh`` script

      .. note::
         When upgrading your CrafterCMS version, remember to check the release notes in case the version you are upgrading to uses a different Java JDK version than your current install. Remember to switch your JAVA_HOME environment variable to point to the new Java JDK version required before running the ``post-upgrade.sh`` script

#. Delete the``{Crafter-CMS-install-directory}/temp/upgrade`` once your upgrade has been completed successfully

Here's an example to perform an upgrade of your current install to a certain version

    .. code-block:: bash

        $ ./start-upgrade.sh -v 5.0.0
        $ cd ../../temp/upgrade
        $ ./upgrade.sh


|

Here's an example to perform an upgrade of your current install using a binary archive  url

    .. code-block:: bash

        $ ./start-upgrade.sh -u https://download/url/to/binary/archive
        $ cd ../../temp/upgrade
        $ ./upgrade.sh

|

Here's an example to perform an upgrade of your current install using the path where your binary archive was downloaded

    .. code-block:: bash

        $ ./start-upgrade.sh -p /path/to/binary/archive
        $ cd ../../temp/upgrade
        $ ./upgrade.sh

|

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Example running the upgrade script from your current install
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Let's take a look at an example of upgrading a CrafterCMS version 4.4.4 install (located in ``/Users/myuser/crafter``) to version 5.0.0 using the upgrade script in 4.4.4

We'll perform an upgrade of 4.4.4 (current install) to 5.0.0:

   .. code-block:: bash
      :emphasize-lines: 1,18
      :caption: *Example output running the start-upgrade script*

      ➜  ./start-upgrade.sh -v 5.0.0
      ============================================================
      Downloading Bundle
      ============================================================
      Downloading bundle @ https://downloads.craftercms.org/5.0.0/crafter-cms-authoring-5.0.0-darwin-x86_64.tar.gz...
      Downloading md5sum @ https://downloads.craftercms.org/5.0.0/crafter-cms-authoring-5.0.0-darwin-x86_64.tar.gz.md5...
      Doing checksum...
      ============================================================
      Extracting Bundle
      ============================================================
      Extracting bundle to folder /Users/myuser/crafter/temp/upgrade
      ============================================================
      Setting up upgrade script
      ============================================================
      ========================================================================
      Start upgrade completed
      ========================================================================
      !!! Please execute /Users/myuser/crafter/temp/upgrade/upgrade.sh to continue with upgrade !!!

   |

The next step is to run the ``upgrade.sh`` script under the ``temp/upgrade`` folder

   .. code-block:: bash
      :force:
      :emphasize-lines: 2,25-26,28,37-45,50,52-54
      :caption: *Example running the upgrade script from the temp directory*

      ➜ cd ../../temp/upgrade
      ➜ ./upgrade.sh
      ========================================================================
      Shutting down Crafter
      ========================================================================
      Preflight check.
      Operating system is Mac, must use Docker to run OpenSearch.
      Running in this mode is for development purposes only.

       ██████╗ ██████╗   █████╗  ███████╗ ████████╗ ███████╗ ██████╗   ██████╗ ███╗   ███╗ ███████╗
      ██╔════╝ ██╔══██╗ ██╔══██╗ ██╔════╝ ╚══██╔══╝ ██╔════╝ ██╔══██╗ ██╔════╝ ████╗ ████║ ██╔════╝
      ██║      ██████╔╝ ███████║ █████╗      ██║    █████╗   ██████╔╝ ██║      ██╔████╔██║ ███████╗
      ██║      ██╔══██╗ ██╔══██║ ██╔══╝      ██║    ██╔══╝   ██╔══██╗ ██║      ██║╚██╔╝██║ ╚════██║
      ╚██████╗ ██║  ██║ ██║  ██║ ██║         ██║    ███████╗ ██║  ██║ ╚██████╗ ██║ ╚═╝ ██║ ███████║
       ╚═════╝ ╚═╝  ╚═╝ ╚═╝  ╚═╝ ╚═╝         ╚═╝    ╚══════╝ ╚═╝  ╚═╝  ╚═════╝ ╚═╝     ╚═╝ ╚══════╝

      ------------------------------------------------------------------------
      Stop Tomcat
      ------------------------------------------------------------------------
      Tomcat already shutdown
      ------------------------------------------------------------------------
      Stop Deployer
      ------------------------------------------------------------------------
      Deployer already shutdown
      > Backup the data folder before upgrade? [(Y)es/(N)o]: n
      > Backup the bin folder before upgrade? [(Y)es/(N)o]: n
      ========================================================================
      Upgrading Crafter 4.4.4 -> 5.0.0
      ========================================================================
      Synching files from /Users/myuser/crafter/temp/upgrade/crafter/bin to /Users/myuser/crafter/bin...
      [-] Deleting file opensearch/plugins/opensearch-security-analytics/plugin-security.policy that doesn't exist in the new release
      [-] Deleting file opensearch/plugins/opensearch-security-analytics/plugin-descriptor.properties that doesn't exist in the new release
      .
      .
      .
      ------------------------------------------------------------------------------------------
      Config file [crafter-deployer/logging.xml] is different in the new release. Please choose:
       - (D)iff file versions to see what changed
       - (E)dit the original file (with $EDITOR)
       - (K)eep the original file
       - (O)verwrite the file with the new version
       - (A)lways overwrite config files and don't ask again
       - (Q)uit the upgrade script (this will stop the upgrade at this point)
      ------------------------------------------------------------------------------------------
      > Enter your choice:
      .
      .
      .
      ========================================================================
      Upgrade completed
      ========================================================================
      !!! Please read the release notes and make any necessary manual changes, then run the post upgrade script: /Users/myuser/crafter/bin/upgrade/post-upgrade.sh !!!

      If the upgrade was completed successfully, please delete the upgrade temp/upgrade directory (rm -rf /Users/myuser/crafter/temp/upgrade)

   |

Finally we'll  run the ``post-upgrade.sh`` script. Remember to check the release notes in case the version you are upgrading to uses a different Java JDK version than your current install and if so, remember to switch your JAVA_HOME environment variable to point to the new Java JDK version before running the ``post-upgrade.sh`` script.

   .. code-block:: bash
      :emphasize-lines: 2,11

      ➜ cd ../../bin/upgrade
      ➜ ./post-upgrade.sh
      ========================================================================
      Post-upgrade 4.4.4 -> 5.0.0
      ========================================================================
      Found hooks match for version >=4.1.0
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      Make published repo bare for all sites
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      Processing repository for site: ed
      No published repository found at /Users/myuser/crafter/data/repos/sites/ed/published, skipping...

      Processing repository for site: hello
      published repository for site hello is now bare

      All published repositories are now bare
      ========================================================================
      Post-upgrade completed
      ========================================================================
      !!! Crafter has not been started, please run /Users/myuser/crafter/bin/startup.sh to start it !!!

   |

You may :ref:`start CrafterCMS <start-crafter-after-upgrade>` now.

.. _start-crafter-after-upgrade:

----------------
Start CrafterCMS
----------------
After performing the upgrade steps listed above (either by running the upgrade script from a new binary archive or, by running the upgrade script from your current install) you may now start CrafterCMS by running the ``startup.sh`` script.

   .. code-block:: bash

      ➜ ./startup.sh

     Preflight check.
      Operating system is Mac, must use Docker to run OpenSearch.
      Running in this mode is for development purposes only.

       ██████╗ ██████╗   █████╗  ███████╗ ████████╗ ███████╗ ██████╗   ██████╗ ███╗   ███╗ ███████╗
      ██╔════╝ ██╔══██╗ ██╔══██╗ ██╔════╝ ╚══██╔══╝ ██╔════╝ ██╔══██╗ ██╔════╝ ████╗ ████║ ██╔════╝
      ██║      ██████╔╝ ███████║ █████╗      ██║    █████╗   ██████╔╝ ██║      ██╔████╔██║ ███████╗
      ██║      ██╔══██╗ ██╔══██║ ██╔══╝      ██║    ██╔══╝   ██╔══██╗ ██║      ██║╚██╔╝██║ ╚════██║
      ╚██████╗ ██║  ██║ ██║  ██║ ██║         ██║    ███████╗ ██║  ██║ ╚██████╗ ██║ ╚═╝ ██║ ███████║
       ╚═════╝ ╚═╝  ╚═╝ ╚═╝  ╚═╝ ╚═╝         ╚═╝    ╚══════╝ ╚═╝  ╚═╝  ╚═════╝ ╚═╝     ╚═╝ ╚══════╝

      ------------------------------------------------------------------------
      Check if upgrade is needed for embedded database /Users/myuser/crafter/data/db
      ------------------------------------------------------------------------
      DB version 11.4.3-MariaDB is up to date with new DB engine version 11.4.3
      MariaDB upgrade is not REQUIRED
      ------------------------------------------------------------------------
      Start Deployer
      ------------------------------------------------------------------------
      ------------------------------------------------------------------------
      Start OpenSearch
      ------------------------------------------------------------------------
      ------------------------------------------------------------------------
      Start Tomcat
      ------------------------------------------------------------------------
      Using CATALINA_BASE:   /Users/myuser/crafter/bin/apache-tomcat
      Using CATALINA_HOME:   /Users/myuser/crafter/bin/apache-tomcat
      Using CATALINA_TMPDIR: /Users/myuser/crafter/temp/tomcat
      Using JRE_HOME:        /Users/myuser/.jenv/versions/21
      Using CLASSPATH:       /Users/myuser/crafter/bin/apache-tomcat/bin/bootstrap.jar:/Users/myuser/crafter/bin/apache-tomcat/bin/tomcat-juli.jar
      Using CATALINA_OPTS:   -server -Xss1024K -Xms1G -Xmx4G -Dlog4j2.formatMsgNoLookups=true -Dtomcat.host=localhost -Dtomcat.http.port=8080   -Dtomcat.https.port=8443 -Dtomcat.ajp.port=8009   -Dtomcat.debug.port=8000 -Dcrafter.home=/Users/myuser/crafter   -Dcrafter.bin.dir=/Users/myuser/crafter/bin -Dcrafter.data.dir=/Users/myuser/crafter/data -Dcrafter.logs.dir=/Users/myuser/crafter/logs   -Dcatalina.logs=/Users/myuser/crafter/logs/tomcat -Dapplication.logs=/Users/myuser/crafter/logs/tomcat -Djava.net.preferIPv4Stack=true 	-Dgrape.root=/Users/myuser/crafter/bin
      Using CATALINA_PID:    /Users/myuser/crafter/bin/apache-tomcat/tomcat.pid
      Tomcat started.
      Log files live here: "/Users/myuser/crafter/logs".
      To follow the main tomcat log, you can run:
      tail -F /Users/myuser/crafter/logs/tomcat/catalina.out
      Happy Crafting

   |

Once you start up CrafterCMS, in the logs, notice the lines mentioning ``Check for upgrades ...`` like below:

   .. code-block:: text

      [INFO] 2025-10-31T09:01:23,307 [main] [upgrade.StudioUpgradeManagerImpl] | Check for upgrades to the blueprints
      ...
      [INFO] 2025-10-31T09:01:24,048 [main] [upgrade.StudioUpgradeManagerImpl] | Check for upgrades to the database and configuration
      ...
      [INFO] 2025-10-31T09:01:24,917 [main] [upgrade.StudioUpgradeManagerImpl] | Starting upgrade of all targets
      [INFO] 2025-10-31T09:01:24,919 [main] [upgrade.StudioUpgradeManagerImpl] | Starting upgrade for target 'hello'
      ...
      [INFO] 2025-10-31T09:01:25,070 [main] [upgrade.StudioUpgradeManagerImpl] | Check for upgrades to configuration in site 'hello'
      ...

   |

CrafterCMS has an upgrade manager that automatically upgrades the system, some configuration files and blueprints on startup. It uses a pipeline of handlers to upgrade various subsystems.

Note that the OpenSearch index will be automatically updated by the CrafterCMS upgrade manager whenever the OpenSearch index settings are updated, for example, a new field has been added for a release.
The updated index containing the new settings will be named the current index version name incremented by 1, e.g. let’s say the current index is ``mysite-authoring_v1``, after the upgrade, the new index will now be ``mysite-authoring_v2``.
