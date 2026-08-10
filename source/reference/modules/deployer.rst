:is-up-to-date: True
:last-updated: 4.5.0

.. index:: Modules; Crafter Deployer

.. meta::
   :description: Crafter Deployer is the CrafterCMS publishing and indexing engine, delivering content changes from Studio to Engine and search indexes.
   :keywords: Crafter Deployer, CrafterCMS, publishing, content indexing, deployment, search

.. _crafter-deployer:

================
Crafter Deployer
================
.. figure:: /_static/images/architecture/crafter-deployer.webp
   :alt: Crafter Deployer
   :width: 75%
   :align: center

Crafter Deployer is the deployment agent for CrafterCMS.

Crafter Deployer performs indexing and runs scheduled deployments to perform tasks like pushing/pulling content
created/edited in Crafter Studio to an external service, executing actions every time a deployment succeeds or fails,
sending out deployment notifications, etc. It is an independent process in the CrafterCMS suite of components.

In the diagram above, it shows a stateless delivery where a single Deployer is putting content into a file system or
bucket that is reachable by a Crafter Engine. As you can see, Crafter Engine in delivery does not need to know anything
about the deployment, it just reads the sources. It can be implemented with more Deployers if you're deploying to a
file system on a remote machine. The number of Deployers that you have and where they sit depends on the deployment
topology that you have.

How a Deployer works is it has targets for each project, so it has context. For each context, it pulls from a (remote)
repository and when it receives updates from that repository on that duty cycle it then performs a set of actions
through deployment processors. There's a set of out-of-the-box processors as described :ref:`below <crafter-deployer-processors-guide>`,
but you may also create your own :ref:`custom processors <custom-processors>`.

|hr|

-------------
Configuration
-------------
^^^^^^^^^^^^^^^^^^^
Configuration Files
^^^^^^^^^^^^^^^^^^^
Crafter Deployer can be configured at the global level and individual target level.

#. Global configuration files are in ``$CRAFTER_HOME/bin/crafter-deployer/config/``, and will be applied to
   all targets loaded.

#. Individual target configuration files are in ``$CRAFTER_HOME/data/deployer/targets/{siteName}-{environment}.yaml``

.. note::
    Environment variables can be used to override any property defined as ``${env:ENVIRONMENT_VARIABLE}`` or ``${ENVIRONMENT_VARIABLE}`` in the configuration files. This allows you to inject these properties into a vanilla installation without modifying any actual files, which is especially useful when using Docker or Kubernetes. Environment variables are configured in the ``CRAFTER_HOME/bin/crafter-setenv.sh`` file. See :ref:`here <environment-variables>` for a list of environment variables used by CrafterCMS.

""""""""""""""""""""""""""
Global Configuration Files
""""""""""""""""""""""""""
Crafter Deployer has two main property configuration files found in ``CRAFTER_HOME/bin/crafter-deployer/config``:

* **application.yaml:** contains the global application properties, like the server port and the locations of other configuration files.
* **base-target.yaml:** contains the common properties for all targets. In here you can find properties for configuring indexing with
  Crafter Search and deployment notifications.

The ``application.yaml`` file is loaded automatically by Spring Boot, so its properties can be overridden in the standard external locations
defined by Spring Boot:

#. ``application.yaml`` in a ``config`` directory under the current directory.
#. ``application.yaml`` in the current directory.
#. ``application.yaml`` in a ``config`` directory in the classpath.
#. ``application.yaml`` in the classpath root.

You can also override the ``application.yaml`` properties by specifying them as System properties, e.g. ``-Dserver.port=7171``.

Here's a sample ``application.yaml`` file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample application.yaml file</a></summary>

.. code-block:: yaml
    :linenos:

    deployer:
      main:
        config:
          environment:
            active: ${CRAFTER_ENVIRONMENT}
        targets:
          config:
            folderPath: ${targets.dir}
        deployments:
          folderPath: ${deployments.dir}
          output:
            folderPath: ${logs.dir}
          processedCommits:
            folderPath: ${processedCommits.dir}
        logging:
          folderPath: ${logs.dir}
        management:
          # Deployer management authorization token
          authorizationToken: ${DEPLOYER_MANAGEMENT_TOKEN}
        security:
          encryption:
            # The key used for encryption of configuration properties
            key: ${CRAFTER_ENCRYPTION_KEY}
            # The salt used for encryption of configuration properties
            salt: ${CRAFTER_ENCRYPTION_SALT}
          ssh:
            # The path of the folder used for the SSH configuration
            config: ${CRAFTER_SSH_CONFIG}

.. raw:: html

   </details>

|

The ``base-target.yaml`` file is handled a little bit differently. This file is loaded by Crafter Deployer every time a new target is added and is merged with the specific properties of the target, 
with the target's properties taking precedence. By default, the override location for this configuration file is ``CRAFTER_HOME/bin/crafter-deployer/config/base-target.yaml``, 
but it can be changed through the ``application.yaml`` property ``deployer.main.targets.config.baseYaml.overrideLocation``.

Here's a sample ``base-target.yaml`` file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample base-target.yaml file</a></summary>

.. code-block:: yaml
    :linenos:

    target:
      localRepoPath: ${deployer.main.deployments.folderPath}/${target.siteName}
      engineUrl: ${env:ENGINE_URL}
      engineManagementToken: ${env:ENGINE_MANAGEMENT_TOKEN}
      studioUrl: ${env:STUDIO_URL}
      studioManagementToken: ${env:STUDIO_MANAGEMENT_TOKEN}
      translation:
        # Indicates if the translation features should be enabled for the target
        enable: false
      search:
        openSearch:
          # Single Cluster
          urls:
            - ${env:SEARCH_URL}
          username: ${env:SEARCH_USERNAME}
          password: ${env:SEARCH_PASSWORD}
          timeout:
            # The connection timeout in milliseconds, if set to -1 the default will be used
            connect: -1
            # The socket timeout in milliseconds, if set to -1 the default will be used
            socket: -1
          # The number of threads to use, if set to -1 the default will be used
          threads: -1
          # Indicates if keep alive should be enabled for sockets used by the search client, defaults to false
          keepAlive: false

          # Multiple Clusters
          #      readCluster:
          #        urls:
          #        username:
          #        password:
          #      writeClusters:
          #        - urls:
          #          username:
          #          password:
          #        - urls:
          #          username:
          #          password:

          # Settings used for all indices
          indexSettings:
            - key: "index.mapping.total_fields.limit"
              value : 3000
            - key: "index.mapping.depth.limit"
              value: 40

          notifications:
            mail:
              server:
                host: ${env:MAIL_HOST}
                port: ${env:MAIL_PORT}

.. raw:: html

   </details>

|

where:

  - ``engineURL`` and ``engineManagementToken``are required for calling Engine APIs, and the environment variables (*env:VARIABLE_NAME*) values are set in the ``crafter-setenv.sh`` file
  - ``studioURL`` and ``studioManagementToken``are required for calling Studio APIs, and the environment variables (*env:VARIABLE_NAME*) values are set in the ``crafter-setenv.sh`` file

""""""""""""""""""""""""""
Target Configuration Files
""""""""""""""""""""""""""
Each deployment target has its own YAML configuration file, where the properties of the target and its entire deployment pipeline are specified.
Without this file, the Deployer doesn't know of the target's existence. By default, these configuration files reside under
``./config/targets`` (in the case of the CrafterCMS installed on a server, they're under ``CRAFTER_HOME/data/deployer/targets``).

Target configurations vary a lot between authoring and delivery since an authoring target works on a local repository while a delivery target
pulls the files from a remote repository. But target configurations between the same environment don't change a lot. Having said that, the
following two examples can be taken as a base for most authoring/delivery target configuration files:

|

.. raw:: html

   <details>
   <summary><a>Authoring Target Configuration File Example</a></summary>

.. code-block:: yaml
  :caption: *Authoring Target Configuration Example (editorial-preview.yaml)*
  :linenos:

  target:
    # Environment name
    env: preview
    # Site name
    siteName: editorial
    # Crafter Engine base URL
    engineUrl: http://localhost:8080
    # Path to the sandbox repository of the site
    localRepoPath: /opt/crafter/authoring/data/repos/sites/editorial/sandbox
    deployment:
      scheduling:
        # Scheduling is disabled since Studio will call deploy on file save
        enabled: false
      pipeline:
        # Calculates the Git differences with the latest commit processed
        - processorName: gitDiffProcessor
        # Performs Crafter Search indexing
        - processorName: searchIndexingProcessor
        # Calls Rebuild Context when a file under /scripts is changed
        - processorName: httpMethodCallProcessor
          includeFiles: ["^/?scripts/.*$"]
          method: GET
          url: ${target.engineUrl}/api/1/site/context/rebuild.json?crafterSite=${target.siteName}
        # Calls Clear Cache
        - processorName: httpMethodCallProcessor
          method: GET
          url: ${target.engineUrl}/api/1/site/cache/clear.json?crafterSite=${target.siteName}
        # Generates a deployment output file
        - processorName: fileOutputProcessor

.. raw:: html

    </details>

|

.. raw:: html

   <details>
   <summary><a>Delivery Target Configuration File Example</a></summary>

.. code-block:: yaml
  :caption: *Delivery Target Configuration Example (editorial-dev.yaml)*
  :linenos:

  target:
    # Environment name
    env: dev
    # Site name
    siteName: editorial
    # Crafter Engine base URL
    engineUrl: http://localhost:9080
    deployment:
      pipeline:
        # Pulls the remote Git repository of the site
        - processorName: gitPullProcessor
          remoteRepo:
            # URL of the remote repo
            url: /opt/crafter/authoring/data/repos/sites/editorial/published
            # Branch of the repo to pull
            branch: live
        # Calculates the Git differences with the latest commit processed
        - processorName: gitDiffProcessor
        # Performs Crafter Search indexing
        - processorName: searchIndexingProcessor
        # Calls Rebuild Context when a file under /scripts is changed
        - processorName: httpMethodCallProcessor
          includeFiles: ["^/?scripts/.*$"]
          method: GET
          url: ${target.engineUrl}/api/1/site/context/rebuild.json?crafterSite=${target.siteName}
        # Calls Clear Cache
        - processorName: httpMethodCallProcessor
          method: GET
          url: ${target.engineUrl}/api/1/site/cache/clear.json?crafterSite=${target.siteName}
        # Generates a deployment output file
        - processorName: fileOutputProcessor

.. raw:: html

    </details>

|

As you can see from the examples above, most of the configuration belongs to the deployment pipeline section. Each
of the YAML list entries is an instance of a ``DeploymentProcessor`` prototype Spring bean that is already defined
in the ``base-context.xml`` file. If you want to define your own set of ``DeploymentProcessor`` beans you can add
them on a new Spring context file based on the target's YAML file name. Using the authoring example above, since
the YAML file name is ``editorial-preview.yaml``, the corresponding Spring context would be ``editorial-preview-context.xml``.
The Deployer out of the box provides the following processor beans:

* **gitPullProcessor:** Clones a remote repository into a local path. If the repository has been cloned already, it performs
  a Git pull. This is useful for delivery targets which need to reach out to the authoring server to retrieve the changes on
  deployment. This must be the first processor in the list since the rest of the processors work on the local repository.

* **gitDiffProcessor:** Calculates the diff between the latest commit in the local repository and the last commit processed,
  which is usually stored under ``./processed-commits`` (in the folder ``CRAFTER_HOME/data/deployer/processed-commits``). This diff is then used to build the change set of the deployment, so this processor should be the second on the list.

* **searchIndexingProcessor:** grabs the files from the change set and sends them to Crafter Search for indexing. It
  also does some XML processing before submitting the files like flattening (recursive inclusion of components), merging
  of inherited XML and metadata extraction for structured document files like PDFs, Word Docs, etc.

* **httpMethodCallProcessor:** executes an HTTP method call to a specified URL.

* **fileOutputProcessor:** generates the deployment output and saves it to a CSV file.

* **mailNotificationProcessor:** sends an email notification when there's a successful deployment with file changes or when
  a deployment failed.

* **webhookNotificationProcessor:** sends a webhook notification with the result of the deployment.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Deployer Configuration Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In this section, we will highlight some of the more commonly used properties in the configuration of Crafter Deployer.

.. list-table:: Common Global Application Configuration Properties
    :header-rows: 1

    * - Property
      - Purpose
    * - :ref:`deployer-thread-pool-size`
      - Allows you to configure the deployment pool |br|
    * - :ref:`deployer-deployment-supervisor`

        .. version_tag::
            :label: Since
            :version: 4.4.2

      - Allows you to enable and configure the deployment supervisor |br|

The properties listed above are configured in ``CRAFTER_HOME/bin/crafter-deployer/config/application.yaml``.

|

.. list-table:: Common Target Configuration Properties
    :header-rows: 1

    * - Property
      - Purpose
    * - :ref:`deployer-single-search-cluster`
      - Allows you to configure a target with a single search cluster
    * - :ref:`deployer-multiple-search-cluster`
      - Allows you to configure a target with multiple search clusters
    * - :ref:`deployer-search-connection-pool`

        .. version_tag::
            :label: Since
            :version: 4.5.0

      - Allows you to configure search connection pool max values for total connections and connections per route
    * - :ref:`deployer-indexing-mime-types`
      - Allows you to configure MIME types used for document indexing
    * - :ref:`deployer-indexing-remote-documents-path-pattern`
      - Allows you to configure remote documents path patterns used for document indexing
    * - :ref:`deployer-indexing-metadata-path-pattern`
      - Allows you to configure metadata path patterns used for document indexing
    * - :ref:`deployer-notification-templates-override-location`
      - Allows you to configure override locations for notification templates
    * - :ref:`deployer-notification-webhook-timeout`

        .. version_tag::
            :label: Since
            :version: 4.4.2

      - Allows you to configure a timeout for webhook requests
    * - :ref:`deployer-target-runtime-threshold`

        .. version_tag::
            :label: Since
            :version: 4.4.2

      - Allows you to configure the target runtime threshold used by the :ref:`deployment supervisor <deployer-deployment-supervisor>` |br|
    * - :ref:`deployer-target-event-listeners`

        .. version_tag::
            :label: Since
            :version: 4.4.2

      - Allows you to configure event listeners for the target |br|

The target properties listed above may be configured in the following locations:

- Global configuration file ``$CRAFTER_HOME/bin/crafter-deployer/config/base-target.yaml``
- Individual target configuration file ``$CRAFTER_HOME/data/deployer/targets/{siteName}-{environment}.yaml``

.. _deployer-single-search-cluster:

"""""""""""""""""""""
Single Search Cluster
"""""""""""""""""""""
The following allows you to configure a target with a single search cluster.
This is the most common configuration used, all operations will be performed on a single search cluster:

.. code-block:: yaml
  :linenos:
  :caption: Target configuration for a single search cluster

    target:
      search:
        openSearch:
          # Single cluster
          urls:
            - ${env:SEARCH_URL}
          username: ${env:SEARCH_USERNAME}
          password: ${env:SEARCH_PASSWORD}
          timeout:
            # The connection timeout in milliseconds, if set to -1 the default will be used
            connect: -1
            # The socket timeout in milliseconds, if set to -1 the default will be used
            socket: -1
          # The number of threads to use, if set to -1 the default will be used
          threads: -1
          # Indicates if keep alive should be enabled for sockets used by the search client, defaults to false
          keepAlive: false

.. _deployer-multiple-search-cluster:

"""""""""""""""""""""""""""""""""""""""""""""""""
Multiple Search Engines or Search Engine Clusters
"""""""""""""""""""""""""""""""""""""""""""""""""
There may be cases where an enterprise needs to run multiple search engines or search engine clusters that carry the same data for extra redundancy beyond regular clustering. The following allows you to configure a target with multiple search clusters.
In the configuration below, all read operations will be performed against one search cluster but write operations will
be performed against all search clusters:

.. code-block:: yaml
  :linenos:
  :caption: Target configuration for multiple search clusters
  :emphasize-lines: 8,14

    target:
      search:
        openSearch:
          # Global auth, used for all clusters
          username: search
          password: passw0rd
          # Cluster for read operations
          readCluster:
            urls:
              - 'http://read-cluster-node-1:9200'
              - 'http://read-cluster-node-2:9200'
              # This cluster will use the global auth
          # Clusters for write operations
          writeClusters:
            - urls:
              - 'http://write-cluster-1-node-1:9200'
              - 'http://write-cluster-1-node-2:9200'
              # This cluster will use the global auth
            - urls:
              - 'http://write-cluster-2-node-1:9200'
              - 'http://write-cluster-2-node-2:9200'
              # Override the global auth for this cluster
              username: search2
              password: passw0rd2

.. _deployer-search-connection-pool:

""""""""""""""""""""""
Search Connection Pool
""""""""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.5.0

The following allows you to configure the search connection pool max values for total connections and connections per route
for  a single search cluster (``target.search.openSearch``), read cluster (``readCluster``) or write cluster (``writeClusters``):

.. code-block:: yaml
  :linenos:
  :caption: Target configuration for search connection pool max values
  :emphasize-lines: 4-6, 9-11, 14-15

  target:
    search:
      openSearch:
        maxTotalConnections: 40
        maxConnectionsPerRoute: 10
        readCluster:
          urls:
            - ${env:SEARCH_URL}
          maxTotalConnections: 40
          maxConnectionsPerRoute: 10
        writeClusters:
          - urls:
              - ${env:SEARCH_URL}
            maxTotalConnections: 40
            maxConnectionsPerRoute: 10

The default max connections per route is set to 2 and max total connections is set to 20.

.. _deployer-indexing-mime-types:

""""""""""
MIME types
""""""""""
The ``supportedMimeTypes`` configured in the ``base-target.yaml`` file determines what MIME types are used for indexing.
The following is the default list of MIME types with full-text-search indexing enabled.

.. code-block:: yaml
    :caption: *Default supported MIME types in base-target.yaml*
    :linenos:
    :emphasize-lines: 7-8

    target:
    ...
      search:
        openSearch:
        ...
        binary:
          # The list of binary file mime types that should be indexed
          supportedMimeTypes:
            - application/pdf
            - application/msword
            - application/vnd.openxmlformats-officedocument.wordprocessingml.document
            - application/vnd.ms-excel
            - application/vnd.ms-powerpoint
            - application/vnd.openxmlformats-officedocument.presentationml.presentation

~~~~~~~~~~~~~~~~~~~~~~~~
Custom MIME type support
~~~~~~~~~~~~~~~~~~~~~~~~
When authors try to link or embed files using Studio’s file picker, only indexed content appears. By default,
CrafterCMS indexes a predefined set of MIME types as shown above. If a file type isn’t listed in this set, it won’t
appear, even if it exists in your ``/static-assets`` folder or has been uploaded to S3.

To add other MIME types to the list of MIME types with full-text-search indexing enabled, so those files will appear
using Studio's file picker, simply edit the override file ``CRAFTER_HOME/bin/crafter-deployer/config/base-target.yaml``
and add your desired MIME type to the list under ``target.search.indexing.binary.authoring.supportedMimeTypes``.

If you're unsure about the MIME type of a file, you can check it using a tool like ``file --mime-type`` on Unix-based
systems. You can also review the following listing of all possible supported mimetypes here:
https://github.com/craftersoftware/craftercms/blob/support/4.x/deployer/src/main/resources/META-INF/mime.types

For a list of common MIME types, see https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types.

After updating the configuration, you must restart your environment and reindex your project for changes to take effect.
See the following documentation on initiating a re-index: :ref:`start-reindexing-reprocessing`.

Finally, you can verify in Studio that your custom MIME type is now indexed by navigating to a content form or RTE
where you use the "Select an item" dialog. You should now see your previously missing file types (e.g., .csv) in the
file selection modal.

**Example**

Let's take a look at an example of extending the supported MIME types. We'll use a project created using the website
editorial blueprint. Upload a ``.csv`` file to ``/static-assets/files``, and modify the content type ``Article`` to add
an ``Item Selector`` control and a ``File Browse`` datasource with the ``Repository Path`` set to ``/static-assets``
and bind your newly created datasource to the control you just added as shown below:

.. image:: /_static/images/system-admin/setup-sample-adding-mime-type.webp
    :width: 80%
    :alt: Setup for example adding MIME type in Deployer
    :align: center

|

Edit an article via form by right-clicking an article on the Sidebar, then selecting ``Edit``, then scroll down to the
Item Selector control we added ``Attachment``, click on ``Add +`` then ``Browse for Existing - File Browse``. Once the
``Select an item`` dialog opens, browse to the path under ``static-assets`` where you uploaded the ``.csv`` files.
Notice when you try to browse for ``.csv`` files that none are listed.

.. image:: /_static/images/system-admin/sample-adding-mime-type-no-csv.webp
    :width: 60%
    :alt: Example adding MIME type in Deployer - ".csv" files are not available
    :align: center

|

Note that ``.csv`` (Comma Separated Values) files are not indexed by default. For our example, we'll add
``.csv`` files to the supported MIME types.

First, we'll add the MIME type ``text/csv`` to the list in the ``base-target.yaml`` file under
``target.search.indexing.binary.authoring.supportedMimeTypes``:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/base-target.yaml*
    :linenos:
    :emphasize-lines: 9-10,25

    target:
    ...
      search:
        openSearch:
        ...
        indexing:
          binary:
            # Setting specific for authoring indexes
            authoring:
              supportedMimeTypes:
                - application/pdf
                - application/msword
                - application/vnd.openxmlformats-officedocument.wordprocessingml.document
                - application/vnd.ms-excel
                - application/vnd.ms-powerpoint
                - application/vnd.openxmlformats-officedocument.presentationml.presentation
                - application/x-subrip
                - image/*
                - video/*
                - audio/*
                - text/x-freemarker
                - text/x-groovy
                - text/javascript
                - text/css
                - text/csv

We'll save our changes then restart the environment and trigger a reindex of our project by running this curl command:

.. code-block:: xml

  curl "http://localhost:9191/api/1/target/deploy/authoring/editorial" -X POST -H "Content-Type: application/json" -d '{ "reprocess_all_files": true }'

Finally, we can verify in Studio that ``.csv`` files are now supported.

.. image:: /_static/images/system-admin/sample-adding-mime-type.webp
    :width: 60%
    :alt: Example adding MIME type in Deployer - ".csv" files are now available
    :align: center

|

.. _deployer-indexing-remote-documents-path-pattern:

"""""""""""""""""""""""""""""
Remote Documents Path Pattern
"""""""""""""""""""""""""""""
CrafterCMS can index documents that reside in remote repositories, but are pointed-to by CrafterCMS content.
The ``remoteBinaryPathPatterns`` configured in the ``base-target.yaml`` file determines what a remote document
is, within a content item, via the regex path pattern. The default for this is configured as follows:

.. code-block:: yaml
    :caption: *Default remoteBinaryPathPatterns in base-target.yaml*
    :linenos:
    :emphasize-lines: 8-9

    target:
    ...
      search:
        openSearch:
        ...
        binary:
          ...
        # The regex path patterns for binary/document files that are stored remotely
          remoteBinaryPathPatterns: &remoteBinaryPathPatterns
            # HTTP/HTTPS URLs are only indexed if they contain the protocol (http:// or https://). Protocol relative
            # URLs (like //mydoc.pdf) are not supported since the protocol is unknown to the back-end indexer.
            - ^(http:|https:)//.+$
            - ^/remote-assets/.+$

To add other remote document path patterns to the list, simply edit the override file
``CRAFTER_HOME/bin/crafter-deployer/config/base-target.yaml`` and add to the list under
``target.search.binary.remoteBinaryPathPatterns``.

.. _deployer-indexing-metadata-path-pattern:

"""""""""""""""""""""
Metadata Path Pattern
"""""""""""""""""""""
The ``metadataPathPatterns`` configured in the ``base-target.yaml`` file determines if a document should be indexed with
the metadata of the object that points to it (a so-called "jacket"). The deployer will re-index the jacket and the
document together whenever the jacket is updated. See :ref:`jacket` for more information.

.. code-block:: yaml
    :caption: *Default metadataPathPatterns in base-target.yaml*
    :linenos:
    :emphasize-lines: 8-9

    target:
    ...
      search:
        openSearch:
        ...
        binary:
          ...
          # The regex path patterns for the metadata ("jacket") files of binary/document files
          metadataPathPatterns:
            - ^/?site/documents/.+\.xml$

To add other jacket patterns to the list, simply edit the override file
``CRAFTER_HOME/bin/crafter-deployer/config/base-target.yaml`` and add to the list under
``target.search.binary.metadataPathPatterns``.

.. _deployer-thread-pool-size:

""""""""""""""""
Thread Pool Size
""""""""""""""""
As the number of sites grows you may need more workers (threads) in the Deployer to service them. If you do not add more
workers then you will see errors in scheduled tasks. Eventually, the system will get through the workload with the workers it
has available, and the error will stop, but the presence of these errors on a regular basis indicates that you need
more workers in the pool.

There are two thread pools available. The deployment pool, which is used to run all deployments and the task scheduler
pool, which is used for starting deployments on a schedule of every 10 secs. For deployments of sites with a lot content
(big sites), we recommend increasing the deployment pool. For deployments with a lot of sites, we recommend increasing
the task scheduler pool.

To increase the deployment pool, set the following items in ``CRAFTER_HOME/bin/crafter-deployer/config/application.yaml``
as shown below:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/application.yaml - Deployment Pool*
    :linenos:

    deployer:
      main:
        deployments:
          pool:
            # Thread pool core size
            size: 25
            # Thread pool max size
            max: 100
            # Thread pool queue size
            queue: 100

|

To increase the thread pool size of the task scheduler, set the ``poolSize`` property in
``CRAFTER_HOME/bin/crafter-deployer/config/application.yaml`` as shown below:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/application.yaml - Task Scheduler Pool*
    :linenos:

    deployer:
      main:
        taskScheduler:
          # Thread pool size of the task scheduler
          poolSize: 20

Here's a sample *application.yaml* file with the deployment pool and task thread pool configured:

.. raw:: html

   <details>
   <summary><a>Sample application.yaml file showing Deployment and Task Scheduler Pools</a></summary>

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/application.yaml*
    :emphasize-lines: 3-5, 12-19
    :linenos:

    deployer:
      main:
        taskScheduler:
          # Thread pool size of the task scheduler
          poolSize: 20
        config:
          environment:
            active: ${CRAFTER_ENVIRONMENT}
        targets:
          config:
            folderPath: ${targets.dir}
        deployments:
          pool:
            # Thread pool core size
            size: 25
            # Thread pool max size
            max: 100
            # Thread pool queue size
            queue: 100
          folderPath: ${deployments.dir}
          output:
            folderPath: ${logs.dir}
          processedCommits:
            folderPath: ${processedCommits.dir}
        logging:
          folderPath: ${logs.dir}
        management:
          # Deployer management authorization token
          authorizationToken: ${DEPLOYER_MANAGEMENT_TOKEN}
        security:
          encryption:
            # The key used for encryption of configuration properties
            key: ${CRAFTER_ENCRYPTION_KEY}
            # The salt used for encryption of configuration properties
            salt: ${CRAFTER_ENCRYPTION_SALT}
          ssh:
            # The path of the folder used for the SSH configuration
            config: ${CRAFTER_SSH_CONFIG}

.. raw:: html

   </details>

|

.. _deployer-deployment-supervisor:

"""""""""""""""""""""
Deployment Supervisor
"""""""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.4.2

The deployment supervisor when enabled and configured monitors deployment time and sends out a notification if
the deployment time exceeds the configured threshold.

Below is a sample configuration for the deployment supervisor:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/application.yaml - Deployment Supervisor Configuration*
    :linenos:
    :emphasize-lines: 4-7

    deployer:
      main:
        deployments:
          # Configuration for the deployment supervisor
          supervisor:
              enabled: true
              cron: '0 */10 * * * ?'

where:

- ``deployer.main.deployments.supervisor.enable`` - Indicates if the deployment supervisor is running
- ``deployer.main.deployments.supervisor.cron`` - The cron expression for scheduling how often the deployment supervisor
  checks the deployment run time against the threshold. The default is 10 minutes.

The deployment supervisor runs as a cron job that wakes up every X minutes (10 minutes by default, configured via
``deployer.main.deployments.supervisor.cron``) and if a target deployment has been running longer than the configured
:ref:`target runtime threshold <deployer-target-runtime-threshold>`, it triggers the ``deploymentRuntimeWarning`` event
that sends out a notification. (To configure event listeners, see: :ref:`deployer-target-event-listeners`).

|

.. _deployer-target-runtime-threshold:

""""""""""""""""""""""""
Target Runtime Threshold
""""""""""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.4.2

The target runtime threshold used by the :ref:`deployment supervisor <deployer-deployment-supervisor>` can be configured
using ``target.runtimeWarningThreshold.seconds`` property, which by default is set to 1 hour.

.. code-block:: yaml
    :linenos:
    :caption: *Default runtime warning threshold in base-target.yaml*

    target:
    runtimeWarningThreshold:
        # The threshold in seconds to warn about long-running deployments
        seconds: 3600

|

.. _deployer-target-event-listeners:

""""""""""""""""""""""
Target Event Listeners
""""""""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.4.2

Targets have event listeners that sends out email and webhook notifications, configurable through the target as shown below:

.. code-block:: yaml
    :linenos:
    :caption: *Example event listeners configuration in base-target.yaml*

    target:
      eventListeners:
        -   eventName: deploymentRuntimeWarning  # This is the type of event to handle
            listeners:
            -   name: notificationEventListener
                templateName: runtime-warning
                sender: emailSender
                to: admin@test.com
            -   name: notificationEventListener
                sender: webhookSender

The available event listeners are as follows:

- **notificationEventListener**:
  The following properties are required by the ``notificationEventListener``:

  - ``sender``: the actual message sender. Supported senders are: ``webhookSender`` or ``emailSender``

  - ``templateName``: freeMarker template to render the message. Notice that the actual path of the template file will depend on the sender.

    - For the ``webhookSender`` it uses **target.notifications.webhook.templates.location**
    - For the ``emailSender`` it uses **target.notifications.mail.templates.location**

  Additionally, it accepts configuration properties for the message sender, e.g.:

  .. code-block:: yaml
      :linenos:
      :caption: *Example additional properties for the message sender in base-target.yaml*

      - name: notificationEventListener
        templateName: runtimeWarning
        from: alerts@test.com
        to: admin@test.com  # REQUIRED
        subject: Runtime threshold reached
      - name: notificationEventListener
        templateName: runtimeWarning
        url: <THE WEBHOOK URL>  # REQUIRED
        method: GET
        contentType: text/plain

|

.. _deployer-notification-templates-override-location:

"""""""""""""""""""""""""""""""""""""""""
Notification Templates Override Locations
"""""""""""""""""""""""""""""""""""""""""
The mail and webhook notification processors uses templates for the notifications that are sent.

The mail notification processor by default uses the templates located in ``classpath:templates/mail``.
To change the mail templates location, simply set the ``mail.templates.overrideLocation`` property:

.. code-block:: yaml
    :linenos:
    :caption: *Default Mail Notification Processor Templates Location in base-target.yaml*
    :emphasize-lines: 6-7

    notifications:
      mail:
        templates:
          # The location (Spring URL) of  the mail templates
          location: classpath:templates/mail
          # The override location (Spring URL) of the mail templates
          overrideLocation: file:${deployer.main.config.folderPath}/templates/mail
          # The name of the default mail template
          default: default

The webhook notification processor by default uses the templates located in ``classpath:templates/webhook``.
To change the webhook templates location, simply set the ``webhook.templates.overrideLocation`` property:

.. code-block:: yaml
    :linenos:
    :caption: *Default Webhook Notification Processor Templates Location in base-target.yaml*
    :emphasize-lines: 6-7

    notifications:
      webhook:
        templates:
          # The location (Spring URL) of  the webhook templates
          location: classpath:templates/webhook
          # The override location (Spring URL) of the webhook templates
          overrideLocation: file:${deployer.main.config.folderPath}/templates/webhook
          # The name of the default template
          default: slack
          # The suffix used to resolve the final name of a template
          suffix: -template.ftl

|

.. _deployer-notification-webhook-timeout:

""""""""""""""""""""""""""""
Notification Webhook Timeout
""""""""""""""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.4.2

The target notification webhook timeout can be configured using ``target.notifications.webhook.timeouts`` property,
which by default is set to 30 seconds.

.. code-block:: yaml
    :linenos:
    :caption: *Default Notifications Webhook Timeout in base-target.yaml*
    :emphasize-lines: 5

    target:
        notifications:
            webhook:
                # The timeout in milliseconds for the webhook requests
                timeout: 30000

|hr|

.. _crafter-deployer-security:

--------
Security
--------

.. _deployer-groovy-sandbox-configuration:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Groovy Sandbox Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. include:: /includes/groovy-sandbox-configuration.rst

"""""""""""""""""""""""""
Groovy Sandbox Properties
"""""""""""""""""""""""""
The following allows you to configure the Groovy sandbox.
The Groovy sandbox is enabled by default and can be disabled by changing the property ``deployer.main.scripting.sandbox.enabled`` to ``false``.

.. code-block:: yaml
    :linenos:
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/application.yaml*
    :emphasize-lines: 6

    deployer:
      main:
        scripting:
          sandbox:
            # Indicates if the sandbox should be enabled for all targets
            enabled: true
            blacklist:
              # Indicates if the blacklist should be enabled for all targets
              # (this will have no effect if the sandbox is disabled)
              enabled: true
              # The location of the blacklist to use for all targets
              # (this will have no effect if the sandbox is disabled)
              path: 'classpath:groovy/blacklist'
            whitelist:
              # Indicates if the whitelist should be enabled for all targets
              # (this will have no effect if the sandbox is disabled)
              enabled: false
              # The location of the whitelist to use for all targets
              # (this will have no effect if the sandbox is disabled)
              path: 'file:${deployer.main.config.folderPath}/groovy/whitelist,classpath:groovy/whitelist'
              # List of patterns for that is allowed to call as `staticMethod java.lang.System getenv java.lang.String` parameter (regexes separated by commas)
              # NOTE: This property is applied even if the whitelist is disabled
              getenvRegex: crafter_.*

|

""""""""""""""""""""""""
Using a Custom Blacklist
""""""""""""""""""""""""
Crafter Deployer includes a default blacklist that you can find
`here <https://github.com/craftersoftware/craftercms/blob/support/4.x/deployer/src/main/resources/groovy/blacklist>`__. Make sure you review the branch/tag you're using.

To use a custom blacklist follow these steps:

#. Copy the default blacklist file to your classpath, for example:

    ``CRAFTER_HOME/bin/crafter-deployer/groovy/extension/blacklist``

#. Remove or comment (adding a ``#`` at the beginning of the line) the expressions that your scripts require
#. Update the ``application.yaml`` configuration file to load the custom blacklist:

    .. code-block:: yaml
        :caption: ``CRAFTER_HOME/bin/crafter-deployer/config/application.yaml``

        sandbox:
          blacklist:
            # The location of the blacklist to use for all targets
            # (this will have no effect if the sandbox is disabled)
            path: 'file:groovy/extension/blacklist'

#. Restart CrafterCMS

Now you can execute the same script without any issues.

|

"""""""""""""""""""""""""""""""
Disabling the Sandbox Blacklist
"""""""""""""""""""""""""""""""
It is possible to disable the blacklist to allow the execution of most expressions, in
case you need to use a considerable number of the expression included in the blacklist while keeping some basic
restrictions. To disable the blacklist for all targets update the ``application.yaml`` configuration file:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/application.yaml*

    sandbox:
      blacklist:
        # Indicates if the blacklist should be enabled for all targets
        # (this will have no effect if the sandbox is disabled)
        enabled: false

|

""""""""""""""""""""""""
Using a Custom Whitelist
""""""""""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.5.0

Crafter Deployer includes a default whitelist that you can find `here <https://github.com/craftersoftware/craftercms/blob/support/4.x/deployer/src/main/resources/groovy/whitelist>`__. Make sure you review the branch/tag you're using.

To use a custom whitelist follow these steps:

#. Copy the default whitelist file to your classpath, for example:

    ``CRAFTER_HOME/bin/crafter-deployer/config/groovy/whitelist``

#. Add, remove or comment (adding a ``#`` at the beginning of the line) the expressions that your scripts require
#. Update the ``application.yaml`` configuration file to load the custom whitelist:

    .. code-block:: yaml
        :caption: ``CRAFTER_HOME/bin/crafter-deployer/config/application.yaml``

        sandbox:
          whitelist:
            # The location of the whitelist to use for all targets
            # (this will have no effect if the sandbox is disabled)
            path: 'file:${deployer.main.config.folderPath}/groovy/whitelist,classpath:groovy/whitelist'

#. Restart CrafterCMS

Now you can execute the same script without any issues.

|

""""""""""""""""""""""""""""""
Enabling the Sandbox Whitelist
""""""""""""""""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.5.0

It is possible to only allow the execution of approved expressions included in a list to prevent operations that
could compromise the system, by using the sandbox whitelist. To create your custom sandbox whitelist, see above.

The sandbox whitelist is disabled by default. To enable the whitelist for all targets update the
``application.yaml`` configuration file:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/application.yaml*

    sandbox:
      whitelist:
        # Indicates if the whitelist should be enabled for all targets
        # (this will have no effect if the sandbox is disabled)
        enabled: true

|

"""""""""""""""""""
Grape Configuration
"""""""""""""""""""
.. include:: /includes/groovy-grape-configuration.rst

|

.. _deployer-grapes-download:

"""""""""""""""
Grapes Download
"""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.5.0

The following allows you to enable or disable automatic Groovy dependency (grapes) downloads for scripts (@grab):

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/application.yaml*
    :emphasize-lines: 8

    deployer:
        main:
            scripting:
                grapes:
                    # Indicates if grapes automatic downloading should be enabled for all targets
                    # If false, already downloaded grapes will be used, but no new grapes will be downloaded
                    download:
                        enabled: false

Automatic grapes download is disabled by default. Set ``deployer.main.scripting.grapes.download.enabled`` to true to
enable automatic grapes download.

|

"""""""""""""""""""""""""""""""""""""""
Installing Grapes from the Command Line
"""""""""""""""""""""""""""""""""""""""
.. include:: /includes/groovy-grape-install.rst

|

"""""""""""""""
Important Notes
"""""""""""""""
.. include:: /includes/groovy-sandbox-important-notes.rst

|

^^^^^^^^^^^^^^^^^^^^
Cipher Configuration
^^^^^^^^^^^^^^^^^^^^
.. code-block:: yaml
    :linenos:
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/application.yaml*

    deployer:
      main:
        security:
          encryption:
            # The key used for encryption of configuration properties
            key: ${CRAFTER_ENCRYPTION_KEY}
            # The salt used for encryption of configuration properties
            salt: ${CRAFTER_ENCRYPTION_SALT}
          ssh:
            # The path of the folder used for the SSH configuration
            config: ${CRAFTER_SSH_CONFIG}

|hr|

.. _crafter-deployer-administration:

--------------
Administration
--------------
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
How to Start/Stop the Deployer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If you're using CrafterCMS installed on a server, starting and stopping the Deployer is very easy. From the command line, navigate to the
``{env-directory}``, authoring or delivery environment folder, and then inside the ``bin`` folder, run ``./crafter.sh start_deployer`` to start
the Deployer or ``./crafter.sh stop_deployer`` to stop the Deployer.

^^^^^^^^^^^^^^
Manage Targets
^^^^^^^^^^^^^^
"""""""""""""""
Create a Target
"""""""""""""""
There are two different ways in which a target configuration file can be created:

* By calling the API endpoint :base_url:`createTarget <_static/api/deployer.html#tag/target/operation/createTarget>`, which creates a new target based on a template. The Deployer comes out
  of the box with two templates: one for local repositories (useful for authoring environments) and one for remote repositories (useful for
  delivery environments). You can also specify your templates under ``./config/templates/targets``, and use the same API endpoint to create
  targets based on those templates.
* By placing the YAML target configuration file under ``./config/targets`` (or ``CRAFTER_HOME/data/deployer/targets``, like indicated
  above). The Deployer will automatically load the file on a schedule, and whenever there's a change it will re-load it.

"""""""""""""""
Update a Target
"""""""""""""""
Updating a target is very similar to creating one:

* Call the same API endpoint as create, but be sure that the ``replace`` parameter is ``true``. OR
* Make the changes directly in the target configuration file. On the next scheduled scan of targets, the Deployer will detect that the file has
  been modified and it will re-load it.

"""""""""""""""
Delete a Target
"""""""""""""""
There are two options for deleting a target:

* Call the API endpoint :base_url:`deleteTarget <_static/api/deployer.html#tag/target/operation/deleteTarget>`.

* Delete the target configuration file in the filesystem.

.. _crafter-deployer-templates-guide:

^^^^^^^^^^^^^^^^
Target Templates
^^^^^^^^^^^^^^^^
When you are creating a target in Crafter Deployer, you can use one of the included templates that can be easily
customized with additional parameters during the creation.

""""""""""""""""""
Built-in Templates
""""""""""""""""""
All target templates support the following parameters:

+-------------+-----------+------------------------------------+
|Name         |Required   |Description                         |
+=============+===========+====================================+
|``env``      ||checkmark||The target’s environment (e.g. dev) |
+-------------+-----------+------------------------------------+
|``site_name``||checkmark||The target’s site name (e.g. mysite)|
+-------------+-----------+------------------------------------+
|``repo_url`` ||checkmark||The target's repository URL         |
+-------------+-----------+------------------------------------+

~~~~~~~~~~~~~~~~
Authoring Target
~~~~~~~~~~~~~~~~
This is one of the templates used by Crafter Studio when a new project/site is created, this template will set up a target for
Studio's search features to index all content items.

This target will:

- Identify the changed files according to the local Git repository history
- Index all site content using the search engine

**Parameters**

This target has no additional parameters.

.. note:: When this target is used, the value of ``repo_url`` must be a local filesystem path

~~~~~~~~~~~~
Local Target
~~~~~~~~~~~~
This is the other template used by Crafter Studio when a new project is created, this template will create a target for
previewing the project.

This target will:

- Identify the changed files according to the local Git repository history
- Index all project content in the search index
- Rebuild Crafter Engine's site context when there are changes in the configuration files or Groovy scripts
- Clear Crafter Engine's cache
- Rebuild Crafter Engine's project GraphQL schema when there are changes in the content-type definitions
- Send email notifications if enabled

**Parameters**

+--------------------------+----------+------------------------------------------------------------------------+
|Name                      |Required  |Description                                                             |
+==========================+==========+========================================================================+
|``disable_deploy_cron``   |          |Disables the cron job that runs deployments every certain amount of time|
+--------------------------+----------+------------------------------------------------------------------------+
|``notification_addresses``|          |The email addresses that should receive deployment notifications        |
+--------------------------+----------+------------------------------------------------------------------------+

.. note:: When this target is used, the value of ``repo_url`` must be a local filesystem path

~~~~~~~~~~~~~
Remote Target
~~~~~~~~~~~~~
This is the default template used for Crafter Engine in delivery environments, it is very similar to the Local Target
but it adds support for remote Git repositories.

This target will:

- Clone the remote repository if needed
- Pull the latest changes from the remote repository (discarding any local uncommitted or conflicting files)
- Identify the changed files according to the Git repository history
- Index all project content in the appropriate search engine
- Rebuild Crafter Engine's site context when there are changes in the configuration files or Groovy scripts
- Clear Crafter Engine's cache
- Rebuild Crafter Engine's project GraphQL schema when there are changes in the content-type definitions
- Send email notifications if enabled

**Parameters**

+------------------------------+----------+------------------------------------------------------------------------+
|Name                          |Required  |Description                                                             |
+==============================+==========+========================================================================+
|``disable_deploy_cron``       |          |Disables the cron job that runs deployments every certain amount of time|
+------------------------------+----------+------------------------------------------------------------------------+
|``repo_branch``               |          |The branch name of the remote Git repo to pull from                     |
+------------------------------+----------+------------------------------------------------------------------------+
|``repo_username``             |          |Username to access remote repository                                    |
+------------------------------+----------+------------------------------------------------------------------------+
|``repo_password``             |          |Password to access remote repository                                    |
+------------------------------+----------+------------------------------------------------------------------------+
|``ssh_private_key_path``      |          |The path for the private key to access the remote repository            |
+------------------------------+----------+------------------------------------------------------------------------+
|``ssh_private_key_passphrase``|          |The passphrase for the private key to access the remote repository      |
|                              |          |(only if the key is passphrase-protected)                               |
+------------------------------+----------+------------------------------------------------------------------------+
|``notification_addresses``    |          |The email addresses that should receive deployment notifications        |
+------------------------------+----------+------------------------------------------------------------------------+

.. note:: When this target is used, the value of ``repo_url`` must be a supported Git URL (HTTP/S or SSH)

~~~~~~~~~~~~~
AWS S3 Target
~~~~~~~~~~~~~
This template is used for Crafter Engine in serverless delivery environments, it is very similar to the Remote Target
but it adds support for syncing files to an AWS S3 bucket and handles AWS Cloudfront invalidations.

This target will:

- Clone the remote repository if needed
- Pull the latest changes from the remote repository (discarding any local uncommitted or conflicting files)
- Identify the changed files according to the Git repository history
- Index all project content in the search index
- Sync all new, updated, and deleted files to an AWS S3 bucket
- Execute an invalidation for all updated files in one or more AWS Cloudfront distributions
- Submit deployments events for all Crafter Engine instances:

  - Rebuild the site context when there are changes in the configuration files or Groovy scripts
  - Clear Crafter Engine's cache
  - Rebuild the site GraphQL schema when there are changes in the content-type definitions

- Send email notifications if enabled

**Parameters**

+------------------------------+-----------+------------------------------------------------------------------------+
|Name                          |Required   |Description                                                             |
+==============================+===========+========================================================================+
|``aws.region``                |           |The AWS Region to use                                                   |
+------------------------------+-----------+------------------------------------------------------------------------+
|``aws.access_key``            |           |The AWS Access Key to use                                               |
+------------------------------+-----------+------------------------------------------------------------------------+
|``aws.secret_key``            |           |The AWS Secret Key to use                                               |
+------------------------------+-----------+------------------------------------------------------------------------+
|``aws.distribution.ids``      |           |An array of AWS Cloudfront distribution ids to execute invalidations    |
+------------------------------+-----------+------------------------------------------------------------------------+
|``aws.s3.url``                ||checkmark||The full AWS S3 URI of the folder to sync files                         |
+------------------------------+-----------+------------------------------------------------------------------------+
|``disable_deploy_cron``       |           |Disables the cron job that runs deployments every certain amount of time|
+------------------------------+-----------+------------------------------------------------------------------------+
|``local_repo_path``           |           |The local path where to put the remote Git repo clone                   |
+------------------------------+-----------+------------------------------------------------------------------------+
|``repo_branch``               |           |The branch name of the remote Git repo to pull from                     |
+------------------------------+-----------+------------------------------------------------------------------------+
|``repo_username``             |           |Username to access remote repository                                    |
+------------------------------+-----------+------------------------------------------------------------------------+
|``repo_password``             |           |Password to access remote repository                                    |
+------------------------------+-----------+------------------------------------------------------------------------+
|``ssh_private_key_path``      |           |The path for the private key to access the remote repository            |
+------------------------------+-----------+------------------------------------------------------------------------+
|``ssh_private_key_passphrase``|           |The passphrase for the private key to access the remote repository      |
|                              |           |(only if the key is passphrase-protected)                               |
+------------------------------+-----------+------------------------------------------------------------------------+
|``notification_addresses``    |           |The email addresses that should receive deployment notifications        |
+------------------------------+-----------+------------------------------------------------------------------------+

.. note:: When this target is used, the value of ``repo_url`` must be a supported Git URL (HTTP/S or SSH)

.. note:: For more details about setting up a serverless delivery see :ref:`setup-serverless-delivery`

.. _aws-cloudformation-target:

~~~~~~~~~~~~~~~~~~~~~~~~~
AWS CloudFormation Target
~~~~~~~~~~~~~~~~~~~~~~~~~
This template is used to provide a serverless delivery environment without the need to manually create all required
resources in AWS. It works similarly to the AWS S3 Target but uses an AWS CloudFormation template to create the AWS
resources on target creation: the S3 bucket where the site content will be stored and a CloudFront distribution that
will front an Engine load balancer and deliver the static assets directly from the S3 bucket. These resources will be
deleted when the target is deleted.

This target will:

- Clone the remote repository if needed
- Pull the latest changes from the remote repository (discarding any local uncommitted or conflicting files)
- Identify the changed files according to the Git repository history
- Index all project content in the search index
- Sync all new, updated and deleted files to an AWS S3 bucket
- Execute an invalidation for all updated files in the AWS CloudFront distribution
- Submit deployments events for all Crafter Engine instances:

  - Rebuild the site context when there are changes in the configuration files or Groovy scripts
  - Clear Crafter Engine's cache
  - Rebuild the site GraphQL schema when there are changes in the content-type definitions

- Send email notifications if enabled

**Parameters**

+-----------------------------------------------------+-----------+----------------------------------------------------+
|Name                                                 |Required   |Description                                         |
+=====================================================+===========+====================================================+
|``aws.region``                                       |           |The AWS Region to use                               |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``aws.default_access_key``                           |           |The AWS Access Key to use for S3 and CloudFront     |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``aws.default_secret_key``                           |           |The AWS Secret Key to use for S3 and CloudFront     |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``aws.cloudformation.namespace``                     ||checkmark||Prefix to use for CloudFormation resource names     |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``aws.cloudformation.deliveryLBDomainName``          ||checkmark||The domain name of the Engine delivery LB           |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``aws.cloudformation.cloudfrontCertificateArn``      |           |The ARN of the CloudFront SSL certificate           |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``aws.cloudformation.alternateCloudFrontDomainNames``|           |The alternate domain names for the CloudFront to use|
|                                                     |           |(must match the valid certificate domain names)     |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``aws.cloudformation.access_key``                    |           |The AWS Access Key to use for CloudFormation        |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``aws.cloudformation.secret_key``                    |           |The AWS Secret Key to use for CloudFormation        |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``aws.cloudformation.stackCapabilities``             |           |The stack capabilities e.g. ``CAPABILITY_IAM``,     |
|                                                     |           |``CAPABILITY_NAMED_IAM`` and                        |
|.. version_tag::                                     |           |``CAPABILITY_AUTO_EXPAND``                          |
|    :label: Since                                    |           |                                                    |
|    :version: 4.2.0                                  |           |                                                    |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``disable_deploy_cron``                              |           |Disables the cron job that runs deployments every   |
|                                                     |           |certain amount of time                              |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``local_repo_path``                                  |           |The local path where to put the remoe Git repo clone|
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``repo_branch``                                      |           |The branch name of the remote Git repo to pull from |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``repo_username``                                    |           |Username to access remote repository                |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``repo_password``                                    |           |Password to access remote repository                |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``ssh_private_key_path``                             |           |The path for the private key to access remote       |
|                                                     |           |repository                                          |
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``ssh_private_key_passphrase``                       |           |The passphrase for the private key to access remote |
|                                                     |           |repository (only if the key is passphrase-protected)|
+-----------------------------------------------------+-----------+----------------------------------------------------+
|``notification_addresses``                           |           |The email addresses that should receive deployment  |
|                                                     |           |notifications                                       |
+-----------------------------------------------------+-----------+----------------------------------------------------+

.. note:: When this target is used, the value of ``repo_url`` must be a supported Git URL (HTTP or SSH)

^^^^^^^^^^^^^^^
Run Deployments
^^^^^^^^^^^^^^^
Crafter Deployer has an option of running scheduled deployments for a target (``deployment.scheduling.enabled``), which is enabled by default, but if you
want to manually trigger a deployment, you just need to call the API endpoint :base_url:`deployTarget <_static/api/deployer.html#tag/target/operation/deployTarget>` (or
:base_url:`deployAllTargets <_static/api/deployer.html#tag/target/operation/deployAllTargets>`). This will start the deployment if the request is correct. To watch the progress of a scheduled or manually
triggered deployment, check the Deployer log. When the deployment has finished, and the target has a ``fileOutputProcessor`` in the deployment pipeline, a
CSV file with the final result of that particular deployment will be written under ``./logs`` (or ``CRAFTER_HOME/logs/deployer``).

.. _deployer-processed-commits:

^^^^^^^^^^^^^^^^^
Processed Commits
^^^^^^^^^^^^^^^^^
Crafter Deployer keeps track of the most recent commit ID that was processed in the last deployment
for each target, during a deployment, it will use this commit ID to get the list of files that have been
changed in the repository.
By default, the processed commits are stored in a folder (``CRAFTER_HOME/data/deployer/processed-commits``)
as an individual file for each target (for example ``editorial-preview.commit``). Each file contains
only the commit ID will be used to track the changes during deployments:

.. code-block:: none
  :caption: Example of a processed commit file
  :linenos:

  0be0d2e52283c17b834901e9cda6332d06fb05b6

If the repository is changed manually using Git commands instead of updating files using Crafter
Studio it is possible that a deployment may find a conflict, for example, if a specific commit is
deleted from the repository. In most cases, Crafter Deployer should be able to detect those conflicts
and solve them automatically, however, if a deployment does not finish successfully you can follow
the steps described in :ref:`debugging-deployer-issues`

.. warning::
  Changing or deleting a processed commit file could cause unchanged files to be indexed again and
  it should be done as a last resort in case of errors.

.. _jacket:

^^^^^^
Jacket
^^^^^^
Jackets are CrafterCMS content items that carry metadata about a binary file. Jackets _wrap_ a binary file and augment it with metadata that flows into the search index as a single document. This makes for a much richer and more effective search experience. Jackets are modeled as a content item like any other content item and can carry arbitrary fields.

Crafter Deployer can index the content of a binary document if it can be transformed to text or has textual metadata. For example, PDF files, Office files, etc. will be indexed and made full-text-searchable. When jacketed, these files will be indexed along with the metadata provided by the jacket.

Jackets are identified by their path and a regex that is configured at the Deployer configuration's target level.
Administrators must configure where jackets are located via the ``base-target.yaml`` configuration file found in
``CRAFTER_HOME/bin/crafter-deployer/config/``. Jacket files live under ``/site/documents`` by default.

An example of a how a jacket is resolved is to have a binary file ``/static-assets/documents/contracts/2024-contract.pdf``, and the Deployer
resolves its jacket at ``/site/documents/contracts/2024-contract.xml``, extracts the XML content of the jacket,
and indexes everything under ``/static-assets/documents/contracts/2024-contract.pdf``

Below is an example Deployer configuration for jackets. Note that in the example below, jacket files live under ``/site/documents``:

|

.. raw:: html

    <details>
    <summary><a>Example Deployer configuration for jackets</a></summary>

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/crafter-deployer/config/base-target.yaml*
    :linenos:
    :emphasize-lines: 15-17, 60-62

    target:
    ...
      search:
        openSearch:
        ...
        binary:
          # The list of binary file mime types that should be indexed
          supportedMimeTypes:
            - application/pdf
            - application/msword
            - application/vnd.openxmlformats-officedocument.wordprocessingml.document
            - application/vnd.ms-excel
            - application/vnd.ms-powerpoint
            - application/vnd.openxmlformats-officedocument.presentationml.presentation
          # The regex path patterns for the metadata ("jacket") files of binary/document files
          metadataPathPatterns:
            - ^/?site/documents/.+\.xml$
          # The regex path patterns for binary/document files that are stored remotely
          remoteBinaryPathPatterns: &remoteBinaryPathPatterns
            # HTTP/HTTPS URLs are only indexed if they contain the protocol (http:// or https://). Protocol relative
            # URLs (like //mydoc.pdf) are not supported since the protocol is unknown to the back-end indexer.
            - ^(http:|https:)//.+$
            - ^/remote-assets/.+$
          # The regex path patterns for binary/document files that should be associated to just one metadata file and are
          # dependant on that parent metadata file, so if the parent is deleted the binary should be deleted from the index
          childBinaryPathPatterns: *remoteBinaryPathPatterns
          # The XPaths of the binary references in the metadata files
          referenceXPaths:
            - //item/key
            - //item/url
          # Setting specific for authoring indexes
          authoring:
            # Xpath for the internal name field
            internalName:
              xpath: '*/internal-name'
              includePatterns:
                - ^/?site/.+$
                - ^/?static-assets/.+$
                - ^/?remote-assets/.+$
                - ^/?scripts/.+$
                - ^/?templates/.+$
            contentType:
              xpath: '*/content-type'
            # Same as for delivery but include images and videos
            supportedMimeTypes:
              - application/pdf
              - application/msword
              - application/vnd.openxmlformats-officedocument.wordprocessingml.document
              - application/vnd.ms-excel
              - application/vnd.ms-powerpoint
              - application/vnd.openxmlformats-officedocument.presentationml.presentation
              - application/x-subrip
              - image/*
              - video/*
              - audio/*
              - text/x-freemarker
              - text/x-groovy
              - text/javascript
              - text/css
            # The regex path patterns for the metadata ("jacket") files of binary/document files
            metadataPathPatterns:
              - ^/?site/documents/.+\.xml$
            binaryPathPatterns:
              - ^/?static-assets/.+$
              - ^/?remote-assets/.+$
              - ^/?scripts/.+$
              - ^/?templates/.+$
            # Look into all XML descriptors to index all binary files referenced
            binarySearchablePathPatterns:
              - ^/?site/.+\.xml$
            # Additional metadata such as contentLength, content-type specific metadata
            metadataExtractorPathPatterns:
              - ^/?site/.+$
            excludePathPatterns:
              - ^/?config/.*$
            # Include all fields marked as remote resources (S3, Box, CMIS)
            referenceXPaths:
              - //item/key
              - //item/url
              - //*[@remote="true"]

.. raw:: html

    </details>

|

"""""""
Example
"""""""
Let's take a look at an example of setting up jackets for binary content. We'll use a project created using the Website
Editorial blueprint, and do the following:

#. Create a directory for binary content ``static-assets/documents``, and the directory for storing the
   jackets ``/site/documents/`` in your project
#. Configure the Sidebar cabinet for the new content type created in a previous step and set up permissions for roles
   interacting with the documents
#. Create content model for jackets and configure the project for the new content model

Let's begin setting up a jacket for binary contents.

First, we'll create the directory that will contain the binary content, ``static-assets/documents`` via Studio. On the
Sidebar, scroll down to ``static-assets``, then click on the more menu (the three dots) and select ``New Folder`` and type in
``Documents`` for the ``Folder Name``.

Next, we'll create the directory for storing the jackets in the project ``/site/documents/`` using your favorite
terminal program, add a ``.keep`` file inside the directory and finally add and commit it.

.. code-block:: bash

    cd CRAFTER_HOME/data/repos/sites/SITENAME/sandbox
    mkdir site/documents
    touch site/documents/.keep
    git add site/documents/.keep
    git commit -m "Add documents folder"

The next step is to set up the Sidebar cabinet for our jackets in Studio. To add the cabinet, open the
``User Interface Configuration`` file by opening the Sidebar in Studio, then clicking on ``Project Tools`` -> ``Configuration``
-> ``User Interface Configuration``. Scroll down to the ``ToolsPanel`` widget, and add a ``Documents`` widget under the
``Pages`` widget like below:

.. code-block:: xml
    :emphasize-lines: 9-17

    <widget id="craftercms.components.ToolsPanel">
      <configuration>
        <widgets>
          ...
          <widget id="craftercms.components.PathNavigatorTree">
            <configuration>
              <id>Pages</id>
              ...
          <widget id="craftercms.components.PathNavigatorTree">
            <configuration>
              <id>Documents</id>
              <label>Documents</label>
              <icon id="@mui/icons-material/DescriptionOutlined"/>
              <rootPath>/site/documents</rootPath>
              <locale>en</locale>
            </configuration>
          </widget>
          ...


We'll now set up permissions for roles interacting with the documents. For our example, we'll add permissions for
the ``author`` role. Open the ``Permissions Mapping`` file by opening the Sidebar in Studio, then clicking on
``Project Tools`` -> ``Configuration`` -> ``Permissions Mapping``. Scroll down to the ``<role name="author">`` section,
and add a regex for our ``/site/documents`` folder we created like below:

.. code-block:: xml
    :emphasize-lines: 7-16

    <permissions>
    <version>4.1.2</version>
    <role name="author">
      <rule regex="/site/website/.*">
            <allowed-permissions>
      ...
      <rule regex="/site/documents|/site/documents/.*">
        <allowed-permissions>
          <permission>content_read</permission>
          <permission>content_write</permission>
          <permission>content_create</permission>
          <permission>folder_create</permission>
          <permission>get_children</permission>
          <permission>content_copy</permission>
        </allowed-permissions>
      </rule>
      ...

Next, we'll create the content model for your jacket. To create a new content type, open the ``Content Types`` tool by
opening the Sidebar in Studio, then clicking on ``Project Tools`` -> ``Content Types``. Click on the ``Create New Type``
button, and use ``Document`` for the ``label`` and ``ID``, and select ``Component`` for ``Type``, then finally, click
on the ``Create`` button.

For the content type, we will add an ``Item Selector`` control that we'll name ``Asset``, and
a couple of data sources that will be bound to the control.  We will use the ``/static-assets/documents`` folder we
created earlier for the ``Repository Path`` of the two data sources we'll be adding, a ``File Upload From Desktop`` data
source that we'll name ``Upload`` and a ``File Browse`` data source that we'll name ``Existing``. For the metadata in
the jacket, it is up to you on what you'd like in the content model. For our example, we will add a ``Text Area`` control
named ``Summary``, and a ``Check Box`` control named ``Featured``.

.. image:: /_static/images/system-admin/deployer-jacket-content-model.webp
    :width: 80%
    :alt: Jacket Content Model
    :align: center

Finally, we'll set up our project for the content model we just created. Open the ``Project Configuration`` file by
opening the Sidebar in Studio, then clicking on ``Project Tools`` -> ``Configuration`` -> ``Project Configuration``.
Scroll down to the ``<repository rootPrefix="/site">`` section and add the folder ``/site/documents`` we created to the
``folders`` section. Next, scroll down to the ``<patterns>`` section. We'll add ``/site/documents`` to the component group.

.. code-block:: xml
    :emphasize-lines: 6,15

    <repository rootPrefix="/site">
      ...
      <folders>
        <folder name="Pages" path="/website" read-direct-children="false" attach-root-prefix="true"/>
        <folder name="Components" path="/components" read-direct-children="false" attach-root-prefix="true"/>
        <folder name="Documents" path="/documents" read-direct-children="false" attach-root-prefix="false"/>
        <folder name="Taxonomy" path="/taxonomy" read-direct-children="false" attach-root-prefix="true"/>
      ...
      </folders>
        <!-- Item Patterns -->
        <patterns>
          ...
          <pattern-group name="component">
            <pattern>/site/components/([^&lt;]+)\.xml</pattern>
            <pattern>/site/documents/([^&lt;]+)\.xml</pattern>
            <pattern>/site/system/page-components/([^&lt;]+)\.xml</pattern>
          ...

Our content model for the jacket is now complete! To add a jacket to content uploaded in ``static-assets/documents``,
open the Sidebar and scroll to ``Documents``. Open the cabinet then click on the three dots next to ``documents``, then
select ``New Content``.

.. image:: /_static/images/system-admin/deployer-jacket-new-document.webp
    :width: 40%
    :alt: Create New Jacket Document
    :align: center


Fill in the fields on the form and save.

.. image:: /_static/images/system-admin/deployer-create-jacket-for-binary-content.webp
    :width: 80%
    :alt: Fill In Form For New Jacket Document
    :align: center

Publish the changes. The binary content and jacket will now be indexed under the location of the binary content.

|hr|

.. _crafter-deployer-processors-guide:

-------------------
Deployer Processors
-------------------
Crafter Deployer includes an extensive list of deployment processors that can be easily added to any target
to meet specific requirements. Some examples of the use cases that can be addressed with deployment processors are:

- Pushing content created/edited in Crafter Studio to an external git repository
- Pulling content created/edited from an external git repository
- Execute actions every time a deployment succeeds or fails
- Pushing content to external systems like databases upon a publish

.. note::
  When adding processors or changing the deployment pipeline for a target keep in mind that the processors will be
  executed following the order defined in the configuration file and some processors require a specific position in the
  pipeline


.. |failDep| replace:: ``failDeploymentOnFailure``

^^^^^^^^^^^^^^^^^^^^^^^^^^
Main Deployment Processors
^^^^^^^^^^^^^^^^^^^^^^^^^^
The main deployment processors can do any task related to detecting a change-set (changed files) or processing a change-set (changed files) that were
detected by other processors. To process a change-set, a processor may interact with any external service as needed.

All deployment processors support the following properties:

.. list-table::
    :header-rows: 1
    :widths: 20 10 10 60

    * - Name
      - Required
      - Default Value
      - Description
    * - ``processorLabel``
      -
      - None
      - Label that other processors can use to jump to this one
    * - ``jumpTo``
      -
      - None
      - The label of the processor to jump to after a successful execution
    * - ``includeFiles``
      -
      - None
      - List of regular expressions to check the files that should be included
    * - ``excludeFiles``
      -
      - None
      - List of regular expressions to check the files that should be excluded
    * - ``alwaysRun``
      -
      - false
      - Indicates if the processor should run even if there are no changes in the current deployment
    * - ``failDeploymentOnFailure``
      -
      - false
      - Enables failing a deployment when there’s a processor failure
    * - ``runInClusterMode``

        .. version_tag::
            :label: Since
            :version: 4.1.1

      -
      - ``PRIMARY``
      - Indicates the current ClusterMode the processor should run.
        Available values are:

        - ``PRIMARY``: Run in primary instance only
        - ``REPLICA``: Run in replica instances only
        - ``ALWAYS``: Run in both primary and replica instances

        *The default value* ``ALWAYS`` *is used by the following processors*

        - *gitPullProcessor*
        - *gitDiffProcessor*
        - *gitUpdateCommitIdProcessor*

.. |lBranch| replace:: ``localRepoBranch``
.. |URL| replace:: ``remoteRepo.url``
.. |Name| replace:: ``remoteRepo.name``
.. |Branch| replace:: ``remoteRepo.branch``
.. |username| replace:: ``remoteRepo.username``
.. |password| replace:: ``remoteRepo.password``

.. |path| replace:: ``remoteRepo.ssh.privateKey.path``
.. |passphrase| replace:: ``remoteRepo.ssh.privateKey.passphrase``

.. _deployer-git-pull-processor:

""""""""""""""""""
Git Pull Processor
""""""""""""""""""
Processor that clones/pulls a remote Git repository into a local path on the filesystem.

.. note:: This needs to be the first processor in the pipeline

**Properties**

.. list-table::
    :header-rows: 1
    :widths: 20 10 10 60

    * - Name
      - Required
      - Default Value
      - Description
    * - |URL|
      - |checkmark|
      -
      - The URL of the remote Git repo to pull
    * - |Name|
      -
      - ``origin``
      - The name to use for the remote repo when pulling from it
    * - |Branch|
      -
      - The default branch in the repo
      - The branch of the remote Git repo to pull
    * - |username|
      -
      -
      - The username for authentication with the remote Git repo. Not needed when SSH with ``RSA`` key pair authentication is used.
    * - |password|
      -
      -
      - The password for authentication with the remote Git repo. Not needed when SSH with ``RSA`` key pair authentication is used.
    * - |path|
      -
      -
      - The SSH private key path, used only with SSH with ``RSA`` key pair authentication.
    * - |passphrase|
      -
      -
      - The SSH private key passphrase, used only with SSH ``withRSA`` key pair authentication.
    * - |failDep|
      -
      - ``true``
      - Enables failing a deployment when there's a processor failure.
    * - ``fastForwardMode``

        .. version_tag::
            :label: Since
            :version: 4.2.2

      -
      - ``FF``
      - The fast forward mode to use when pulling changes from the remote repo.
        Supported values are: ``FF``, ``NO_FF`` and ``FF_ONLY``.
        See the `jgit docs <https://javadoc.io/static/org.eclipse.jgit/org.eclipse.jgit/7.1.0.202411261347-r/org.eclipse.jgit/org/eclipse/jgit/api/MergeCommand.FastForwardMode.html>`__
        for more information on the supported fast forward mode values.
    * - ``mergeStrategy``

        .. version_tag::
            :label: Since
            :version: 4.2.2

      -
      - ``theirs``
      - The merge strategy to use.
        Supported values are: ``ours``, ``theirs``, ``simple-two-way-in-core``, ``resolve``, ``recursive``.
        See the `jgit docs <https://javadoc.io/static/org.eclipse.jgit/org.eclipse.jgit/7.1.0.202411261347-r/org.eclipse.jgit/org/eclipse/jgit/merge/MergeStrategy.html>`__
        for more information on the supported merge strategy values.
    * - ``contentMergeOption``

        .. version_tag::
            :label: Since
            :version: 4.2.2

      -
      - ``CONFLICT``
      - The content merge strategy to handle conflicts.
        Supported values are ``CONFLICT``, ``OURS``, ``THEIRS``, ``UNION``.
        See the `jgit docs <https://javadoc.io/static/org.eclipse.jgit/org.eclipse.jgit/7.1.0.202411261347-r/org.eclipse.jgit/org/eclipse/jgit/merge/ContentMergeStrategy.html>`__
        for more information on the supported content merge strategy values.

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *Git Pull Processor using basic auth*

  - processorName: gitPullProcessor
    remoteRepo:
      url: https://github.com/myuser/mysite.git
      branch: live
      username: myuser
      password: mypassword

|

.. code-block:: yaml
  :linenos:
  :caption: *Git Pull Processor using SSH with RSA key pair*

  - processorName: gitPullProcessor
    remoteRepo:
      url: https://github.com/myuser/mysite.git
      branch: live
      ssh:
        privateKey:
          path: /home/myuser/myprivatekey
          passphrase: mypassphrase

|

.. code-block:: yaml
    :linenos:
    :caption: *Git Pull Processor with merge options*

    - processorName: gitPullProcessor
      fastForwardMode: FF_ONLY
      contentMergeOption: CONFLICT
      mergeStrategy: theirs
      remoteRepo:
        url: ......../repos/sites/ed1/published
        branch: live

|

.. _deployer-git-diff-processor:

""""""""""""""""""
Git Diff Processor
""""""""""""""""""
Processor that, based on a previously processed commit that's stored, does a diff with the current commit of the
deployment, to find out the change-set. If there is no previously processed commit, then the entire repository becomes
the change-set.

.. note::
  This processor needs to be placed after the ``gitPullProcessor`` and before any other processor like the
  ``searchIndexingProcessor``

**Properties**

+---------------------+---------+-------------+---------------------------------------------------------------------+
|Name                 |Required |Default Value|Description                                                          |
+=====================+=========+=============+=====================================================================+
|``includeGitLog``    |         |``false``    |Indicates if the git log details should be included in the change set|
+---------------------+---------+-------------+---------------------------------------------------------------------+
|``updateCommitStore``|         |``true``     |Indicates if the processed commit value should be modified           |
+---------------------+---------+-------------+---------------------------------------------------------------------+
||failDep|            |         |``true``     |Enables failing a deployment when there's a processor failure        |
+---------------------+---------+-------------+---------------------------------------------------------------------+

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *Git Diff Processor*

  - processorName: gitDiffProcessor
    includeGitLog: true


.. _deployer-git-push-processor:

""""""""""""""""""
Git Push Processor
""""""""""""""""""
Processor that pushes a local repo to a remote Git repository.

**Properties**

+------------+-----------+-------------------------------+------------------------------------------------------------+
|Name        |Required   |Default Value                  |Description                                                 |
+============+===========+===============================+============================================================+
||lBranch|   ||checkmark||                               |The branch of the local repo to push                        |
+------------+-----------+-------------------------------+------------------------------------------------------------+
||URL|       ||checkmark||                               |The URL of the remote Git repo to push to                   |
+------------+-----------+-------------------------------+------------------------------------------------------------+
||Branch|    |           |The default branch in the repo |The branch of the remote Git repo to push to                |
+------------+-----------+-------------------------------+------------------------------------------------------------+
||username|  |           |                               |The username for authentication with the remote Git repo.   |
|            |           |                               |Not needed when SSH with RSA key pair authentication is used|
+------------+-----------+-------------------------------+------------------------------------------------------------+
||password|  |           |                               |The password for authentication with the remote Git repo.   |
|            |           |                               |Not needed when SSH with RSA key pair authentication is used|
+------------+-----------+-------------------------------+------------------------------------------------------------+
||path|      |           |                               |The SSH private key path, used only with SSH with RSA key   |
|            |           |                               |pair authentication                                         |
+------------+-----------+-------------------------------+------------------------------------------------------------+
||passphrase||           |                               |The SSH private key passphrase, used only with SSH withRSA  |
|            |           |                               |key pair authentication                                     |
+------------+-----------+-------------------------------+------------------------------------------------------------+
|``force``   |           |``false``                      |Sets the force preference for the push                      |
+------------+-----------+-------------------------------+------------------------------------------------------------+
|``pushAll`` |           |``false``                      |If all local branches should be pushed to the remote        |
+------------+-----------+-------------------------------+------------------------------------------------------------+

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *Git Push Processor using basic auth*

  - processorName: gitPushProcessor
    remoteRepo:
      url: https://github.com/myuser/mysite.git
      branch: deployed
      username: myuser
      password: mypassword

.. code-block:: yaml
  :linenos:
  :caption: *Git Push Processor using SSH with RSA key pair*

  - processorName: gitPushProcessor
    remoteRepo:
      url: https://github.com/myuser/mysite.git
      branch: deployed
      ssh:
        privateKey:
          path: /home/myuser/myprivatekey
          passphrase: mypassphrase

.. _deployer-git-update-commit-id-processor:

""""""""""""""""""""""""""""""
Git Update Commit Id Processor
""""""""""""""""""""""""""""""
Processor that updates the processed commits value with the current commit.

**Example**

.. code-block:: yaml
    :linenos:
    :caption: *Git Update Commit Id Processor*

    - processorName: gitUpdateCommitIdProcessor

.. _deployer-script-processor:

"""""""""""""""""""""""
Groovy Script Processor
"""""""""""""""""""""""
A custom Groovy processor that can process published content.

**Properties**

+------------+-----------+-------------------------------+------------------------------------------------------------+
|Name        |Required   |Default Value                  |Description                                                 |
+============+===========+===============================+============================================================+
|scriptPath  ||checkmark||                               |The relative path of the script to execute                  |
+------------+-----------+-------------------------------+------------------------------------------------------------+

.. note::  The default path scripts are loaded from is ``$CRAFTER_HOME/bin/crafter-deployer/processors/scripts``

**Example**

.. code-block:: yaml
    :linenos:
    :caption: *Groovy Script Processor*

    - processorName: scriptProcessor
      scriptPath: 'myscripts/mychanges.groovy'

|

The following variables are available for use in your scripts:

==================  ===========
Variable Name       Description
==================  ===========
logger              The processor's logger, http://www.slf4j.org/api/org/slf4j/Logger.html
applicationContext  The application context of the current target, https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContext.html
deployment          The current deployment, :javadoc_base_url:`deployer/org/craftercms/deployer/api/Deployment.html`
execution           The execution for this processor, :javadoc_base_url:`deployer/org/craftercms/deployer/api/ProcessorExecution.html`
filteredChangeSet   A subset of ``originalChangeSet`` that matches the ``includeFiles`` pattern and not the ``excludeFiles`` pattern for this processor, :javadoc_base_url:`deployer/org/craftercms/deployer/api/ChangeSet.html`
originalChangeSet   The original change set returned by the previous processors in the pipeline, :javadoc_base_url:`deployer/org/craftercms/deployer/api/ChangeSet.html`
==================  ===========

|
|

Let's take a look at an example script that you can use for the Groovy script processor.
Below is a script that only includes a file from the change-set if a parameter is present in the deployment:

.. code-block:: groovy
   :caption: *Example Groovy script to be run by a script processor*
   :linenos:

   import org.craftercms.deployer.api.ChangeSet

   logger.info("starting script execution")

   def specialFile = "/site/website/expensive-page-to-index.xml"

   // if the file has been changed but the param was not sent then remove it from the change set
   if (originalChangeSet.getUpdatedFiles().contains(specialFile) && !deployment.getParam("index_expensive_page")) {
       originalChangeSet.removeUpdatedFile(specialFile)
   }

   // return the new change set
   return originalChangeSet

|

Here's another example script that pushes content (uploads a document) to an external service upon publish.
In this example, for every newly created file and upon publish (changeset), if the path is a static asset (path starts with "/static-assets"),
it reads the file from the content store and uploads it (multipart/form-data) to a third‑party HTTP API using OkHttp,
streaming the file upload so the file is read while sending.

.. raw:: html

   <details>
   <summary><a>Sample script to upload document to an external service</a></summary>

.. code-block:: groovy
    :caption: *Example Groovy script to be run by a script processor*
    :linenos:

    @Grab(group='com.squareup.okhttp3', module='okhttp', version='4.12.0', initClass=false)

    import okhttp3.MediaType
    import okhttp3.MultipartBody
    import okhttp3.OkHttpClient
    import okhttp3.Request
    import okhttp3.RequestBody
    import okhttp3.Response

    import okio.BufferedSink
    import okio.Okio

    import java.util.concurrent.TimeUnit

    // =====================================================
    // Config
    // =====================================================
    def token = applicationContext.getEnvironment().getProperty('target.myservice.token')

    def agentId  = applicationContext.getEnvironment().getProperty('target.myservice.agentId')

    OkHttpClient client = new OkHttpClient.Builder()
            .connectTimeout(30, TimeUnit.SECONDS)
            .readTimeout(300, TimeUnit.SECONDS)
            .writeTimeout(300, TimeUnit.SECONDS)
            .build()

    // =====================================================
    // Helpers
    // =====================================================
    class ContentAccessHelper {
        def contentStoreService
        def context
        def remoteAssetPattern = ""

        def ContentAccessHelper(contentStoreService, context) {
            this.contentStoreService = contentStoreService
            this.context = context
        }

        /**
         * Get the input stream of an asset
         *  This service should be used to get asset input streams for the following 3 reasons:
         * 1. This code works with blob store and without
         * 2. This code does not assume direct access to system resources (which is disabled for scripting)
         * 3. This code future-proofs your code for other repository updates
         */
        def retrieveStaticAsset(binaryPath) throws Exception {

            // item is in our repository
            def binaryContent = contentStoreService.findContent(this.context, binaryPath)

            if(binaryContent != null) {
                return binaryContent
            }
            else {
                throw new Exception("Content at path returned null via findContent {}", binaryPath)
            }
        }

        def pathToFilename(contentPath) {
            return contentPath.substring(contentPath.lastIndexOf("/")+1)
        }

        /**
         * @return true if path is an asset
         */
        def isAsset(contentPath) {
            return (contentPath) ? contentPath.startsWith("/static-assets") : false
        }
    }

    /**
     * Upload the document to Acme Service (Example an LLM/Rag framework) (streaming)
     */
    def addDocumentToMyThirdPartyService(agentId, fileName, fileLength, fileInputStream, client, token) {

        MediaType MEDIA_TYPE_OCTET = MediaType.parse("application/octet-stream")

        // Streaming RequestBody: OkHttp will read from disk as it writes to the network
        RequestBody fileBody = new RequestBody() {
            @Override
            MediaType contentType() {
                return MEDIA_TYPE_OCTET
            }

            @Override
            long contentLength() throws IOException {
                return fileLength
            }

            @Override
            void writeTo(BufferedSink sink) throws IOException {
                def source = Okio.source(fileInputStream)
                try {
                    sink.writeAll(source)
                } finally {
                    fileInputStream.close()
                }
            }
        }

        // Multipart body (form + file)
        RequestBody requestBody = new MultipartBody.Builder()
                .setType(MultipartBody.FORM)
                // .addFormDataPart("description", "A file description here")
                .addFormDataPart("file", fileName, fileBody)
                .build()

        // Third Party URL
        String sourceUrl = "https://api.mythirdparty.com/v1/agents/${agentId}/sources"

        // Build the request
        Request request = new Request.Builder()
                .url(sourceUrl)
                .post(requestBody)
                .header("Authorization", "Bearer "+token)
                .build()

        // Execute synchronously
        Response response = client.newCall(request).execute()

        try {
            String body = response.body()?.string()
            println "Status: ${response.code()}"
            println "Response body:\n${body}"
            // do something or return success/failure
            // this is a happy path example
        } finally {
            response.close()
        }
    }

    /**
     * Process a changeset
     */
    def processChangeSet(changeSet, contentAccessHelper, token, agentId, client) {
        changeSet.getCreatedFiles().each { itemPath ->
            if(contentAccessHelper.isAsset(itemPath)) {

                def asset = contentAccessHelper.retrieveStaticAsset(itemPath)
                def fileName = contentAccessHelper.pathToFilename(itemPath)
                def fileLength = asset.getLength()
                def fileInputStream = asset.getInputStream()

                addDocumentToMyThirdPartyService(agentId, fileName, fileLength, fileInputStream, client, token)
            }
            else {
                println "Not a document (skipping): ${itemPath}"
            }
        }

        // changeSet.getUpdatedFiles().each { itemPath ->
        //     Don't do anything for now
        // }

        // changeSet.getDeletedFiles().each { itemPath ->
        //     Don't do anything for now
        // }

    }

    // =====================================================
    // Main flow
    // =====================================================
    def contextFactory = applicationContext.getBean("contextFactory")
    def contentStoreService = applicationContext.getBean("crafter.contentStoreService")
    def context = contextFactory.getObject()
    def contentAccessHelper = new ContentAccessHelper(contentStoreService, context)

    processChangeSet(originalChangeSet, contentAccessHelper, token, agentId, client)

    return originalChangeSet

.. raw:: html

   </details>

|

"""""""""""""""""""""""""""""""""""""
File Based Deployment Event Processor
"""""""""""""""""""""""""""""""""""""
Processor that triggers a deployment event that consumers of the repository (Crafter Engine instances) can subscribe to by
reading a specific file from the repository.

**Properties**

+---------------------------+-----------+--------------------------------+-------------------------------------------+
|Name                       |Required   |Default Value                   |Description                                |
+===========================+===========+================================+===========================================+
|``deploymentEventsFileUrl``|           |``deployment-events.properties``|Relative path of the deployment events file|
+---------------------------+-----------+--------------------------------+-------------------------------------------+
|``eventName``              ||checkmark||                                |Name of the event to trigger               |
+---------------------------+-----------+--------------------------------+-------------------------------------------+

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *File Based Deployment Event Processor*

  - processorName: fileBasedDeploymentEventProcessor
    eventName: 'events.deployment.rebuildContext'

.. _deployer-command-line-processor:

""""""""""""""""""""""
Command Line Processor
""""""""""""""""""""""
Processor that runs a command line process (e.g. a shell script).

**Properties**

+----------------------+-----------+--------------------+-------------------------------------------------------+
|Name                  |Required   |Default Value       |Description                                            |
+======================+===========+====================+=======================================================+
|``workingDir``        |           |Deployer's directory|The directory from which the process will run          |
+----------------------+-----------+--------------------+-------------------------------------------------------+
|``command``           ||checkmark||                    |The full command that the process will run             |
+----------------------+-----------+--------------------+-------------------------------------------------------+
|``processTimeoutSecs``|           |``30``              |The amount of seconds to wait for the process to finish|
+----------------------+-----------+--------------------+-------------------------------------------------------+
|``includeChanges``    |           |``false``           |Additional parameters will be added to the command     |
|                      |           |                    |                                                       |
|                      |           |                    ||includeChangesTrue|                                   |
+----------------------+-----------+--------------------+-------------------------------------------------------+

.. |includeChangesTrue| replace:: **Example:** script.sh SITE_NAME OPERATION (CREATE | UPDATE | DELETE) FILE (relative path of the file)

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *Command Line Processor*

  - processorName: commandLineProcessor
    workingDir: '/home/myuser/myapp/bin'
    command: 'myapp -f --param1=value1'


.. _deployer-search-indexing-processor:

"""""""""""""""""""""""""
Search Indexing Processor
"""""""""""""""""""""""""
Processor that indexes the files on the change-set, using one or several BatchIndexer. After the files have been
indexed it submits a commit.

**Properties**

+----------------------------------+--------+---------------------+---------------------------------------------------+
|Name                              |Required|Default Value        |Description                                        |
+==================================+========+=====================+===================================================+
|``ignoreIndexId``                 |        |``false``            |If the index ID should be ignored                  |
+----------------------------------+--------+---------------------+---------------------------------------------------+
|``indexId``                       |        |Value of ``siteName``|The specific index ID to use                       |
+----------------------------------+--------+---------------------+---------------------------------------------------+
|``reindexItemsOnComponentUpdates``|        |``true``             |Flag that indicates that if a component is updated,|
|                                  |        |                     |all other pages and components that include it     |
|                                  |        |                     |should be updated too                              |
+----------------------------------+--------+---------------------+---------------------------------------------------+

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *Search Indexing Processor*

  - processorName: searchIndexingProcessor

""""""""""""""""""""""""""
HTTP Method Call Processor
""""""""""""""""""""""""""
Processor that does a HTTP method call.

**Properties**

+----------+-----------+-------------+---------------+
|Name      |Required   |Default Value|Description    |
+==========+===========+=============+===============+
|``url``   ||checkmark||             |The URL to call|
+----------+-----------+-------------+---------------+
|``method``||checkmark||             |The HTTP method|
+----------+-----------+-------------+---------------+

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *HTTP Method Call Processor*

  - processorName: httpMethodCallProcessor
    method: GET
    url: 'http://localhost:8080/api/1/site/cache/clear.json?crafterSite=mysite'

"""""""""""""""
Delay Processor
"""""""""""""""
Processor that pauses the pipeline execution for a given number of seconds.

**Properties**

+-----------+--------+-------------+-------------------------+
|Name       |Required|Default Value|Description              |
+===========+========+=============+=========================+
|``seconds``|        |``5``        |Amount of seconds to wait|
+-----------+--------+-------------+-------------------------+

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *Delay Processor*

  - processorName: delayProcessor
    seconds: 10

.. _deployer-target-find-replace-processor:

""""""""""""""""""""""""""
Find And Replace Processor
""""""""""""""""""""""""""
Processor that replaces a pattern on the content of the created or updated files.

.. warning::
  The files changed by this processor will not be committed to the git repository and will be discarded when the next
  deployment starts.

**Properties**

+---------------+-----------+-------------+--------------------------------------------------------------+
|Name           |Required   |Default Value|Description                                                   |
+===============+===========+=============+==============================================================+
|``textPattern``||checkmark||             |Regular expression to search in files                         |
+---------------+-----------+-------------+--------------------------------------------------------------+
|``replacement``||checkmark||             |Expression to replace the matches                             |
+---------------+-----------+-------------+--------------------------------------------------------------+
||failDep|      |           |``true``     |Enables failing a deployment when there's a processor failure |
+---------------+-----------+-------------+--------------------------------------------------------------+

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *Find And Replace Processor*

  - processorName: findAndReplaceProcessor
    textPattern: (/static-assets/[^&quot;&lt;]+)
    replacement: 'http://mycdn.com$1'

""""""""""""""
AWS Processors
""""""""""""""
All deployment processors related to AWS services support the following properties:

+-------------+-----------+---------------------------+-------------------------------------------------------------+
|Name         |Required   |Default Value              |Description                                                  |
+=============+===========+===========================+=============================================================+
|``region``   |           |If not provided the AWS SDK|The AWS Region                                               |
+-------------+-----------+                           +-------------------------------------------------------------+
|``accessKey``|           |default providers will be  |The AWS Access Key                                           |
+-------------+-----------+                           +-------------------------------------------------------------+
|``secretKey``|           |used                       |The AWS Secret Key                                           |
+-------------+-----------+---------------------------+-------------------------------------------------------------+
|``url``      ||checkmark||                           |AWS S3 bucket URL to upload files                            |
+-------------+-----------+---------------------------+-------------------------------------------------------------+
||failDep|    |           |``true``                   |Enables failing a deployment when there's a processor failure|
+-------------+-----------+---------------------------+-------------------------------------------------------------+

|

.. _deployer-s3-sync-processor:

~~~~~~~~~~~~~~~~~
S3 Sync Processor
~~~~~~~~~~~~~~~~~
Processor that syncs files to an AWS S3 Bucket.

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *S3 Sync Processor*

  - processorName: s3SyncProcessor
    url: s3://serverless-sites/site/mysite


.. |defaultS3E| replace:: ``deployment-events.properties``

.. _deployer-s3-deployment-events-processor:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
S3 Deployment Events Processor
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Processor that uploads the deployment events to an AWS S3 Bucket

**Properties**

+---------------------------+-----------+------------------+----------------------------------------------------------+
|Name                       |Required   |Default Value     |Description                                               |
+===========================+===========+==================+==========================================================+
|``deploymentEventsFileUrl``|           ||defaultS3E|      |URL of the deployment events file, relative to the local  |
|                           |           |                  |git repo                                                  |
+---------------------------+-----------+------------------+----------------------------------------------------------+

**Example**

.. code-block:: yaml
    :linenos:
    :caption: *S3 Deployment Events Processor*

    - processorName: s3DeploymentEventsProcessor
      region: ${aws.region}
      accessKey: ${aws.accessKey}
      secretKey: ${aws.secretKey}
      url: {{aws.s3.url}}

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Cloudfront Invalidation Processor
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Processor that invalidates the changed files in the given AWS Cloudfront distributions.

**Properties**

+-----------------+-----------+-------------+-------------------------+
|Name             |Required   |Default Value|Description              |
+=================+===========+=============+=========================+
|``distributions``||checkmark||             |List of distributions ids|
+-----------------+-----------+-------------+-------------------------+

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *Cloud Front Invalidation Processor*

  - processorName: cloudfrontInvalidationProcessor
    distributions:
      - E15UHQPTKROC8Z

^^^^^^^^^^^^^^^^^^^^^^^^^^
Post Deployment Processors
^^^^^^^^^^^^^^^^^^^^^^^^^^
The post deployment processors assume that all changed files have been handled and the result of the deployment is
already known (either successful or failed) and take actions based on those results, because of that, these processors need to be
placed after all main deployment processors to work properly.

"""""""""""""""""""""
File Output Processor
"""""""""""""""""""""
Post processor that writes the deployment result to an output CSV file under ``CRAFTER_HOME/logs/deployer`` for later access, whenever a deployment fails or
files were processed.

**Example**

.. code-block:: yaml
  :linenos:
  :caption: *File Output Processor*

  - processorName: fileOutputProcessor

.. _deployer-notification-processors:

"""""""""""""""""""""""
Notification Processors
"""""""""""""""""""""""
All deployment processors related to notification processors support the following properties:

.. list-table::
    :header-rows: 1
    :widths: 20 5 15 60

    * - Name
      - Required
      - Default Value
      - Description
    * - ``templateName``
      -
      -
      - The name of the Freemarker template used for email creation.
    * - ``serverName``
      -
      -
      - The hostname of the email server.
    * - ``status``
      -
      - ``SUCCESS``
      - The status condition that triggers the notification.
        Possible values are:

        - **SUCCESS**: Notifications sent for successful deployments
        - **ON_ANY_STATUS**: Notifications sent for all deployments
        - **ON_ANY_FAILURE**: Notifications sent for deployments where at least one processor has failed
        - **ON_TOTAL_FAILURE**: Notifications will be sent for deployments in which the general status indicates failure

    * - ``failedProcessors``

        .. version_tag::
            :label: Since
            :version: 4.2.2

      -
      -
      - A regex pattern to match the failed processors name that trigger the notification.
    * - ``mutePeriodMinutes``

        .. version_tag::
            :label: Since
            :version: 4.2.2

      -
      - ``0``
      - The number of minutes to wait before sending another notification for the same processor.
    * - ``lastDateFilenameSuffix``

        .. version_tag::
            :label: Since
            :version: 4.2.2

      -
      - ``-lastNotification``
      - The suffix to use when creating the last notification date file. This allows multiple notification processors
        for the same target.
    * - ``dateTimePattern``
      -
      -
      - The date time pattern to use when specifying a date in the message.

.. _deployer-mail-notification-processor:

~~~~~~~~~~~~~~~~~~~~~~~~~~~
Mail Notification Processor
~~~~~~~~~~~~~~~~~~~~~~~~~~~
Post processor that sends an email notification with the result of a deployment, whenever a deployment fails or files
were processed. The output file generated by the ``fileOutputProcessor`` is attached if it's available.
To change the location of mail templates, see the corresponding section in :ref:`deployer-notification-templates-override-location`

**Properties**

+-------------------+-----------+-------------------------------+-----------------------------------------------------+
|Name               |Required   |Default Value                  |Description                                          |
+===================+===========+===============================+=====================================================+
|``templateName``   |           |``default``                    |The name of the Freemarker template used for email   |
|                   |           |                               |creation                                             |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
|``from``           |           |``noreply@example.com``        |The value of the From field in the emails            |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
|``to``             ||checkmark||                               |The value of the To field in the emails              |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
|``subject``        |           |``Deployment Report``          |The value of the Subject field in the emails         |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
|``html``           |           |``true``                       |Whether the emails are HTML                          |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
|``serverName``     |           |Current local host name        |The hostname of the email server                     |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
|``dateTimePattern``|           |``MM/dd/yyyy hh:mm:ss.SSS a z``|The date time pattern to use when specifying a date  |
|                   |           |                               |in the email                                         |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
|``status``         |           |``SUCCESS``                    |The status condition that triggers the notification. |
|                   |           |                               |See above for possible values.                       |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
||failedProc|       |           |                               |A regex pattern to match the failed processors name  |
|                   |           |                               |that trigger the notification.                       |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
||mutePdMin|        |           |``0``                          |The number of minutes to wait before sending another |
|                   |           |                               |notification for the same processor.                 |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+
||lastDateFNSuf|    |           |``-lastNotification``          |The suffix to use when creating the last notification|
|                   |           |                               |date file.                                           |
+-------------------+-----------+-------------------------------+-----------------------------------------------------+

.. |failedProc| replace:: ``failedProcessors``
.. |mutePdMin| replace:: ``mutePeriodMinutes``
.. |lastDateFNSuf| replace:: ``lastDateFilenameSuffix``

**Examples**

.. code-block:: yaml
    :linenos:
    :caption: *Mail Notification Processor for any failure*

    - processorName: mailNotificationProcessor
      to:
        - admin@example.com
        - author@example.com
      status: ON_ANY_FAILURE

.. code-block:: yaml
     :linenos:
     :caption: *Mail Notification Processor with mute period*

     - processorName: mailNotificationProcessor
       to:
         - test@example.com
       mutePeriodMinutes: 2
       lastDateFilenameSuffix: -error

|

.. _deployer-webhook-notification-processor:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Webhook Notification Processor
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. version_tag::
    :label: Since
    :version: 4.2.2

Post processor that sends a webhook notification with the result of the deployment.
The processor will be included in remote target template if ``webhook_url`` param is provided to the add target API. See
the example below.

A default template for webhook notifications ``templates/webhook/slack-template.ftl`` is provided out-of-the-box.
To change the location of webhook templates, see the corresponding section in :ref:`deployer-notification-templates-override-location`

**Properties**

.. list-table::
    :header-rows: 1
    :widths: 20 10 10 60

    * - Name
      - Required
      - Default Value
      - Description
    * - ``url``
      - |checkmark|
      -
      - The URL to send the webhook notification to.
    * - ``method``
      -
      - ``post``
      - The HTTP method to use.
    * - ``contentType``
      -
      - ``application/json``
      - The content type of the request body.

**Example**

.. code-block:: yaml
    :linenos:
    :caption: *Webhook Notification Processor*

    {{#if webhook_url}}
    - processorName: webhookNotificationProcessor
      status: ON_ANY_FAILURE
      mutePeriodMinutes: {{#if mute_period_minutes}}{{mute_period_minutes}}{{else}}60{{/if}}
      url: {{webhook_url}}
      method: POST
    {{/if}}

^^^^^^^^^^^^^^^^^^^^^
Full Pipeline Example
^^^^^^^^^^^^^^^^^^^^^
The following example shows how the deployment processors work together to deliver a serverless site using AWS services.

.. raw:: html

    <details>
    <summary><a>Example serverless delivery pipeline</a></summary>

.. code-block:: yaml
  :linenos:
  :caption: *Serverless Delivery Pipeline*

  pipeline:
    # -------------------- START OF MAIN PIPELINE --------------------

    # First clone or update the local repository from github
    - processorName: gitPullProcessor
      remoteRepo:
        url: https://github.com/myuser/mysite.git
        branch: live
        username: myuser
        password: my_secret_password

    # Then find the added/changed/deleted files since the previous pull (if any)

    - processorName: gitDiffProcessor

    # Change all references to static-assets to use a CDN URL instead of the local URL
    - processorName: findAndReplaceProcessor
      includeFiles: ['^/site/.*$', '^/templates/.*$', '^/static-assets/.*(js|css|html)$']
      textPattern: (/static-assets/[^&quot;&lt;]+)
      replacement: 'http://d111111abcdef8.cloudfront.net$1'

    # Index the changes in search
    - processorName: searchIndexingProcessor

    # Sync the changes in a S3 bucket
    - processorName: s3SyncProcessor
      url: s3://serverless-sites/site/mysite

    # Add a small delay to allow the S3 changes to propagate
    - processorName: delayProcessor

    # Invalidate the changed files in the CDN
    - processorName: cloudfrontInvalidationProcessor
      includeFiles: ['^/static-assets/.*$']
      distributions:
        - E15UHQPTKROC8Z

    # Trigger deployment events so any Crafter Engine listening can update accordingly:
    # Rebuild the site context if any config or script has changed
    - processorName: fileBasedDeploymentEventProcessor
      includeFiles: ["^/?config/.*$", "^/?scripts/.*$"]
      excludeFiles: ['^/config/studio/content-types/.*$']
      eventName: 'events.deployment.rebuildContext'

    # Clear the cache if any static-asset has changed
    - processorName: fileBasedDeploymentEventProcessor
      excludeFiles: ['^/static-assets/.*$']
      eventName: 'events.deployment.clearCache'

    # Rebuild the GraphQL schema if any content-type has changed
    - processorName: fileBasedDeploymentEventProcessor
      includeFiles: ['^/config/studio/content-types/.*$']
      eventName: 'events.deployment.rebuildGraphQL'

    # Push the updated events to the S3 bucket
    - processorName: s3SyncProcessor
      includeFiles: ['^/?deployment-events\.properties$']
      url: s3://serverless-sites/site/mysite

    # -------------------- END OF MAIN PIPELINE --------------------
    # Only Post Processors can be in this section

    # Record the result of the deployment to a CSV file
    - processorName: fileOutputProcessor

    # Notify the site admin & an author if there were any failures during the deployment
    - processorName: mailNotificationProcessor
      to:
        - admin@example.com
        - author@example.com
      status: ON_ANY_FAILURE

.. raw:: html

    </details>

|

|hr|

.. _custom-processors:

^^^^^^^^^^^^^^^^^
Custom Processors
^^^^^^^^^^^^^^^^^
Crafter Deployer can be easily configured to match different needs but in case additional features are needed it is
also possible to include custom libraries by following this guide:

"""""""""""""""""""""""""""""""""""
Step 1: Create the custom processor
"""""""""""""""""""""""""""""""""""
Custom processors are completely free to use any third party library or SDK, the only requisite is to define a class
that implements the ``DeploymentProcessor`` interface.

.. note::
    It is highly recommended to extend ``AbstractDeploymentProcessor`` or ``AbstractMainDeploymentProcessor`` instead of
    just implementing the interface.

These classes can be accessed by adding a dependency in your project:

.. code-block:: xml

    <dependency>
      <groupId>org.craftercms</groupId>
      <artifactId>crafter-deployer</artifactId>
      <version>${craftercms.version}</version>
    </dependency>

""""""""""""""""""""""""""""""""
Step 2: Add the custom processor
""""""""""""""""""""""""""""""""
Custom processors are included to the Crafter Deployer classpath by adding all the required jar files in the following
folder:

  ``INSTALL_DIR/bin/crafter-deployer/lib``

.. note::
    Make sure to carefully review all other dependencies in your project to make sure there are no conflicts with
    the libraries used by Crafter Deployer or any other custom processor.

""""""""""""""""""""""""""""""""""""""
Step 3: Configure the custom processor
""""""""""""""""""""""""""""""""""""""
Once the custom processor is placed in the classpath, the only remaining step is to create or update a target to use it.
All configuration files for targets will be placed in the following folder:

  ``INSTALL_DIR/data/deployer/targets``

First you need to create or update a context file to define all beans required by the custom processor, the file should
have the name ``{site}-{env}-context.xml``:

.. code-block:: xml

    <bean id="externalService" class="com.example.Service">
      <property name="url" value="${service.url}"/>
      <property name="port" value="${service.port}"/>
    </bean>

    <bean id="myCustomProcessor" class="com.example.CustomProcessor" parent="deploymentProcessor">
      <property name="service" ref="externalService"/>
    </bean>

.. note::
    The parent bean is provided by Crafter Deployer and it includes common configuration used by the
    ``AbstractDeploymentProcessor`` and ``AbstractMainDeploymentProcessor`` classes.

Once the bean has been defined it can be added to the target's pipeline in the yaml file with the matching name
``{site}-{env}.yaml``:

.. code-block:: yaml

    target:
      env: preview
      siteName: mySite
      deployment:
        scheduling:
          enabled: false
        pipeline:
          - processorName: myCustomProcessor
            username: john
            password: passw0rd!
    service:
      url: http://www.example.com
      port: 8080


Any change in the classpath will require a restart of Crafter Deployer, changes in configuration files will be
applied when the target is reloaded.

"""""""
Example
"""""""
Let's take a look at an example of creating and configuring your own script deployment processor that lists the
approximate number of bytes for created and updated files. We'll use a site created using the website editorial
blueprint named ``ed``

First, we'll create our custom deployment processor. We'll then look at how to configure our custom deployment processor
in a target.

For this example, to be able to access the number of bytes of the files being created and updated, we'll need a way to
access those files securely since the scripting sandbox does not give you direct access to files on the operating system.
Note also that files may be deployed in various ways in CrafterCMS. It could be on the file system, or it could be
"blobs", an xml file that points to where the actual content is, such as a S3 bucket.

There's an out-of-the-box service that does everything that we just mentioned called the content store service that we
can use. To get the content store service in our deployer processor, we'll perform the following actions against Spring:

.. code-block:: groovy
    :caption: *To get access to the out-of-the-box content store service, add the following:*

    def contextFactory = applicationContext.getBean("contextFactory")
    def contentStoreService = applicationContext.getBean("crafter.contentStoreService")
    def context = contextFactory.getObject()


Here's our custom deployment processor ``ContentAccessExample.groovy`` showing you how to use the content store service
to retrieve static assets and display the number of bytes for created and updated files:

.. raw:: html

    <details>
    <summary><a>Custom Processor Example</a></summary>

.. code-block:: groovy
    :linenos:
    :caption: *CRAFTER_HOME/bin/crafter-deployer/ContentAccessExample.groovy*
    :emphasize-lines: 1-3, 7, 18, 35, 76

    def contextFactory = applicationContext.getBean("contextFactory")
    def contentStoreService = applicationContext.getBean("crafter.contentStoreService")
    def context = contextFactory.getObject()

    def contentAccessHelper = new ContentAccessHelper(contentStoreService, context)

    def someTargValue = applicationContext.getEnvironment().getProperty('target.myCustomParams.myParam')

    logger.info("Invoking example deployer processor")
    logger.info("Target value: {}", someTargValue)

    for(path : originalChangeSet.getCreatedFiles()) {
        if(contentAccessHelper.isAsset(path)) {
            def is

            try {
                is = contentAccessHelper.retrieveStaticAsset(path)
                logger.info("CREATE w bytes: {} {}", path, is.available())
            }
            finally {
                if(is) is.close()
            }
        }
    }

    for(path : originalChangeSet.getUpdatedFiles()) {
        // Note: Update is only called if the file is different.
        // The deployer ignores repeated deployments of the same file

        if(contentAccessHelper.isAsset(path)) {
            def is

            try {
                is = contentAccessHelper.retrieveStaticAsset(path)
                logger.info("UPDATE w bytes: {} {}", path, is.available())
            }
            finally {
                if(is) is.close()
            }
        }
    }

    // don't do anything with deleted files
    // for(path : originalChangeSet.getDeletedFiles()) {
    // }

    logger.info("Invoking example deployer processor complete")


    /**
     * Helper class for dealing with assets
     */
    protected class ContentAccessHelper {
        def contentStoreService
        def context
        def remoteAssetPattern = ""

        def ContentAccessHelper(contentStoreService, context) {
            this.contentStoreService = contentStoreService
            this.context = context
        }

        /**
         * get the input stream of an asset
         *  This service should be used to get asset input streams for the following 3 reasosns:
         * 1. This code works with blob store and without
         * 2. This code does not assume direct access to system resources (which is
         * disabled for scripting)
          * 3. This code future proofs your code for other repository updates
         */
        def retrieveStaticAsset(binaryPath)
        throws Exception {

            if(!isRemoteAsset(binaryPath)) {
                // item is in our repository
                def binaryContent = contentStoreService.findContent(this.context, binaryPath)

                if(binaryContent != null) {
                    return binaryContent.getInputStream()
                }
                else {
                    throw new Exception("Content at path returned null via findContent {}", binaryPath)
                }
            }
            else {
                // item is remote
                throw new Exception("Remote assets not supported by processor at this time {}", binaryPath)
            }
        }

        /**
         * @return true if asset is a remote asset
         */
        def isRemoteAsset(path) {
            return false
        }

        /**
         * @return true if path is an asset
         */
        def isAsset(contentPath) {
            return (contentPath) ? contentPath.startsWith("/static-assets") : false
        }
    }

.. raw:: html

   </details>

Finally, to use the custom processor above, we'll need to configure it in a target. For our example, we'll configure
it in the authoring target ``ed-authoring.yaml`` like below:

.. raw:: html

    <details>
    <summary><a>Configuring your custom processor in a target</a></summary>

.. code-block:: yaml
    :linenos:
    :emphasize-lines: 17-22
    :caption: *CRAFTER_HOME/data/deployer/targets/ed-authoring.yaml*

    version: 4.1.3.0
    target:
    env: preview
    siteName: ed
    localRepoPath: CRAFTER_HOME/data/repos/sites/ed/sandbox

    myCustomParams:
        myParam: "a value"

    search:
        indexIdFormat: '%s-authoring'
    deployment:
        scheduling:
        enabled: false
        pipeline:
        - processorName: gitDiffProcessor
        - processorName: scriptProcessor
          scriptPath: 'CRAFTER_HOME/bin/crafter-deployer/ContentAccessExample.groovy'
          excludeFiles:
          - ^/.*\.keep$
          includeFiles: ["^/site/website/.*$", "^/static-assets/.*$"]
          failDeploymentOnFailure: true
        - processorName: searchIndexingProcessor
          excludeFiles:
          - ^/sources/.*$
        - processorName: httpMethodCallProcessor
          method: GET
          url: ${target.engineUrl}/api/1/site/cache/clear.json?crafterSite=${target.siteName}&token=${target.engineManagementToken}
        - processorName: httpMethodCallProcessor
          includeFiles:
          - ^/?config/studio/content-types.*$
          method: GET
          url: ${target.engineUrl}/api/1/site/context/graphql/rebuild.json?crafterSite=${target.siteName}&token=${target.engineManagementToken}
        - processorName: fileOutputProcessor
          processorLabel: fileOutputProcessor

.. raw:: html

   </details>

After configuring our custom deployment processor, remember to restart your install for the changes to take effect.
We'll now test our custom deployment processor by logging in to Studio and uploading an image under
``/static-assets/images``. To check that our custom processor ran, open the deployer log under
``CRAFTER_HOME/logs/deployer/crafter-deployer.out`` and look for the lines we log in our custom processor:

.. code-block:: text
    :linenos:
    :emphasize-lines: 9-14
    :caption: *Crafter Deployer log - CRAFTER_HOME/logs/deployer/crafter-deployer.out*

    2025-05-07 10:48:01.473  INFO 72679 --- [deployment-3] org.craftercms.deployer.impl.TargetImpl  : ============================================================
    2025-05-07 10:48:01.475  INFO 72679 --- [deployment-3] org.craftercms.deployer.impl.TargetImpl  : Deployment for ed-authoring started
    2025-05-07 10:48:01.476  INFO 72679 --- [deployment-3] org.craftercms.deployer.impl.TargetImpl  : ============================================================
    2025-05-07 10:48:01.477  INFO 72679 --- [deployment-3] l.processors.AbstractDeploymentProcessor : ----- < gitDiffProcessor @ ed-authoring > -----
    ...
    2025-05-07 10:48:02.777  INFO 72679 --- [deployment-5] org.craftercms.deployer.impl.TargetImpl  : ============================================================
    2025-05-07 10:48:02.777  INFO 72679 --- [deployment-5] org.craftercms.deployer.impl.TargetImpl  : Deployment for ed-preview finished in 0.003 secs
    2025-05-07 10:48:02.777  INFO 72679 --- [deployment-5] org.craftercms.deployer.impl.TargetImpl  : ============================================================
    2025-05-07 10:48:03.308  INFO 72679 --- [deployment-3] deployer.impl.processors.ScriptProcessor : Invoking example deployer processor
    2025-05-07 10:48:03.311  INFO 72679 --- [deployment-3] deployer.impl.processors.ScriptProcessor : Target value: null
    2025-05-07 10:48:03.389  INFO 72679 --- [deployment-3] deployer.impl.processors.ScriptProcessor : CREATE w bytes: /static-assets/images/15905722779_2901cdeefd_o.jpg 2160375
    2025-05-07 10:48:03.394  INFO 72679 --- [deployment-3] deployer.impl.processors.ScriptProcessor : Invoking example deployer processor complete
    2025-05-07 10:48:03.394  INFO 72679 --- [deployment-3] deployer.impl.processors.ScriptProcessor : Completed execution of script /Users/home/crafter-authoring/bin/crafter-deployer/ContentAccessExample.groovy
    2025-05-07 10:48:03.394  INFO 72679 --- [deployment-3] l.processors.AbstractDeploymentProcessor : ----- </ scriptProcessor @ ed-authoring > -----
    2025-05-07 10:48:03.394  INFO 72679 --- [deployment-3] l.processors.AbstractDeploymentProcessor : ----- < authoringSearchIndexingProcessor @ ed-authoring > -----
    2025-05-07 10:48:03.395  INFO 72679 --- [deployment-3] ocessors.AbstractSearchIndexingProcessor : Performing search indexing...
    2025-05-07 10:48:03.395  INFO 72679 --- [deployment-3] ocessors.AbstractSearchIndexingProcessor : Ensuring that index ed-authoring exists
    ...

|

.. _custom-configuration-parameters:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Custom Configuration Parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Crafter Deployer supports adding custom configuration parameters in deployment target configuration files.
This is useful when you want to get target level configuration within a deployment processor.

To create a custom configuration parameter for use in your deployment processors, open the YAML configuration file of the
deployment target you want to add the custom parameter using your favorite editor. Add your custom parameter under
``target`` as shown below:

.. code-block:: yaml
    :caption: *Defining a custom configuration parameter in a deployment target configuration file editorial-preview.yaml*
    :emphasize-lines: 5-6

    target:
      env: preview
      siteName: editorial

      myCustomParams:
        myParam: "a value"

To get the custom configuration parameter values defined in a deployment target configuration file for use in a
deployment processor:

.. code-block:: yaml
    :caption: *Retrieving a custom value defined in a deployment target configuration file*

    def someTargetValue = applicationContext.getEnvironment().getProperty('target.myCustomParams.myParam')


|hr|

.. _crafter-deployer-search-configuration-guide:

--------------------
Search Configuration
--------------------
Crafter Deployer provides two ways to use search:

^^^^^^^^^^^^^^^^^^^^^
Single Search Cluster
^^^^^^^^^^^^^^^^^^^^^
This is the most common configuration used, all operations will be performed on a single search cluster:

.. code-block:: yaml
  :linenos:
  :caption: Target configuration for a single search cluster

    target:
      search:
        openSearch:
          # Single cluster
          urls:
            - ${env:SEARCH_URL}
          username: ${env:SEARCH_USERNAME}
          password: ${env:SEARCH_PASSWORD}
          timeout:
            # The connection timeout in milliseconds, if set to -1 the default will be used
            connect: -1
            # The socket timeout in milliseconds, if set to -1 the default will be used
            socket: -1
          # The number of threads to use, if set to -1 the default will be used
          threads: -1
          # Indicates if keep alive should be enabled for sockets used by the search client, defaults to false
          keepAlive: false

^^^^^^^^^^^^^^^^^^^^^^^^
Multiple Search Clusters
^^^^^^^^^^^^^^^^^^^^^^^^
Using this configuration all read operations will be performed on one search cluster but write operations will
be performed on multiple search clusters:

.. code-block:: yaml
  :linenos:
  :caption: Target configuration for multiple search clusters

    target:
      search:
        openSearch:
          # Global auth, used for all clusters
          username: search
          password: passw0rd
          # Cluster for read operations
          readCluster:
            urls:
              - 'http://read-cluster-node-1:9200'
              - 'http://read-cluster-node-2:9200'
              # This cluster will use the global auth
          # Clusters for write operations
          writeClusters:
            - urls:
              - 'http://write-cluster-1-node-1:9200'
              - 'http://write-cluster-1-node-2:9200'
              # This cluster will use the global auth
            - urls:
              - 'http://write-cluster-2-node-1:9200'
              - 'http://write-cluster-2-node-2:9200'
              # Override the global auth for this cluster
              username: search2
              password: passw0rd2

^^^^^^^^^^^^^^^^^^^
Configuration Files
^^^^^^^^^^^^^^^^^^^
The search configuration can be changed in two places:

#. Global configuration file ``$CRAFTER_HOME/bin/crafter-deployer/config/base-target.yaml``, this will be applied to
   all targets loaded.

#. Individual target configuration file ``$CRAFTER_HOME/data/deployer/targets/{siteName}-{environment}.yaml``

|hr|

.. _crafter-deployer-api:

--------
REST API
--------
To view the Crafter Deployer REST APIs:

.. open_iframe_modal_button::
   :label: Open here
   :url: ../../_static/api/deployer.html
   :title: Deployer API

.. raw:: html

    or <a href="../../_static/api/deployer.html" target="_blank">in a new tab</a>

|

|hr|

-----------
Source Code
-----------
Crafter Deployer's source code is managed in GitHub: https://github.com/craftersoftware/craftercms/tree/support/4.x/deployer
