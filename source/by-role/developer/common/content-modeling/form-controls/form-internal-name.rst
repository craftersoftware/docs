:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Internal Name form control reference — configure the internal name text input with max length, tokenize for indexing, and escape content options.
   :keywords: internal name, form control, CrafterCMS, content type, tokenize, indexing, content modeling

.. _form-internal-name:

=====================
Internal Name Control
=====================
Simple text internal name

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-internal-name-properties.webp
    :width: 30%
    :alt: Form Control Internal Name Properties
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
   * - Max Length
     - Maximum number of characters to accept as input.
   * - Readonly
     - Make field read-only (can't be changed by the author).
   * - Tokenize for Indexing
     - Make the object searchable by this field.
   * - Escape Content
     - Make the object escape script tags.
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out.
   * - Match Pattern
     - Field must match a regex to be accepted.
   * - **Related Data Sources**
     - None
