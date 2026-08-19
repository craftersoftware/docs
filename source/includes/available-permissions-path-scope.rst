The path level permissions are defined in the file ``permission-mappings-config.xml`` within a project/site. These are defined under a role and use a regex rule to match a specific path pattern.

.. list-table::
    :header-rows: 1
    :widths: 25 75

    * - Permission
      - Description

    * - resolve_conflict
      - User is permitted to resolve a conflict for a file by accepting ours or theirs |br|
        Required for using the :ref:`Git project tool <project-tools-git>` to resolve a conflict for a file in the UI via
        the API :base_url:`Resolve conflict <_static/api/studio.html#tag/repository/operation/resolveConflict>`

    * - site_diff_conflicted_file
      - User is permitted to get the difference between ``ours`` and ``theirs`` for a conflicted file for a project |br|
        Required for using the :ref:`Git project tool <project-tools-git>` to get the difference between ``ours`` and
        ``theirs`` for a conflicted file in the UI or via the API :base_url:`Diff conflicted file <_static/api/studio.html#tag/repository/operation/diffConflictedFile>`

    * - publish_approve
      - User is permitted to approve submitted content for publishing from the :ref:`UI <publishing-and-status>` or via
        the API :base_url:`Approve request publish submission <_static/api/studio.html#tag/workflow/operation/workflowApprove>` |br|
        May also be required when using the API that :base_url:`Publishes the given content to the target (staging or live) <_static/api/studio.html#tag/publishing/operation/publish>`.

    * - publish_request
      - User is permitted to publish content to the target (staging or live) from the the :ref:`UI <publishing-and-status>` or via
        the API that :base_url:`Publishes the given content to the target (staging or live) <_static/api/studio.html#tag/publishing/operation/publish>` |br|
        Also required by the API :base_url:`Get available publishing targets for site <_static/api/studio.html#tag/publishing/operation/getAvailablePublishingTargets>`
        Users need both `publish_approve` and `publish_request` to be able to publish immediately without approval.

    * - publish_reject
      - User is permitted to reject a publish package waiting for approval from the :ref:`UI <publishing-and-status>` or via
        the API :base_url:`Approve request publish submission <_static/api/studio.html#tag/workflow/operation/workflowReject>`

    * - content_copy
      - User is permitted to copy content from the UI :ref:`Sidebar <sidebar>` or the :ref:`Toolbar <toolbar>` options |br|
        Note that this permission enables the ``copy`` action in the UI but it does not enable the user to paste the items.
        Paste requires ``content_write`` permission

    * - content_create
      - User is permitted to create new content from the UI :ref:`Sidebar <sidebar>` or the :ref:`Toolbar <toolbar>` options |br|
        Note that this permission enables the action in the UI but it does not enable the user to create the content.
        Actual content creation requires ``content_write`` permission

    * - content_delete
      - User is permitted to delete content from the UI :ref:`Sidebar <sidebar>` or the :ref:`Toolbar <toolbar>` options
        or via the API :base_url:`Delete content <_static/api/studio.html#tag/content/operation/getDeletePackage>`

    * - content_read
      - User is permitted to read content from the UI or use various APIs e.g. getting the history for a content item
        via the API :base_url:`Get item history <_static/api/studio.html#tag/content/operation/getItemHistory>`,
        getting the preview image of a given content type via the API
        :base_url:`Get content type preview image <_static/api/studio.html#tag/content/operation/getContentTypePreviewImage>`, etc.

    * - content_write
      - User is permitted to edit content in the UI or use various APIs e.g. renaming content
        via the API :base_url:`Rename content <_static/api/studio.html#tag/content/operation/contentRename>`, etc.

    * - folder_create
      - User is permitted to create new folder

    * - item_unlock
      - User is permitted to unlock items |br|
        Required when using the API to :base_url:`unlock item by path <_static/api/studio.html#tag/content/operation/itemUnlockByPath>`
