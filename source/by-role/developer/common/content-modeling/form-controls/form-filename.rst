:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Filename form control reference — configure text filename input with max length and read-only options for content type forms.
   :keywords: filename, form control, CrafterCMS, content type, max length, read-only, content modeling

.. _form-filename:

================
Filename Control
================
Simple text filename.

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-filename-example.webp
    :width: 50%
    :alt: Form Control Filename Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-filename-properties.webp
    :width: 30%
    :alt: Form Control Filename Properties
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
   * - Max Length
     - Maximum number of characters to accept as input.
   * - Read Only
     - Make field read-only (can't be changed by the author).
   * - Allow Edit Without Warning
     -
   * - **Constraints**
     -
   * - **Related Data Sources**
     - None
