:is-up-to-date: True
:last-updated: 4.0.3

.. meta::
   :description: CrafterCMS Video form control reference — configure video selectors from desktop upload, repository, WebDAV, or S3 data sources in content types.
   :keywords: video control, form control, CrafterCMS, content type, video upload, S3, WebDAV, video repository, content modeling

.. _form-video:

=============
Video Control
=============
Video selector from a Data Source.

-------
Example
-------
.. figure:: /_static/images/form-controls/form-control-video-example.webp
    :width: 60%
    :alt: Form Control Video Example

-------------
Configuration
-------------
.. image:: /_static/images/form-controls/form-control-video.webp
    :width: 30%
    :alt: Form Control Video
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
   * - Data Source
     - Source that will populate the video picker.
   * - Read Only
     - Make field read-only (can't be changed by the author).
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out.
   * - **Related Data Sources**
     - * |vidUploadFromDesktop|
       * |vidFromRepo|
       * |vidFromWebDAV|
       * |vidUploadToWebDAV|
       * |vidFromS3|
       * |vidUploadToS3|

.. |vidUploadFromDesktop| replace:: :ref:`Video Uploaded From Desktop <form-source-video-desktop>`
.. |vidFromRepo| replace:: :ref:`Video from Repository <form-source-video-repo>`
.. |vidFromWebDAV| replace:: :ref:`Video from WebDAV Repository <form-source-webdav-video-repo>`
.. |vidUploadToWebDAV| replace:: :ref:`Video Uploaded to WebDAV Repository <form-source-webdav-video-upload>`
.. |vidFromS3| replace:: :ref:`Video From S3 Repository <form-source-s3-video-repo>`
.. |vidUploadToS3| replace:: :ref:`Video Uploaded to S3 Repository <form-source-s3-video-upload>`
