:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Text Area form control reference — configure multi-line text input with columns, rows, max length, resize, and escape content options.
   :keywords: text area, form control, CrafterCMS, content type, multi-line text, max length, content modeling

.. _form-text-area:

=================
Text Area Control
=================
Simple text-area input.

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-text-area-example.webp
    :width: 50%
    :alt: Form Control Text Area Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-text-area-properties.webp
    :width: 40%
    :alt: Form Control Text Area
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
   * - Columns
     - Number of columns of the text area to show.
   * - Rows
     - Number of rows of the text area to show.
   * - Max Length
     - Maximum number of characters to accept as input.
   * - Allow Resize
     - Allows the resizing feature of the text area.
   * - Read Only
     - Make field read-only (can't be changed by the author).
   * - Escape Content
     - Allows escaping of script tags.
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out
   * - **Related Data Sources**
     - None
