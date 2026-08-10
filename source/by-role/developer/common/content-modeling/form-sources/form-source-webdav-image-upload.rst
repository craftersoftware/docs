:is-up-to-date: True
:last-updated: 4.0.0

.. meta::
   :description: CrafterCMS WebDAV Image Upload data source reference — upload images to a WebDAV server using a configured WebDAV profile in content type forms.
   :keywords: WebDAV image upload, data source, CrafterCMS, WebDAV, image upload, profile ID, form sources, content modeling

.. _form-source-webdav-image-upload:

===============================
WebDAV Image Upload Data Source
===============================
Data source to upload images to WebDav repository.

.. figure:: /_static/images/form-sources/form-source-webdav-image-upload-repo.webp
    :width: 25%
    :alt: Source Control File Upload WebDAV

|

-------------
Configuration
-------------

.. image:: /_static/images/form-sources/form-source-basics-conf.webp
    :align: center
    :width: 40%
    :alt: Source Control File Upload WebDAV

|

.. list-table::
    :align: left
    :header-rows: 1

    * - Name
      - Description
    * - **Data Source Basics**
      -
    * - Title
      - Data source title to show on the form
    * - Name
      - Name of variable to store the final result in
    * - **Properties**
      -
    * - Repository Path
      - WebDAV repository path where to store the new uploaded image
    * - Profile Id
      - WebDAV profile id configured in webdav.xml
