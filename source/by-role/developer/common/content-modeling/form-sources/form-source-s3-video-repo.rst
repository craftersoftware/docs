:is-up-to-date: True
:last-updated: 4.0.0

.. meta::
   :description: CrafterCMS Video from S3 Repository data source reference — browse and select videos from AWS S3 storage using a configured S3 profile.
   :keywords: S3 video repository, data source, CrafterCMS, AWS S3, video browse, profile ID, form sources, content modeling

.. _form-source-s3-video-repo:

====================================
Video from S3 Repository Data Source
====================================
Data source to select videos from repository.

.. figure:: /_static/images/form-sources/form-source-s3-video-repo.webp
    :width: 20%
    :alt: Source Control Video From S3 Repository

-------------
Configuration
-------------
.. image:: /_static/images/form-sources/form-source-basics-conf.webp
    :align: center
    :width: 40%
    :alt: Source Control Video From S3 Repository Configuration

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
