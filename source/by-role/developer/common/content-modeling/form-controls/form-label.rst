:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Label form control reference — display static text or HTML labels to guide content authors within content type forms.
   :keywords: label control, form control, CrafterCMS, content type, static text, HTML label, content modeling

.. _form-label:

=============
Label Control
=============
Simple label control in form.

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-label-example.webp
    :width: 60%
    :alt: Form Control Label Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-label-properties.webp
    :width: 30%
    :alt: Form Control Label
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
   * - Text
     - Label text to be displayed in form.
   * - Render as HTML
     -
   * - **Constraints**
     -
   * - **Related Data Sources**
     - None
