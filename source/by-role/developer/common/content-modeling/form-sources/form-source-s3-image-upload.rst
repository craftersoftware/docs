:is-up-to-date: True
:last-updated: 4.0.0

.. meta::
   :description: CrafterCMS S3 Image Upload data source reference — upload images directly to AWS S3 storage using a configured S3 profile in content type forms.
   :keywords: S3 image upload, data source, CrafterCMS, AWS S3, image upload, profile ID, form sources, content modeling

.. _form-source-s3-image-upload:

===========================
S3 Image Upload Data Source
===========================
Data source to upload images to S3 repository.

.. figure:: /_static/images/form-sources/form-source-s3-image-upload-repo.webp
    :width: 25%
    :alt: Source Control File Upload S3

-------------
Configuration
-------------

.. image:: /_static/images/form-sources/form-source-basics-conf.webp
    :align: center
    :width: 40%
    :alt: Source Control File Upload S3

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
      - S3 repository path where to store the new uploaded image
    * - Profile Id
      - S3 profile id configured in aws.xml
