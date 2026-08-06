:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS Repeating Group form control reference — capture repeating data records like image carousels or widget lists with min/max occurrence settings.
   :keywords: repeating group, form control, CrafterCMS, content type, carousel, widget, repeating data, content modeling

.. _form-repeating-group:

=======================
Repeating Group Control
=======================
Repeating groups are used when the form has one or several controls that repeat to capture the same data as records. For example: a list of images in a carousel, or a list of widgets on a page

-------
Example
-------
.. figure:: /_static/images/form-controls/form-control-repeating-group-example.webp
    :width: 65%
    :alt: Form Control Repeating Group Example


-------------
Configuration
-------------
.. image:: /_static/images/form-controls/form-control-repeating-group.webp
    :width: 30%
    :alt: Form Control Repeating Group
    :align: center

|

.. list-table::
   :align: left
   :header-rows: 1

   * - Name
     - Description
   * - **Repeat Group Basics**
     -
   * - Title
     - Control title to show the author on the input form
   * - Name / Variable Name
     - Name of variable to store the final result in. This is used by the View layer or the Controllers to gain access to the data during runtime
   * - ICE Group
     - In-Context Edit Group: During ICE editing in Preview mode, this control will be part of this group for editing which means when the author click on the Pencil Tool, they will get all the controls in the same group associated with the specific Pencil Tool
   * - Description
     - Form Control description.
   * - Minimum Occurrences
     - Minimum number of repeating controls
   * - Maximum Occurrences
     - Maximum number of repeating controls
