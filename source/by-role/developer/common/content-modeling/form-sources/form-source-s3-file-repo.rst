:is-up-to-date: True
:last-updated: 4.0.0

.. meta::
   :description: CrafterCMS S3 Repository data source reference — browse and select files from AWS S3 storage using a configured S3 profile in content type forms.
   :keywords: S3 repository, data source, CrafterCMS, AWS S3, file browse, profile ID, form sources, content modeling

.. _form-source-s3-file-repo:

=========================
S3 Repository Data Source
=========================
Data source to select files from S3 repository.

.. figure:: /_static/images/form-sources/form-source-s3-file-repo.webp
    :width: 20%
    :alt: Source Control S3 Repository

-------------
Configuration
-------------
.. image:: /_static/images/form-sources/form-source-basics-conf.webp
    :align: center
    :width: 40%
    :alt: Source Control S3 Repository Configuration

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
    * - Repository Path
      - Path where to browse the S3 repository.
    * - Profile Id
      - S3 profile id configured in aws.xml
