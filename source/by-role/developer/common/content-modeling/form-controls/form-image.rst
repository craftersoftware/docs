:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Image form control reference — configure image selectors with dimension constraints, crop dialog, and S3, WebDAV, or repository data sources.
   :keywords: image control, form control, CrafterCMS, content type, image upload, S3, WebDAV, crop image, content modeling

.. _form-image:

=============
Image Control
=============
Image selector from a Data Source.

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-image-example.webp
    :width: 40%
    :alt: Form Control Image Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-image.webp
    :width: 30%
    :alt: Form Control Image
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
   * - Width
     - Min/max width of image.
   * - Height
     - Min/max height of image.
   * - Thumbnail Width
     - Width of thumbnail image.
   * - Thumbnail Height
     - Height of thumbnail image.
   * - Data Source
     - Source where the image will be loaded.
   * - Read Only
     - Make field read-only (can't be changed by the author).
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out.
   * - **Related Data Sources**
     - * |imgUploadFromDesktop|
       * |imgFromRepo|
       * |imgFromWebDAV|
       * |imgUploadToWebDAV|
       * |imgFromS3|
       * |imgUploadToS3|

.. |imgUploadFromDesktop| replace:: :ref:`Image Uploaded from Desktop <form-source-image-desktop>`
.. |imgFromRepo| replace:: :ref:`Image from Repository <form-source-image-repo>`
.. |imgFromWebDAV| replace:: :ref:`Image from WebDAV Repository <form-source-webdav-image-repo>`
.. |imgUploadToWebDAV| replace:: :ref:`Image Uploaded to WebDAV Repository <form-source-webdav-image-upload>`
.. |imgFromS3| replace:: :ref:`Image From S3 Repository <form-source-s3-image-repo>`
.. |imgUploadToS3| replace:: :ref:`Image Uploaded to S3 Repository <form-source-s3-image-upload>`

Setting the width and/or the height properties of this control will automatically open the
image cropper dialog ``Crop Image`` when the image being uploaded does not meet the requirements set.

.. image:: /_static/images/page/crop-image-dialog.webp
     :width: 50 %
     :align: center
     :alt: Crop Image Dialog
