:is-up-to-date: True
:last-updated: 4.0.2

.. meta::
   :description: CrafterCMS Simple Taxonomy data source reference — define and use XML taxonomy files for Dropdown and Grouped Checkboxes form controls.
   :keywords: simple taxonomy, data source, CrafterCMS, taxonomy XML, dropdown, grouped checkboxes, form sources, content modeling

.. _form-source-simple-taxonomy:

===========================
Simple Taxonomy Data Source
===========================
Data source to select or create content to be used on the form.

.. figure:: /_static/images/form-sources/form-source-simple-taxonomy.webp
    :width: 30%
    :alt: Form Control Data Source Simple Taxonomy

|

-------------
Configuration
-------------

.. image:: /_static/images/form-sources/form-source-simple-taxonomy-conf.webp
    :align: center
    :width: 40%
    :alt: Form Control Data Source Simple Taxonomy Configuration

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
    * - Component Path
      - Path where to browse the datasource items.

----------------------------------------
Creating an XML file for the data source
----------------------------------------

The Simple Taxonomy Data Source uses xml files to get the components that are going to be used. You can
create your own component to use as data source by following the instructions on :ref:`creating a component<content-type-component>` and just skip creating the view and controller part of the content type if needed.

.. image:: /_static/images/form-sources/form-source-simple-taxonomy-example.webp
    :align: center
    :width: 65%
    :alt: Form Control Data Source Simple Taxonomy Example

|

Here's a sample xml snippet of a simple taxonomy data source file:

.. code-block:: xml

    <items>
        <item>
            <key>guy</key>
	        <value>Guy</value>
	    </item>
	    <item>
	        <key>gal</key>
	        <value>Gal</value>
	    </item>
    </items>
	<file-name>segments.xml</file-name>

