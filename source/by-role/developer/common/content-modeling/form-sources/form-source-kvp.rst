:is-up-to-date: True
:last-updated: 4.0.0

.. meta::
   :description: CrafterCMS Static Key Value Pairs data source reference — define static key/value option lists for Dropdown and Grouped Checkboxes form controls.
   :keywords: key value pairs, data source, CrafterCMS, static options, dropdown, grouped checkboxes, form sources, content modeling

.. _form-source-kvp:

==================================
Static Key Value Pairs Data Source
==================================
Data source where key/value pairs can be added for its use on a control.

.. figure:: /_static/images/form-sources/form-source-kvp.webp
    :width: 20%
    :alt: Source Control Static Key Value Pairs

|

-------------
Configuration
-------------

.. image:: /_static/images/form-sources/form-source-kvp-conf.webp
    :align: center
    :width: 40%
    :alt: Form Static Key Value Pairs

|

.. list-table::
    :align: left
    :header-rows: 1

    * - Name
      - Description
    * - **Data Source Basics**
      -
    * - Title
      - Data source title to show on the form
    * - Name
      - Name of variable to store the final result in
    * - **Properties**
      -
    * - Data Type
      - Key Value pairs type (String, Integer, Float, Date, HTML).
    * - Options
      - Key Value pairs to be added on the datasource.
    * - Show keys
      - Enable or disable showing the keys

--------------
Adding Options
--------------

When creating a static Key Value Pairs data source you can add the options by clicking on the "Options" input on the
datasource configuration. That will display a dialog with the necessary features to manage the data.

.. image:: /_static/images/form-sources/form-source-kvp-options.webp
    :align: center
    :width: 50%
    :alt: Form Static Key Value Pairs Options

|

- Fill the ``Key`` and ``Value`` fields.
- Clicking on ``Add another`` adds a new key value pair.
- Clicking on the ``Delete`` button removes the pair.
- ``Save & Close`` or ``Cancel`` to finish editing the pairs.
