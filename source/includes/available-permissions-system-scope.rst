The system level (global) permissions are defined in the file ``global-permission-mappings-config.xml``.

.. list-table::
    :header-rows: 1
    :widths: 25 75

    * - Permission
      - Description
    * - configure_log_levels
      - User is permitted to configure log levels from the :ref:`UI <override-logging-levels>`
        or via the API :base_url:`Loggers update level <_static/api/studio.html#tag/loggers/operation/loggersUpdateLevel>`
    * - view_logs
      - User is permitted to view logs from the :ref:`project tools UI <studio-log-console>` or
        :ref:`Main Menu UI <main-menu-tool-log-console>` depending on where the permission is configured (project or global/system)
    * - view_log_levels
      - User is permitted to view log levels from the :ref:`Main Menu UI <override-logging-levels>`
        or via the API :base_url:`Loggers get all <_static/api/studio.html#tag/loggers/operation/loggersGetAll>`
    * - audit_log
      - User is permitted to access the audit logs in the UI from the :ref:`main menu <nav-menu-audit>` or the
        :ref:`project tools <studio-logging>` via the API :base_url:`Get audit log <_static/api/studio.html#tag/audit/operation/getAuditLog>`
    * - duplicate_site
      - User is permitted to :ref:`duplicate a project <duplicate-project>`
    * - create_site
      - User is permitted to :ref:`create projects <your-first-editorial-project>`
    * - delete_site
      - User is permitted to delete projects |br|
        This is required when using the API to :base_url:`Delete a project <_static/api/studio.html#tag/sites/operation/deleteSite>`
    * - edit_site
      - User is permitted to edit sites |br|
        Required when using the following APIs:

        - :base_url:`Update the name and description for a given site <_static/api/studio.html#tag/sites/operation/updateSite>`
        - :base_url:`Unlock a site locked with state LOCKED <_static/api/studio.html#tag/sites/operation/unlockSite>`
    * - create_groups
      - User is permitted to :ref:`create new groups <create-a-new-group>` |br|
        Required when using the API to :base_url:`Create group <_static/api/studio.html#tag/groups/operation/createGroup>`
    * - read_groups
      - User is permitted to read groups |br|
        Used for :ref:`administering groups <groups-management>`
        Required when using the following APIs:

        - :base_url:`Get all groups <_static/api/studio.html#tag/groups/operation/getGroups>`
        - :base_url:`Get group by ID <_static/api/studio.html#tag/groups/operation/getGroup>`
        - :base_url:`Get all the members of the specified group <_static/api/studio.html#tag/groups/operation/getGroupMembers>`
    * - update_groups
      - User is permitted to update groups |br|
        Used for :ref:`administering groups <groups-management>`
        Required when using the following APIs:

        - :base_url:`Update group <_static/api/studio.html#tag/groups/operation/updateGroups>`
        - :base_url:`Add member(s) to the specified group <_static/api/studio.html#tag/groups/operation/addGroupMembers>`
    * - delete_groups
      - User is permitted to :ref:`delete groups <deleting-a-group>` |br|
        Required when using the following APIs:

        - :base_url:`Delete group <_static/api/studio.html#tag/groups/operation/deleteGroup>`
        - :base_url:`Remove member(s) from the specified group <_static/api/studio.html#tag/groups/operation/removeGroupMembers>`
    * - create_users
      - User is permitted to :ref:`create new users <creating-a-user>` |br|
        Required when using the API to :base_url:`Create user <_static/api/studio.html#tag/users/operation/createUser>`
    * - delete_users
      - User is permitted to :ref:`delete users <deleting-a-user>` |br|
        Required when using the API to :base_url:`Create user <_static/api/studio.html#tag/users/operation/deleteUser>`
    * - encryption_tool
      - User is permitted to access the :ref:`encryption tool <studio-encryption-tool>` |br|
        Required when using the API to :base_url:`Encrypt a text value <_static/api/studio.html#tag/security/operation/encrypt>`
    * - install_plugins
      - User is permitted to :ref:`install plugins <plugin-management>` |br|
        Required when using the following APIs:

        - :base_url:`Install plugin <_static/api/studio.html#tag/marketplace/operation/installPlugin>`
        - :base_url:`Copy local plugin <_static/api/studio.html#tag/marketplace/operation/copyPlugin>`
    * - list_plugins
      - User is permitted to list installed plugins |br|
        Required when using the API to :base_url:`get the list of marketplace plugins installed in the given site <_static/api/studio.html#tag/marketplace/operation/getInstalledPlugins>`
    * - manage_access_token
      - User is permitted to :ref:`manage the access tokens <nav-menu-token-management>` |br|
        Required when using the following APIs:

        - :base_url:`Get access tokens <_static/api/studio.html#tag/security/operation/getAccessTokens>`
        - :base_url:`Create access token <_static/api/studio.html#tag/security/operation/createAccessToken>`
        - :base_url:`Update access token <_static/api/studio.html#tag/security/operation/updateAccessToken>`
        - :base_url:`Delete access token <_static/api/studio.html#tag/security/operation/deleteAccessToken>`
    * - read_users
      - User is permitted to read users. Used in the :ref:`Users Management Console <users-management>` |br|
        Required when using the following APIs:

        - :base_url:`Get users <_static/api/studio.html#tag/users/operation/getUsers>`
        - :base_url:`Get user <_static/api/studio.html#tag/users/operation/getUser>`
        - :base_url:`Get user sites <_static/api/studio.html#tag/users/operation/getUserSites>`
        - :base_url:`Get user site roles <_static/api/studio.html#tag/users/operation/getUserSiteRoles>`
    * - update_users
      - User is permitted to update users. Used in the :ref:`Users Management Console <users-management>` |br|
        Required when using the following APIs:

        - :base_url:`Update user <_static/api/studio.html#tag/users/operation/updateUser>`
        - :base_url:`Enable user <_static/api/studio.html#tag/users/operation/enableUser>`
        - :base_url:`Disable user <_static/api/studio.html#tag/users/operation/disableUser>`
        - :base_url:`Reset user password <_static/api/studio.html#tag/users/operation/resetUserPassword>`
    * - search_plugins
      - User is permitted to :ref:`search for plugins <plugin-management>`
    * - read_cluster |enterpriseOnly|
      - User is allowed to list the cluster members and the cluster mode (PRIMARY vs REPLICA) |br|
        Required when using the following APIs:

        - :base_url:`Get the local Studio instance cluster mode <_static/api/studio.html#tag/cluster/operation/getClusterMode>`
        - :base_url:`Get all the members of the cluster <_static/api/studio.html#tag/cluster/operation/getClusterMembers>`
    * - write_global_configuration
      - User is permitted to write global configuration content for Studio
    * - system_properties_manage
      - User is allowed to update, write or delete system properties. |br|
        Required when using the following APIs:

        - :base_url:`Update system properties <_static/api/studio.html#tag/system/operation/updateSystemProperties>`
        - :base_url:`Set system maintenance mode status <_static/api/studio.html#tag/system/operation/setMaintenanceMode>` |enterpriseOnly|

    * - system_properties_read
      - User is allowed to read system properties. |br|
        Required when using the following API:

        - :base_url:`Read system properties <_static/api/studio.html#tag/system/operation/getSystemProperties>`

    * - allowed_in_maintenance |enterpriseOnly|
      - User is allowed to access the system APIs during maintenance mode |br|
