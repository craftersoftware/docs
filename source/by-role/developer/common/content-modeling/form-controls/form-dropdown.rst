:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Dropdown form control reference — configure dropdown selects with Simple Taxonomy or Static Key Value Pairs data sources in content types.
   :keywords: dropdown, form control, CrafterCMS, content type, simple taxonomy, key value pairs, data source, content modeling

.. _form-dropdown:

================
Dropdown Control
================
Simple Dropdown control

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-dropdown-example.webp
    :width: 50%
    :alt: Form Control Dropdown Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-dropdown.webp
    :width: 50%
    :alt: Form Control Dropdown Properties
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
     - Source that will populate the dropdown.
   * - Allow Empty Value
     - To enable/disable allowing to leave the field empty.
   * - Read Only
     - Make field read-only (can't be changed by the author).
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out.
   * - **Related Data Sources**
     - * :ref:`Simple Taxonomy <form-source-simple-taxonomy>`
       * :ref:`Static Key Value Pairs <form-source-kvp>`
