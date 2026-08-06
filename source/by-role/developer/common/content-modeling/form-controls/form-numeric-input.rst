:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Numeric Input form control reference — configure numeric fields with min/max, regex validation, and tokenize for indexing options.
   :keywords: numeric input, form control, CrafterCMS, content type, minimum, maximum, regex, tokenize, content modeling

.. _form-numeric-input:

=====================
Numeric Input Control
=====================
Simple numeric input control.

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-numeric-input-example.webp
        :width: 60%
        :alt: Form Control Numeric Input Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-numeric-input.webp
        :width: 35%
        :alt: Form Control Numeric Input Properties
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
   * - Display Size
     - How much of the input to show on the author input form
   * - Maximum
     - Maximum number of numeric characters to accept as input
   * - Minimum
     - Minimum number of numeric characters to accept as input
   * - Read Only
     - Make field read-only (can't be changed by the author)
   * - Tokenize for Indexing
     - Make the object searchable by this field
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out
   * - Match Pattern
     - Field must match a regex to be accepted
   * - **Related Data Sources**
     - None
   * - **Related Topics**
     - :ref:`experience-builder`
