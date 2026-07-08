:is-up-to-date: True
:last-updated: 5.0.0
:orphan:

.. _breaking-changes-5-x:

================================
Breaking Changes in CrafterCMS 5
================================
This section covers changes that might affect your CrafterCMS projects, as well as other considerations
before upgrading. Please review the following and apply changes as required:

|hr|

.. _breaking-changes-in-craftercms-5-0-0:

------------------------------------
Breaking Changes in CrafterCMS 5.0.0
------------------------------------
- The Spring profile ``crafter_studio_externalDb`` has been removed in CrafterCMS version 5.0.0.

- The following APIs are removed from CrafterCMS 5.0.0:

  - /api/1/services/api/1/site/create.json
  - /api/1/services/api/1/site/delete-site.json
  - /api/1/services/api/1/site/get-canned-message.json
  - /api/1/services/api/1/content/change-content-type.json
  - /api/1/services/api/1/content/get-content-type.json : use /api/2/configuration/content_types/{siteId} (GET, with contentTypeId query param) instead
  - /api/1/services/api/1/content/get-content-types.json : use /api/2/configuration/content_types/{siteId} (GET, for all types)
  - /api/2/configuration/content_types/{siteId}/allowed_types (GET the ids of content types allowed for a path) instead
  - /api/2/configuration/content-type/usage : use /api/2/configuration/content_types/{siteId}/usage instead
  - /api/2/configuration/content-type/preview_image : use /api/2/configuration/content_types/{siteId}/preview_image instead
  - /api/2/configuration/content-type/form_controller : use /api/2/configuration/content_types/{siteId}/form_controller instead
  - /api/2/configuration/content-type/delete : use /api/2/configuration/content_types/{siteId} (DELETE) instead
  - /api/1/services/api/1/content/get-item-versions.json
  - /api/1/services/api/1/content/write-content.json
  - /api/1/services/api/1/publish/commits.json
  - /api/1/services/api/1/publish/start.json
  - /api/1/services/api/1/publish/stop.json
  - /api/1/services/api/1/publish/publish-items.json
  - /api/1/services/api/1/server/get-available-languages.json
  - /api/2/content/children_by_path
  - /api/2/publish/all
  - /api/2/publish/history
  - /api/2/publish/packages
  - /api/2/publish/package
  - /api/2/publish/cancel
  - /api/2/publish/status
  - /api/2/dashboard/content/pending_approval
  - /api/2/dashboard/content/pending_approval/{id}
  - /api/2/dashboard/publishing/scheduled
  - /api/2/dashboard/publishing/scheduled/{id}
  - /api/2/dashboard/publishing/history
  - /api/2/dashboard/publishing/history/{id}
  - /api/2/workflow/affected_paths
  - /api/2/workflow/request_publish
  - /api/2/workflow/publish
  - /api/2/workflow/approve
  - /api/2/workflow/reject
  - /api/2/workflow/packages
  - /api/2/workflow/packages/{id}
  - /api/2/workflow/packages/approve
  - /api/2/workflow/packages/reject
  - /api/1/services/api/1/deployment/bulk-golive.json
  - /api/1/services/api/1/preview/sync-site.json
  - /api/2/dependency/dependent_items

- Publishing blacklist configuration has been changed. |br|
  From a regex:

  .. code-block:: yaml
      :caption: *Old property for the publishing blacklist*

      studio.configuration.publishing.blacklist.regex: >-
          .*/\.keep

  To a git `pathspec <https://git-scm.com/docs/gitglossary#Documentation/gitglossary.txt-aiddefpathspecapathspec>`__
  that is empty by default because ``.keep`` files are always excluded:

  .. code-block:: yaml
      :caption: *New property for the publishing blacklist*
      :emphasize-lines: 1

      studio.configuration.publishing.blacklist.pathspecs: ""

  See :ref:`publishing-blacklist` for more information.

- Content type controllers now use the new content lifecycle API |br|
  When upgrading to CrafterCMS version 5.0.0, the Upgrade Manager will compare ``controller.groovy`` to the
  default script from built-in blueprints. If they match, the script will be deleted.

  If they don't match, the original file contents will be commented out and a ``logger.error`` statement will be added so admins
  can manually check and fix as required. Below is an example of a commented out controller script after upgrading to
  version 5.0. Notice the ``logger.error`` statement added:

  .. code-block:: groovy
      :caption: *Example controller script commented out when upgrading*
      :emphasize-lines: 37

      /*
        WARNING: This controller script was not updated automatically.
        Please review and update it manually to ensure compatibility with the new version of Crafter Studio.
        Original content commented out below
      */
      ///*
      // * Copyright (C) 2007-2022 Crafter Software Corporation. All Rights Reserved.
      // *
      // * This program is free software: you can redistribute it and/or modify
      // * it under the terms of the GNU General Public License version 3 as published by
      // * the Free Software Foundation.
      // *
      // * This program is distributed in the hope that it will be useful,
      // * but WITHOUT ANY WARRANTY; without even the implied warranty of
      // * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
      // * GNU General Public License for more details.
      // *
      // * You should have received a copy of the GNU General Public License
      // * along with this program.  If not, see <http://www.gnu.org/licenses/>.
      // */
      //
      //import scripts.libs.CommonLifecycleApi
      //
      //def contentLifecycleParams =[:]
      //contentLifecycleParams.site = site
      //contentLifecycleParams.path = path
      //contentLifecycleParams.user = user
      //contentLifecycleParams.contentType = contentType
      //contentLifecycleParams.contentLifecycleOperation = contentLifecycleOperation
      //contentLifecycleParams.contentLoader = contentLoader
      //contentLifecycleParams.applicationContext = applicationContext
      //
      //def controller = new CommonLifecycleApi(contentLifecycleParams)
      //controller.execute()
      //
      //System.out.println("Server side content lifecycle event : " + contentLifecycleOperation)
      logger.error('This controller is disabled. Please review and upgrade to use the 5.0 content lifecycle API')

  |

  Here's the output of the controller script above after the upgrade:

  .. code-block:: text
      :caption: *Controller script logger output when running the commented out controller script after an upgrade*

      [ERROR] 2025-10-16T16:15:24,277 [http-nio-8080-exec-5] [Lifecycle-edd-/page/article] | This controller is disabled. Please review and upgrade to use the 5.0 content lifecycle API

  |

  Please see :ref:`server-side-form-controllers` and :ref:`content-processors-configuration` for more information on
  content type controller scripts and content processors configuration.

- The following permissions are removed from CrafterCMS 5.0.0:

  - **cancel_publish**: replaced by **publish_cancel** for name consistency
  - **get_publishing_queue**: replaced by **publish_get_queue** for name consistency
  - **publish**: use the new **publish_approve** and **publish_request**
  - **publish_by_commits**: use the new **publish_approve** and **publish_request**
  - **publish_clear_lock**: removed

  See :ref:`permission-mappings` for more information on permissions for version 5.0.0

- The deprecated Engine REST API Get Descriptor ``/api/1/site/content_store/descriptor`` code is now removed in version 5.0.0 |br|
  To get the content of an item, please use the :base_url:`Get item <_static/api/engine.html#tag/content/operation/getItem>` API.

  Here's an example of getting the content of the home page of a site created using the empty blueprint named ``hello``,
  where the ``url`` for the home page is ``/site/website/index.xml``:

  .. code-block:: text

      GET http://localhost:8080/api/1/site/content_store/item.json?url=/site/website/index.xml&crafterSite=hello

  |

  Here's the output with the content of the page highlighted:

  .. code-block:: json
      :caption: *Get Item API output*
      :emphasize-lines: 6-24

      {
        "name": "index.xml",
        "url": "/site/website/index.xml",
        "descriptorUrl": "/site/website/index.xml",
        "descriptorDom": {
          "page": {
            "content-type": "/page/entry",
            "display-template": "/templates/web/entry.ftl",
            "merge-strategy": "inherit-levels",
            "objectGroupId": "8d7f",
            "objectId": "8d7f21fa-5e09-00aa-8340-853b7db302da",
            "file-name": "index.xml",
            "folder-name": null,
            "createdDate": "2024-01-31T16:18:14.000Z",
            "createdDate_dt": "2024-01-31T16:18:14.000Z",
            "lastModifiedDate": "2025-10-17T12:20:30.440Z",
            "lastModifiedDate_dt": "2025-10-17T12:20:30.440Z",
            "placeInNav": "false",
            "internal-name": "Home",
            "orderDefault_f": "-1",
            "body_html": "\u003Ch1\u003EWelcome to Your CrafterCMS Project Yo.\u003C/h1\u003E\u003Cp\u003EEdit from API. This project blueprint is the equivalent of a Hello World. It's a blank slate on which you can build your own digital experience.\u003C/p\u003E\u003Cul\u003E\u003Cli\u003ETo create new content types use the Project Tools &gt; Content Types menu on the left Sidebar\u003C/li\u003E\u003Cli\u003ETo update markup, edit this template by clicking on the Options menu on the top toolbar and select \"Edit Template\"\u003C/li\u003E\u003Cli\u003ETo modify this text, hover/click on it or use the options menu and select \"Edit\" on the top toolbar\u003C/li\u003E\u003Cli\u003ECrafter documentation can be found \u003Ca href=\"https://craftercms.com/docs\" target=\"_blank\" rel=\"noopener\"\u003Ehere\u003C/a\u003E\u003C/li\u003E\u003Cli\u003ECrafterCMS authoring and developer training is available. Please contact \u003Ca href=\"mailto:info@craftercms.com\"\u003Einfo@craftercms.com\u003C/a\u003E\u003C/li\u003E\u003C/ul\u003E\u003Cdiv\u003E\u003Cp\u003EThis content is managed by the form. &nbsp;Click edit to change OR in the upper right hand corner, click the pencil to turn on edit mode.\u003C/p\u003E\u003Cp\u003EEdit from API.\u003C/p\u003E\u003C/div\u003E",
            "title_t": "Hello!",
            "disabled": "false"
          }
        },
        "properties": null,
        "folder": false
      }

|hr|
