:is-up-to-date: True
:since-version: 4.0.2

.. meta::
   :description: CrafterCMS Components data source reference — configure drag-and-drop regions for shared and embedded components in content type forms.
   :keywords: components data source, form source, CrafterCMS, shared components, embedded components, drag and drop, content modeling

.. _form-source-components:

======================
Components Data Source
======================
Data source for setting regions on the page that are wired to accept components.

.. figure:: /_static/images/form-sources/form-source-components.webp
    :width: 30%
    :alt: Form Source Components

|

-------------
Configuration
-------------

.. image:: /_static/images/form-sources/form-source-components-conf.webp
    :align: center
    :width: 40%
    :alt: Form Source Components Fields

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
    * - Allow Shared
      - Allows user to browse shared components
    * - Allow Embedded
      - Allows user to browse embedded components
    * - Enable Browse
      - Allows user to browse existing items
    * - Enable Search
      - Allows user to search for existing items
    * - Base Repository Path
      - Path where to store new content created.
    * - Base Browse Path
      - Path where to browse the datasource items.
    * - Content Types
      - Components that can be accepted
    * - Tags
      - String identifiers
