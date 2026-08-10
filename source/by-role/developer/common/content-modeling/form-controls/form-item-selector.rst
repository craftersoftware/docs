:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Item Selector form control reference — configure node-selector for cross-referencing content with shared, embedded, file, S3, or WebDAV data sources.
   :keywords: item selector, node-selector, form control, CrafterCMS, content type, shared content, embedded content, S3, WebDAV

.. _form-item-selector:

=====================
Item Selector Control
=====================
Item Selector control.

The field type for an item selector control in a content model is ``node-selector``.
Use a ``node-selector`` in your content model to allow cross-referencing and linking between items.

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-item-selector-example.webp
    :width: 50%
    :alt: Form Control Item Selector Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-item-selector.webp
    :width: 30%
    :alt: Form Control Item Selector Properties
    :align: center

|

.. list-table::
   :align: left
   :header-rows: 1

   * - Name
     - Description
   * - **Field Basics**
     -
   * - Title
     - Control title to show the author on the input form
   * - Name / Variable Name
     - Name of variable to store the final result in. This is used by the View layer or the Controllers to gain access to the data during runtime
   * - ICE Group
     - In-Context Edit Group: During ICE editing in Preview mode, this control will be part of this group for editing which means when the author click on the Pencil Tool, they will get all the controls in the same group associated with the specific Pencil Tool
   * - Description
     - Form Control description
   * - Help
     - Help to show authors on the input form associated with this control
   * - **Properties**
     -
   * - Min Size
     - Minimum amount of items selected.
   * - Max Size
     - Maximum amount of items selected.
   * - Item Manager
     - Source where the items will be selected/created.
   * - Read Only
     - Make field read-only (can't be changed by the author).
   * - Disable Flattening for Search
     -
   * - Use single value filename
     -
   * - Use _mvs postfix
     -
   * - **Constraints**
     -
   * - Allow Duplicate
     - Allows adding the same item more than once.
   * - **Related Data Sources**
     - * |sharedContent|
       * |embeddedContent|
       * |fileUploadFromDesktop|
       * |fileFromRepo|
       * |fileFromWebDAV|
       * |fileUploadToWebDAV|
       * |fileFromS3|
       * |fileUploadToS3|

.. |fileUploadFromDesktop| replace:: :ref:`File Uploaded From Desktop <form-source-file-desktop>`
.. |fileFromRepo| replace:: :ref:`File from Repository <form-source-file-browse>`
.. |fileFromWebDAV| replace:: :ref:`File from WebDAV Repository <form-source-webdav-file-repo>`
.. |fileUploadToWebDAV| replace:: :ref:`File Uploaded to WebDAV Repository <form-source-webdav-file-upload>`
.. |fileFromS3| replace:: :ref:`File From S3 Repository <form-source-s3-file-repo>`
.. |fileUploadToS3| replace:: :ref:`File Uploaded to S3 Repository <form-source-s3-file-upload>`
.. |sharedContent| replace:: :ref:`Shared Content <form-source-shared-content>`
.. |embeddedContent| replace:: :ref:`Embedded Content <form-source-embedded-content>`
