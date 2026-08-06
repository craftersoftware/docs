:is-up-to-date: True
:last-updated: 4.0.0

.. meta::
   :description: CrafterCMS Transcoded Video form control reference — select transcoded videos from AWS MediaConvert data source in content type forms.
   :keywords: transcoded video, form control, CrafterCMS, content type, MediaConvert, video transcoding, S3, content modeling

.. _form-transcoded-video:

========================
Transcoded Video Control
========================
Transcoded Video selector for Video Transcoding Data Source.

-------
Example
-------
.. figure:: /_static/images/form-controls/form-control-transcoded-video-example.webp
    :width: 60%
    :alt: Form Control Transcoded Video Example

-------------
Configuration
-------------
.. image:: /_static/images/form-controls/form-control-transcoded-video.webp
    :width: 30%
    :alt: Form Control Transcoded Video
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
     - Source that will populate the transcoded video picker.
   * - Read Only
     - Make field read-only (can't be changed by the author).
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out.
   * - **Related Data Sources**
     - * |mediaConvertTranscode|

.. |mediaConvertTranscode| replace:: :ref:`Video Transcoding From S3 Repository <form-source-mediaconvert-transcode>`
