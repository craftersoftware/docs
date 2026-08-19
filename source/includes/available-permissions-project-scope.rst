The project level permissions are defined in the file ``permission-mappings-config.xml`` within a project/site. These are defined under a role and use the match-all ``<rule regex=".*">``.

.. list-table::
    :header-rows: 1
    :widths: 25 75

    * - Permission
      - Description
    * - add_remote
      - User is permitted to add a remote repository to the project. |br|
        Required for using the :ref:`Git project tool <project-tools-git>` to add a repository in a project using the
        ``New Remote`` button which uses the :base_url:`Add remote repository <_static/api/studio.html#tag/repository/operation/addRemoteRepository>` API.
    * - cancel_failed_pull
      - User is permitted to cancel a failed pull from a repository. |br|
        Required for using the :ref:`Git project tool <project-tools-git>` to cancel a failed pull in the UI via
        the API :base_url:`Cancel failed pull <_static/api/studio.html#tag/repository/operation/cancelFailedPull>`
    * - commit_resolution
      - User is permitted to commit resolution |br|
        Required for using the :ref:`Git project tool <project-tools-git>` to commit resolution in the UI via
        the API :base_url:`Commit resolution <_static/api/studio.html#tag/repository/operation/commitResolution>`
    * - list_remotes
      - User is permitted to list remote repositories for a project |br|
        Required for using the :ref:`Git project tool <project-tools-git>` to list remote repositories for a project in
        the UI via the API :base_url:`List remote repositories <_static/api/studio.html#tag/repository/operation/listRemoteRepositories>`
    * - pull_from_remote
      - User is permitted to pull content from remote repository to project content repository |br|
        Required for using the :ref:`Git project tool <project-tools-git>` to pull content from remote repository in the UI via
        the API :base_url:`Pull from remote repository <_static/api/studio.html#tag/repository/operation/pullFromRemoteRepository>`
    * - push_to_remote
      - User is permitted to push content to remote repository from project content repository |br|
        Required for using the :ref:`Git project tool <project-tools-git>` to push content to remote repository in the UI via
        the API :base_url:`Push to remote repository <_static/api/studio.html#tag/repository/operation/pushToRemoteRepository>`
    * - remove_remote
      - User is permitted to remove remote repository from project content repository |br|
        Required for using the :ref:`Git project tool <project-tools-git>` to remove remote repository from project in the UI via
        the API :base_url:`Remove remote repository <_static/api/studio.html#tag/repository/operation/removeRemoteRepository>`
    * - repair_repository
      - User is permitted to repair the repository
    * - cancel_publish
      - User is permitted to cancel a publish request from the :ref:`UI <publishing-and-status>`
        or via the API :base_url:`Cancel publishing packages <_static/api/studio.html#tag/publishing/operation/cancelPublishingPackages>`
    * - get_publishing_queue
      - User is permitted to get the list of packages in the publishing queue from the :ref:`UI <publishing-and-status>`
        or via the API :base_url:`Publishing packages <_static/api/studio.html#tag/publishing/operation/PublishingPackages>` |br|
        Also required by the API :base_url:`Get package details <_static/api/studio.html#tag/publishing/operation/getPublishingPackageDetails>`
    * - publish_status
      - User is permitted to get the publishing status from the :ref:`UI <publishing-and-status>`
        or via the API :base_url:`Get publishing status <_static/api/studio.html#tag/publishing/operation/getPublishingStatus>`
    * - publish_by_commits
      - User is permitted to publish commits from sandbox repository by commit id from the :ref:`UI <publishing-and-status>`
        or via the API :base_url:`Publish commits <_static/api/studio.html#tag/publishing/operation/publishCommits>`
    * - start_stop_publisher
      - User is permitted to start/stop the publisher from the :ref:`UI <publishing-and-status>`
    * - view_logs
      - User is permitted to view logs from the :ref:`project tools UI <studio-log-console>` or
        :ref:`Main Menu UI <main-menu-tool-log-console>` depending on where the permission is configured (project or global/system)
    * - audit_log
      - User is permitted to access the audit logs in the UI from the :ref:`main menu <nav-menu-audit>` or the
        :ref:`project tools <studio-logging>` via the API :base_url:`Get audit log <_static/api/studio.html#tag/audit/operation/getAuditLog>`
    * - content_search
      - User is permitted to search for content from the UI
    * - s3_read
      - User is permitted to get a list of items from an S3 bucket defined in the :ref:`AWS S3 profile <aws-profile-configuration>`
        configured.|br| This is required when using the API to :base_url:`Get a list of items from an S3 bucket <_static/api/studio.html#tag/aws/operation/listItems>`
    * - s3_write
      - User is permitted to upload a file to an S3 bucket defined in the :ref:`AWS S3 profile <aws-profile-configuration>`
        configured.|br| This is required when using the API to :base_url:`Upload a file to an S3 bucket <_static/api/studio.html#tag/aws/operation/uploadItems>`, etc.
    * - webdav_read
      - User is permitted to get a list of items from a WebDAV server configured in the :ref:`WebDAV profiles <webdav-profiles-configuration>`
        configuration file. |br| This is required when using the API to :base_url:`Get a list of items from a WebDAV server <_static/api/studio.html#tag/webdav/operation/listItemsWebdav>`
    * - webdav_write
      - User is permitted to upload a file to a WebDAV server configured in the :ref:`WebDAV profiles <webdav-profiles-configuration>`
        configuration file. |br| This is required when using the API to :base_url:`Upload a file to a WebDAV server <_static/api/studio.html#tag/webdav/operation/uploadItemsWebdav>`
    * - encryption_tool
      - User is permitted to access the :ref:`encryption tool <studio-encryption-tool>` |br|
        Required when using the API to :base_url:`Encrypt a text value <_static/api/studio.html#tag/security/operation/encrypt>`
    * - get_children
      - User is permitted to call getChildren* APIs for browsing project content |br|
        Required when using the following APIs:

        - :base_url:`Get list of children for given item paths <_static/api/studio.html#tag/content/operation/getChildrenByPaths>`
        - :base_url:`Get item details for given an item path <_static/api/studio.html#tag/content/operation/getDetailedItemByPath>`
        - :base_url:`Get list of items for given item paths <_static/api/studio.html#tag/content/operation/getSandboxItemsByPath>`
    * - install_plugins
      - User is permitted to :ref:`install plugins <plugin-management>` |br|
        Required when using the following APIs:

        - :base_url:`Install plugin <_static/api/studio.html#tag/marketplace/operation/installPlugin>`
        - :base_url:`Copy local plugin <_static/api/studio.html#tag/marketplace/operation/copyPlugin>`
    * - list_plugins
      - User is permitted to list installed plugins |br|
        Required when using the API to :base_url:`get the list of marketplace plugins installed in the given site <_static/api/studio.html#tag/marketplace/operation/getInstalledPlugins>`
    * - remove_plugins
      - User is permitted to :ref:`remove installed plugins <plugin-management>` |br|
        Required when using the following APIs:

        - :base_url:`Remove plugin <_static/api/studio.html#tag/marketplace/operation/removePlugin>`
        - :base_url:`Plugin usage <_static/api/studio.html#tag/marketplace/operation/pluginUsage>`
    * - set_item_states
      - User is permitted to set item states |br|
        Required when using the following APIs:

        - :base_url:`Set item states <_static/api/studio.html#tag/workflow/operation/setItemStates>`
        - :base_url:`Update item states <_static/api/studio.html#tag/workflow/operation/updateItemStates>`
    * - site_status
      - User is permitted to :ref:`get status of repository for a project <project-tools-git>` |br|
        Required when using the API :base_url:`Repository status <_static/api/studio.html#tag/repository/operation/repositoryStatus>`
    * - unlock_repository
      - User is permitted to unlock the repository
    * - read_configuration
      - User is permitted to read configuration content for project |br|
        Required when using the following APIs:

        - :base_url:`Get all model definitions for site <_static/api/studio.html#tag/model/operation/getAllModelDefinitions>`
        - :base_url:`Get configuration content for site and configuration location <_static/api/studio.html#tag/configuration/operation/getConfiguration>`
        - :base_url:`Get configuration history for site and configuration location <_static/api/studio.html#tag/configuration/operation/getConfigurationHistory>`
    * - write_configuration
      - User is permitted to write configuration content for project |br|
        Required when using the following APIs:

        - :base_url:`Write configuration content for site <_static/api/studio.html#tag/configuration/operation/writeConfiguration>`
        - :base_url:`Write configuration content a given site and plugin <_static/api/studio.html#tag/configuration/operation/writePluginConfiguration>`
        - :base_url:`Clear all configuration cache for a given site <_static/api/studio.html#tag/configuration/operation/clearConfigurationCache>`
        - :base_url:`Delete files related to a given content-type <_static/api/studio.html#tag/configuration/operation/deleteContentType>`
