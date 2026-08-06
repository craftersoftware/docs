:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Grouped Checkboxes form control reference — configure multiple checkbox groups with Simple Taxonomy or Key Value Pairs data sources.
   :keywords: grouped checkboxes, form control, CrafterCMS, content type, simple taxonomy, data source, content modeling

.. _form-grouped-checkboxes:

==========================
Grouped Checkboxes Control
==========================
Several checkboxes (true/false).

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-grouped-checkboxes-example.webp
    :width: 30%
    :alt: Form Control Grouped Checkboxes Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-grouped-checkboxes.webp
    :width: 30%
    :alt: Form Control Grouped Checkboxes Properties
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
   * - Data Source
     - Source that will populate the checkboxes.
   * - Show Select All
     - To select/unselect all options.
   * - List Direction
     - Display list horizontally or vertically
   * - Read Only
     - Make field read-only (can't be changed by the author).
   * - **Constraints**
     -
   * - Minimum Selection
     - Set a minimum amount of checkboxes to be selected.
   * - **Related Data Sources**
     - * :ref:`Simple Taxonomy <form-source-simple-taxonomy>`
       * :ref:`Static Key Value Pairs <form-source-kvp>`
