:is-up-to-date: True
:last-update: 4.0.0

.. meta::
   :description: CrafterCMS Time form control reference — configure time pickers with custom timezone, populate expression, and set-now link options in content types.
   :keywords: time control, form control, CrafterCMS, content type, time picker, timezone, populate expression, content modeling

.. _form-time:

============
Time Control
============
Date and Time field with a picker.

-------
Example
-------

.. figure:: /_static/images/form-controls/form-control-time-example.webp
    :width: 60%
    :alt: Form Control Time Example 1
    :align: left

|

.. figure:: /_static/images/form-controls/form-control-time-example2.webp
    :width: 60%
    :alt: Form Control Time Example 2
    :align: left

-------------
Configuration
-------------

.. image:: /_static/images/form-controls/form-control-time-properties.webp
    :width: 30%
    :alt: Form Control Time Properties
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
   * - Show Clear Value
     - Display link to clear value
   * - Set Now Link
     - Display link to set time to now
   * - Populated
     - Put in values in the date/time based on populate expression
   * - Populate Expression
     - Populate date/time control using the following expressions: now [+ or -][number][days or weeks or years or hours or minutes]
   * - Use Custom Timezone
     - Allows user to select a timezone
   * - Readonly
     - Make field read-only (can't be changed by the author).
   * - Readonly on Edit
     -
   * - **Constraints**
     -
   * - Required
     - Make field required to fill out.
   * - **Related Data Sources**
     - None
