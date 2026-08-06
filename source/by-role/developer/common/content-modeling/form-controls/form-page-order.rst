:is-up-to-date: True
:last-updated: 4.0.0

.. meta::
   :description: CrafterCMS Page Order form control reference — allow authors to control page navigation ordering within the content hierarchy.
   :keywords: page order, form control, CrafterCMS, content type, navigation, page ordering, content modeling

.. _form-page-order:

==================
Page Order Control
==================
Control that allows changing the page order.

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-page-order-example.webp
    :width: 40%
    :alt: Form Control Page Order Example

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-page-order-properties.webp
    :width: 30%
    :alt: Form Control Page Order
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
   * - Read Only
     - Make field read-only (can't be changed by the author).
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out.
   * - **Related Data Sources**
     - None
