:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Rich Text Editor (RTE) form control reference — configure WYSIWYG text areas with image, video, and file managers and custom RTE configuration.
   :keywords: rich text editor, RTE, form control, CrafterCMS, content type, WYSIWYG, TinyMCE, image manager, content modeling

.. _form-rte:

========================
Rich Text Editor Control
========================
Rich Text Area field.

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-rte-example.webp
    :width: 60%
    :alt: RTE Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-rte-properties.webp
    :width: 40%
    :alt: RTE
    :align: center

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
   * - Width
     - Width of the Rich Text Area.
   * - Height
     - Height of the Rich Text Area.
   * - Force Root Block p Tag
     -
   * - Force p tags New Lines
     - Adds a p tag on every new line.
   * - Force br New Lines
     - Adds br for each new line.
   * - Supported Channels
     -
   * - RTE Configuration
     - Configuration to be loaded on the rte.
   * - Image Manager
     -
   * - Video Manager
     -
   * - File Manager
     -
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out.
   * - **Related Data Sources**
     - Image Manager

       * |imgUploadFromDesktop|
       * |imgFromRepo|
       * |imgFromWebDAV|
       * |imgUploadToWebDAV|
       * |imgFromS3|
       * |imgUploadToS3|

       Video Manager

       * |vidUploadFromDesktop|
       * |vidFromRepo|
       * |vidFromWebDAV|
       * |vidUploadToWebDAV|
       * |vidFromS3|
       * |vidUploadToS3|

       File Manager

       * |fileUploadFromDesktop|
       * |fileFromRepo|
       * |fileFromWebDAV|
       * |fileUploadToWebDAV|
       * |fileFromS3|
       * |fileUploadToS3|
       * |sharedContent|
       * |embeddedContent|


.. |imgUploadFromDesktop| replace:: :ref:`Image Uploaded from Desktop <form-source-image-desktop>`
.. |imgFromRepo| replace:: :ref:`Image from Repository <form-source-image-repo>`
.. |imgFromWebDAV| replace:: :ref:`Image from WebDAV Repository <form-source-webdav-image-repo>`
.. |imgUploadToWebDAV| replace:: :ref:`Image Uploaded to WebDAV Repository <form-source-webdav-image-upload>`
.. |imgFromS3| replace:: :ref:`Image From S3 Repository <form-source-s3-image-repo>`
.. |imgUploadToS3| replace:: :ref:`Image Uploaded to S3 Repository <form-source-s3-image-upload>`

.. |vidUploadFromDesktop| replace:: :ref:`Video Uploaded From Desktop <form-source-video-desktop>`
.. |vidFromRepo| replace:: :ref:`Video from Repository <form-source-video-repo>`
.. |vidFromWebDAV| replace:: :ref:`Video from WebDAV Repository <form-source-webdav-video-repo>`
.. |vidUploadToWebDAV| replace:: :ref:`Video Uploaded to WebDAV Repository <form-source-webdav-video-upload>`
.. |vidFromS3| replace:: :ref:`Video From S3 Repository <form-source-s3-video-repo>`
.. |vidUploadToS3| replace:: :ref:`Video Uploaded to S3 Repository <form-source-s3-video-upload>`

.. |fileUploadFromDesktop| replace:: :ref:`File Uploaded From Desktop <form-source-file-desktop>`
.. |fileFromRepo| replace:: :ref:`File from Repository <form-source-file-browse>`
.. |fileFromWebDAV| replace:: :ref:`File from WebDAV Repository <form-source-webdav-file-repo>`
.. |fileUploadToWebDAV| replace:: :ref:`File Uploaded to WebDAV Repository <form-source-webdav-file-upload>`
.. |fileFromS3| replace:: :ref:`File From S3 Repository <form-source-s3-file-repo>`
.. |fileUploadToS3| replace:: :ref:`File Uploaded to S3 Repository <form-source-s3-file-upload>`
.. |sharedContent| replace:: :ref:`Shared Content <form-source-shared-content>`
.. |embeddedContent| replace:: :ref:`Embedded Content <form-source-embedded-content>`
