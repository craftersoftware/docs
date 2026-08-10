:is-up-to-date: True
:last-updated: 4.0.0

.. meta::
   :description: CrafterCMS S3 File Upload data source reference — upload files directly to AWS S3 storage using a configured S3 profile in content type forms.
   :keywords: S3 file upload, data source, CrafterCMS, AWS S3, file upload, profile ID, form sources, content modeling

.. _form-source-s3-file-upload:

==========================
S3 File Upload Data Source
==========================
Data source to upload files to S3 repository.

.. figure:: /_static/images/form-sources/form-source-s3-file-upload-repo.webp
    :width: 25%
    :alt: Source Control File Upload S3

|

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
      - S3 repository path where to store the new uploaded file
    * - Profile Id
      - S3 profile id configured in aws.xml
