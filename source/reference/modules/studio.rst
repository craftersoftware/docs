:is-up-to-date: True
:last-updated: 5.0.0

.. highlight:: xml

.. meta::
   :description: Crafter Studio (CrafterCMS authoring module) documentation describing configuration, security and administration.
   :keywords: Crafter Studio, CrafterCMS, content authoring, CMS, publishing, workflow, studio-config

.. _crafter-studio:

==============
Crafter Studio
==============
.. figure:: /_static/images/architecture/crafter-studio.webp
    :alt: Crafter Studio
    :width: 75%
    :align: center

|

Crafter Studio provides all the content management services and enables authoring, version control, workflow, and publishing of content.

|hr|

.. _studio-config:

-------------
Configuration
-------------
Crafter Studio is primarily configured via a single configuration file, ``studio-config.yaml``, and 2 override files that can be used to override the settings in the core configuration file.

The core configuration file for Crafter Studio ``studio-config.yaml`` is located under ``CRAFTER_HOME/bin/apache-tomcat/webapps/studio/WEB-INF/classes/crafter/studio`` and contains pre-configured settings.

.. warning:: Do not change the ``studio-config.yaml`` file directly; override the settings you want to change in one of the override files.

The override files are:

* Studio Configuration Override file **studio-config-override.yaml** located under ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension`` can be accessed by opening the file using your favorite editor
* Global Studio Configuration Override file **studio-config-override.yaml** located under ``CRAFTER_HOME/data/repos/global/configuration`` can be accessed from Studio from the ``Navigation Menu`` under ``Global Config``

The configuration loading order is as follows:

* ``studio-config.yaml`` from the WAR file is loaded first
* ``studio-config-override.yaml`` from the shared folder is loaded next (if it exists)
* ``studio-config-override.yaml`` from the global configuration folder is loaded last (if it exists)

If the same property is present in multiple files, the value from the last configuration file will be used.

You'll note that the first override file from the ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension`` folder resides on the local file system. This makes it easy for system admins but will not replicate across a cluster. The second override file from the ``CRAFTER_HOME/data/repos/global/configuration`` folder is a repository item and will replicate across a cluster. Furthermore, the second override file can be managed from Studio without the need to access the file system. See :ref:`nav-menu-global-config` for more information on how to access the global configuration file from Studio.

.. note:: Changing the configuration files requires a restart of Crafter Studio for the changes to take effect.
.. note:: Environment variables can be used to override any property defined as ``${env:ENVIRONMENT_VARIABLE}`` in the configuration files. This allows you to inject these properties into a vanilla installation without modifying any actual files, which is especially useful when using Docker or Kubernetes. See :ref:`here <environment-variables>` for a list of environment variables used by CrafterCMS.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Studio Configuration Properties
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In this section, we will highlight some of the more commonly used properties in the configuration of Crafter Studio. For a complete list of all the properties, see the ``studio-config.yaml`` file.

.. list-table:: Configuration Properties
    :header-rows: 1

    * - Property
      - Purpose

    * - :ref:`SMTP Configuration (Email) <studio-smtp-config>`
      - Configure the SMTP server to be used by Crafter Studio when sending emails
    * - :ref:`CORS <studio-cors>`
      - Configure CORS
    * - :ref:`Blob Stores <blob-stores>`
      - Configure internally managed static asset stores to handle very large files
    * - :ref:`Project Policy <project-policy-configuration>`
      - Configure constraints for content being added to the project
    * - :ref:`Editable Mime Types <editable-mime-types>`
      - Configure the MIME-types that are editable directly in Crafter Studio
    * - :ref:`Project/Site Configuration <project-configuration>`
      - Configure your project/site configuration
    * - :ref:`UI Configuration <user-interface-configuration>`
      - Configure the Studio UI
    * - :ref:`RTE Configuration <rte-configuration>`
      - Configure the default RTE
    * - :ref:`Preview Deployer Configuration <studio-preview-deployer-config>`
      - Configure your deployer URLs
    * - :ref:`Preview Search Configuration <studio-preview-search-config>`
      - Configure your search URLs
    * - :ref:`Search <studio-search>`
      - Configure Studio search
    * - :ref:`Cache Settings <cache-settings>`
      - Configure the cache control settings for templates and assets
    * - :ref:`Forwarded Headers <studio-forwarded-headers>`
      - Configure forwarded headers
    * - :ref:`Policy Headers <studio-policy-headers>`
      - Configure policy headers
    * - :ref:`crafterSite Cookie Domain <studio-crafterSite-cookie-domain>`
      - Configure the ``crafterSite`` cookie domain
    * - :ref:`Deployer HTTP Requests <studio-deployer-http-request-timeout>`
      - Configure timeout for Deployer HTTP requests
    * - :ref:`Serverless Delivery Targets <studio-serverless-delivery-targets>`
      - Configure serverless delivery
    * - :ref:`CloudFormation Capabilities <studio-cloudformation-capabilities>`
      - Configure capabilities for CloudFormation stack
    * - :ref:`Validations Regex <studio-validations-regex>`
      - Configure the regex used for validating various inputs
    * - :ref:`Disk Monitoring <studio-disk-monitoring>`
      - Configure the disk monitoring notifications and thresholds
    * - :ref:`Workflow Notification Configuration <notifications-configuration>`
      - Configure the workflow notifications
    * - :ref:`Commit Message <studio-commit-message>`
      - Configure the commit messages used by Crafter Studio
    * - :ref:`Audit Log <studio-audit-log>`
      - Configure whether to enable/disable the Studio audit log job for operations not performed through Crafter Studio
    * - :ref:`Publishing Blacklist <publishing-blacklist>`
      - Configure the publishing blacklist
    * - :ref:`Configuration Files Maximum <configuration-files-maximum>`
      - Configure the maximum length of configuration content
    * - :ref:`Git Configuration <git-configuration>`
      - Configure Git properties
    * - :ref:`Content Type Editor Configuration <content-type-editor-configuration>`
      - Configure the content types
    * - :ref:`Dependency Resolver Configuration <dependency-resolver-configuration>`
      - Configure the dependency resolver
    * - :ref:`Project Tools Configuration <project-tools-configuration>`
      - Configure the project tools
    * - :ref:`Asset Processing Configuration <asset-processing-configuration>`
      - Configure asset processing
    * - :ref:`AWS Profiles Configuration <aws-profile-configuration>`
      - Configure AWS integration
    * - :ref:`Box Profiles Configuration <box-profile-configuration>`

	.. version_tag::
		:label: Until
		:version: 4.2

      - Configure Box integration
    * - :ref:`WebDAV Profiles Configuration <webdav-profiles-configuration>`
      - Configure WebDAV integration

.. TODO Add more configuration properties

|

|hr|

.. _studio-smtp-config:

""""""""""""""""""""""""""
SMTP Configuration (Email)
""""""""""""""""""""""""""
This section allows the user to set up a mail client by configuring the SMTP server to send emails from Crafter Studio, such as when authors request to publish content or when a request to publish has been approved.

.. code-block:: yaml
    :linenos:
    :caption: *CRAFTER_HOME/data/repos/global/configuration/studio-config-override.yaml*

    ##################################################
    ##        SMTP Configuration (Email)            ##
    ##################################################
    # Default value for from header when sending emails.
    # studio.mail.from.default: admin@example.com
    # SMTP server name to send emails.
    # studio.mail.host: ${env:MAIL_HOST}
    # SMTP port number to send emails.
    # studio.mail.port: ${env:MAIL_PORT}
    # SMTP username for authenticated access when sending emails.
    # studio.mail.username:
    # SMTP password for authenticated access when sending emails.
    # studio.mail.password:
    # Turn on/off (value true/false) SMTP authenaticated access protocol.
    # studio.mail.smtp.auth: false
    # Enable/disable (value true/false) SMTP TLS protocol when sending emails.
    # studio.mail.smtp.starttls.enable: false
    # Enable/disable (value true/false) SMTP EHLO protocol when sending emails.
    # studio.mail.smtp.ehlo: true
    # Enable/disable (value true/false) debug mode for email service. Enabling debug mode allows tracking/debugging communication between email service and SMTP server.
    # studio.mail.debug: false

|

|hr|

.. _studio-cors:

""""""""""""""""""""""""""""""""""""
Cross-Origin Resource Sharing (CORS)
""""""""""""""""""""""""""""""""""""
Cross-Origin Resource Sharing (CORS) is an HTTP-header based mechanism that allows a web page to access resources from
a different domain.

The following section of Studio's configuration overrides allows you to set CORS

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:
    :emphasize-lines: 10

    ################################################################
    ##                             CORS                           ##
    ################################################################
    # This is configured as permissive by default for ease of deployment
    # Remember to tighten this up for production

    # Disable CORS headers completely
    # studio.cors.disable: false
    # Value for the Access-Control-Allow-Origin header
    # studio.cors.origins: '*'
    # Value for the Access-Control-Allow-Headers header
    # studio.cors.headers: '*'
    # Value for the Access-Control-Allow-Methods header
    # studio.cors.methods: '*'
    # Value for the Access-Control-Allow-Credentials header
    # studio.cors.credentials: true
    # Value for the Access-Control-Max-Age header
    # studio.cors.maxage: -1

The CORS origins accept regex patterns. Values are split using ``,``. Remember that commas inside
patterns need to be escaped with a ``\`` like:
``studio.cors.origins: 'http://localhost:[8000\,3000],http://*.other.domain'``

|

|hr|

.. _blob-stores:

"""""""""""
Blob Stores
"""""""""""
Blob Stores allow you to host internally managed static asset stores to handle very large files. The Blob Stores configuration file allows you to configure stores for assets with the corresponding information required by the store being used.
To modify the Blob Stores configuration, click on |projectTools| from the bottom of the *Sidebar*, then click on **Configuration** and select **Blob Stores** from the list.

.. image:: /_static/images/site-admin/config-open-blob-stores.webp
    :alt: Configurations - Open Blob Stores Configuration
    :width: 45%
    :align: center

~~~~~~
Sample
~~~~~~
Here's a sample Blob Stores Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample "blob-stores-config.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-blob-stores-config.xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/blob-stores-config.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|

where:

- The environment variables (*env:VARIABLE_NAME*) values are set in the ``crafter-setenv.sh`` file. See :ref:`here <env-var-serverless-deployments>` for more information on environment variables used in serverless deployments

Remember to encrypt your credentials. For more information on how to manage/encode your secrets such as AWS credentials,
please see :ref:`managing-secrets`

For better security and control, we recommend setting an AWS profile via the ``crafter-setenv.sh`` file instead of
configuring the encrypted credentials in the blob stores configuration file. This allows you to have an IAM user
per developer, which is a better approach than a single user whose credentials are included (encrypted) in the
configuration file. In this way, if you need to rotate or remove the credentials of a single user, the access of
other users won't be affected.

To set an AWS profile, using your favorite editor, open ``CRAFTER_HOME/bin/crafter-setenv.sh`` and add the following:

.. code-block:: bash

   export AWS_PROFILE=YOUR_AWS_PROFILE

|

*where* ``YOUR_AWS_PROFILE`` is the AWS profile you wish to use for the blob store. See :ref:`here <aws-profile-configuration>`
for more information on configuring AWS profiles.

When using an AWS profile, you can now remove the ``<credentials />`` section in your blob stores configuration file.

Remember to restart your CrafterCMS install for the changes you made to take effect.

~~~~~~~~~~~~~~~~~~~~~~~
Using AWS Service Roles
~~~~~~~~~~~~~~~~~~~~~~~
CrafterCMS supports AWS access without using access/secret keys, by setting AWS service roles on your machine

Simply follow the instructions here for attaching an IAM role to your instance:
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#attach-iam-role

Remember to remove the ``<credentials />`` section in your blob stores configuration file.

.. _publishing-assets-from-blob-stores:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Publishing Assets from the Blob Stores
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
CrafterCMS supports managing assets in external storage through workflow and publishing mechanics.
This allows uploading assets to an external storage for preview, that can then be published to either a live or a staging
(depending on if staging is setup for your Crafter install) external storage, thus making the external assets available
to delivery only after the assets have been published to the live external storage.

The external storage could be in the cloud, such as AWS S3 or some other storage solution that is outside of where CrafterCMS is installed.

''''''''''''''''''''''''''''''''
Configuring the External Storage
''''''''''''''''''''''''''''''''
First we'll need to setup the external storage to be used by CrafterCMS.
To setup an external storage for assets, open the **Sidebar**, then click on |projectTools| -> *Configurations*. Select ``Blob Stores`` from the dropdown and fill in the required information.

.. code-block:: xml

   <blobStore>
     <id/>
     <type/>
     <pattern/>
     <mappings>
       <mapping>
         <publishingTarget/>
         <storeTarget/>
         <prefix/>
       </mapping>
     </mappings>
     <configuration/>
   </blobStore>

|

To see more information on the Blob Stores configuration, see :ref:`above <blob-stores>`

After setting up the ``Blob Stores`` configuration, you may now use the external storage for uploading using the various upload methods provided by Crafter Studio, and publishing to live or staging if it's setup.

'''''''
Example
'''''''
Let's take a look at an example of setting up an external storage for preview, staging and live and then uploading and finally publishing assets to the external storage we setup. In the example, we will use AWS S3 as the external storage and the Website Editorial blueprint in Crafter Studio to create our project.

**Prerequisites:**

#. Project created using the Website Editorial blueprint.
#. AWS S3 bucket/s. A single bucket can be used as long as all the ``publishingTarget`` uses a unique ``prefix``, or a separate bucket can be created for each ``publishingTarget``, or a combination of both.

   For our example, we will be using two buckets. One for authoring and another for delivery. The following buckets were setup in AWS S3: *my-authoring-bucket* for authoring (used by publishing target ``preview`` with the prefix *sandbox* and publishing target ``staging`` with the prefix *staging*) and *my-deli-bucket* for delivery.

**Here are the steps:**

#. Enable staging (optional)
#. Setup the blob store
#. Upload files
#. Publish the files to staging (if setup)
#. Publish the files into live

Let's begin:

1. Enable Staging (optional)
''''''''''''''''''''''''''''
This step is optional but for our example, we wanted to be able to publish to staging, so in this step, we will first enable staging. In your Studio, click on |projectTools| -> *Configuration* -> *Project Configuration* and set ``enable-staging-environment`` to ``true`` to enable staging

  .. code-block:: xml
     :emphasize-lines: 2

     <published-repository>
         <enable-staging-environment>true</enable-staging-environment>
         <staging-environment>staging</staging-environment>
         <live-environment>live</live-environment>
     </published-repository>

  |

For more information on staging, see :ref:`staging-env`


2. Setup Blob Store
'''''''''''''''''''
In your Studio, click on |projectTools| -> *Configuration* -> *Blob Stores* and fill in the required information to setup the S3 buckets for the preview, staging and live.

   .. code-block:: xml
      :caption: *CRAFTER_HOME/data/repos/sites/sandbox/SITENAME/sandbox/config/studio/blob-stores-config.xml*
      :linenos:
      :emphasize-lines: 5,9,14,19,24,25,27

      <blobStores>
        <blobStore>
          <id>s3-default</id>
          <type>s3BlobStore</type>
          <pattern>/static-assets/item/.*</pattern>
          <mappings>
            <mapping>
              <publishingTarget>preview</publishingTarget>
              <storeTarget>my-authoring-bucket</storeTarget>
              <prefix>sandbox</prefix>
            </mapping>
            <mapping>
              <publishingTarget>staging</publishingTarget>
              <storeTarget>my-authoring-bucket</storeTarget>
              <prefix>staging</prefix>
            </mapping>
            <mapping>
              <publishingTarget>live</publishingTarget>
              <storeTarget>my-delivery-bucket</storeTarget>
            </mapping>
          </mappings>
          <configuration>
            <credentials>
              <accessKey>xxxxxxxxx</accessKey>
              <secretKey>xxxxxxxxx</secretKey>
            </credentials>
            <region>us-west-1</region>
            <pathStyleAccess>true</pathStyleAccess>
          </configuration>
        </blobStore>
      </blobStores>

   |

**where the highlighted items above refers to:**

* **pattern:** the regex to match file paths (the path in Studio that when used will access the external storage, ``/static-assets/item/.*`` for our example above)
* **mappings.mapping.storeTarget:** the name of the storeTarget inside the store (AWS S3 buckets, ``my-authoring-bucket`` and ``my-deli-bucket`` for our example above)
* **configuration:** configuration specific for the store type (For AWS S3, it requires credentials to access the buckets)

Remember to encrypt your credentials. For more information on how to manage/encode your secrets such as AWS credentials,
please see :ref:`managing-secrets`

To see more information on the Blob Stores configuration, see :ref:`above <blob-stores>`


3. Upload files
'''''''''''''''
There are various ways to upload files in Crafter Studio. Here's a few ways we can upload to the external storage:

#. Upload through a picker with corresponding data source setup in a content type
#. Upload using the ``Bulk Upload`` or ``Upload`` right-click option

Let's take a closer look:

#. One way of uploading files is through the use of a picker (image, video, item selector) with its corresponding data source with the ``Repository Path`` property set to the ``pattern`` we defined in the ``Blob Stores`` configuration file.

   For our example, open the **Page - Article** content type by opening the **Sidebar**, then click on |projectTools| -> *Content Types*, then choose the template name ``Page - Article``.

   In the **Page - Article** content type, notice that the ``Repository Path`` property of the ``Upload Image`` data source is set to: ``/static-assets/item/images/{yyyy}/{mm}/{dd}/``, which falls into the file path pattern ``/static-assets/item/.*`` we setup in the ``Blob Stores`` configuration file

   .. image:: /_static/images/site-admin/ext-storage/setup-datasource.webp
      :align: center
      :alt: Setup data source to use the file path pattern in Blob Stores
      :width: 95%

   Let's change the image used in one of the articles in the project.

   From the **Sidebar**, navigate to ``/articles/2016/6`` then right click on ``Coffee is Good for Your Health`` then select ``Edit``.

   Scroll down to the ``Content`` section, then click on the ``Replace`` button next to the **Image** field, then select ``Upload Images``. Select the file you want to upload. In our example, the file ``new1.png`` will be uploaded to ``static-assets/item/images/2020/03/27``.

   .. image:: /_static/images/site-admin/ext-storage/upload-image-with-picker.webp
      :align: center
      :alt: Upload image using an image picker
      :width: 95%

   |

   After uploading the file, we should see it in the AWS S3 bucket for authoring ``my-authoring-bucket`` in the sandbox:

   .. image:: /_static/images/site-admin/ext-storage/picker-uploaded-img-in-bucket.webp
      :align: center
      :alt: Image uploaded using the image picker is now in the S3 bucket
      :width: 95%

#. Next we'll try uploading using the ``Upload`` right-click option.

   Open the **Sidebar** and navigate to ``static-assets/item``. Create a folder named ``docs`` under ``item``. Right click on the newly created folder and select ``Upload`` to upload a single file, or ``Bulk Upload`` to upload multiple files

   In the example below, two files were uploaded to the ``docs`` folder.

   .. image:: /_static/images/site-admin/ext-storage/uploaded-files-to-s3.webp
       :align: center
       :alt: "s3" folder created under "static-assets"
       :width: 35%

   |

   When you upload files to the ``docs`` folder, the files get uploaded to the ``sandbox`` of the ``my-authoring-bucket`` previously setup.

   .. image:: /_static/images/site-admin/ext-storage/s3-preview-bucket.webp
       :align: center
       :alt: Files in preview in "s3" my-authoring-bucket
       :width: 85%

|


5. Publish the files to staging
'''''''''''''''''''''''''''''''
The next step in our example is to publish the files to ``staging``. To publish a file to ``staging``, navigate to the file in the ``Sidebar`` then right click on the file, and select ``Publish`` or open the ``Dashboard`` and select the file/s you want to publish to ``staging`` in the ``Unpublished Work`` widget and click on ``Publish`` from the context nav.

The ``Publish`` dialog will come up. Remember to select ``staging`` for the ``Publishing Target``

.. image:: /_static/images/site-admin/ext-storage/publish-to-staging.webp
    :align: center
    :alt: Publish file to staging in Studio
    :width: 65%

|

When the file/s are published to ``staging``, the files get published to the ``staging`` branch of the ``my-authoring-bucket`` in s3.

.. image:: /_static/images/site-admin/ext-storage/s3-staging-bucket.webp
    :align: center
    :alt: Published files to staging in "s3" my-authoring-bucket
    :width: 85%

|

6. Publish the files to delivery
''''''''''''''''''''''''''''''''
Finally, we'll publish the file/s to ``live``. To publish a file to ``live``, navigate to the file in the ``Sidebar`` then right click on the file, and select ``Publish`` or open the ``Dashboard`` and select the file/s you want to publish to ``live`` in the ``Unpublished Work`` widget and click on ``Approve & Publish`` from the context nav.

The ``Publish`` dialog will come up. Remember to select ``live`` for the ``Publishing Target``

.. image:: /_static/images/site-admin/ext-storage/publish-to-live.webp
    :align: center
    :alt: Publish file to live in Studio
    :width: 65%

|

When the file/s are published to ``live``, the file/s get published to the ``my-deli-bucket`` in s3.

.. image:: /_static/images/site-admin/ext-storage/s3-delivery-bucket.webp
    :align: center
    :alt: Published file/s to live in "s3" my-delivery-bucket
    :width: 85%

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Setting up Staging for Existing Projects
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
When adding the ``staging`` publishing target to an established project that uses external storage, Studio does not clone the assets in external storage for ``live`` into ``staging``. Performing a bulk publish to ``staging`` also does not work at this time. This is because Studio does not publish to ``staging``, assets in a LIVE, UNEDITED state.

To sync the external storage for ``staging`` with ``live``, you must copy the assets in the ``live`` external storage to the ``staging`` external storage.

Let's take a look at an example of adding ``staging`` to an existing project.

**Prerequisites:**

#. Project created using the Website Editorial blueprint with external storage setup for ``live`` and assets already published to ``live`` (See example above for setting up external storage for a project. Remember to not setup ``staging`` as we will be doing it in this example)
#. AWS S3 bucket to be used by the ``staging`` publishing target. For our example, we will be using the bucket ``my-staging`` setup in AWS S3.

**Here are the steps:**

#. Enable staging in Studio
#. Setup the blob store in Studio
#. Copy assets in live to staging in external storage

Let's begin:

#. **Enable staging**

   In your Studio, click on |projectTools| -> *Configuration* -> *Project Configuration* and set ``enable-staging-environment`` to ``true`` to enable staging

     .. code-block:: xml
        :emphasize-lines: 2

        <published-repository>
          <enable-staging-environment>true</enable-staging-environment>
          <staging-environment>staging</staging-environment>
          <live-environment>live</live-environment>
        </published-repository>

     |

   For more information on staging, see :ref:`staging-env`

2. **Setup Blob Store**

   Setup ``staging`` in the Blob Store by adding the following to your ``Blob Stores`` configuration. In your Studio, click on |projectTools| -> *Configuration* -> *Blob Stores* and fill in the required information to setup the S3 bucket for staging.

     .. code-block:: xml

        <mapping>
          <publishingTarget>staging</publishingTarget>
          <storeTarget>my-staging</storeTarget>
        </mapping>

     |


   To see more information on the Blob Stores configuration, see :ref:`above <blob-stores>`

#. **Copy assets in** ``live`` **to** ``staging`` **in external storage**

   In your AWS console, copy the contents of your delivery bucket

   .. image:: /_static/images/site-admin/ext-storage/s3-copy-delivery.webp
      :align: center
      :alt: Copy assets in the delivery bucket
      :width: 85%

   |

   Paste the copied content into the staging bucket ``my-staging``

   .. image:: /_static/images/site-admin/ext-storage/s3-staging-bucket-content.webp
      :align: center
      :alt: Assets copied from delivery bucket to staging bucket
      :width: 85%

   |

   The ``live`` and ``staging`` external storage is now synced.

|hr|

.. _project-policy-configuration:

""""""""""""""
Project Policy
""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.0.0

The project policy configuration file allows the administrator to configure constraints for content being added to the project
(via uploads), such as filename/item name constraints, minimum/maximum size of files, permitted content types or file types (MIME-types), etc.

*Note that the project policy does not apply to content created directly on disk via the Git or APIs.*

CrafterCMS supports the following project policies:

- Filename allowed patterns and automatic renaming rules
- File size limits
- MIME-type limits
- Content-type limits

.. note::
    .. version_tag::
        :label: Since
        :version: 4.1.4

    The default policy for filenames and automatic renaming rules is to lowercase everything except items under: ``/scripts``, ``/templates``, and ``/static-assets/app``


To modify the project policy configuration, click on |projectTools| from the *Sidebar*, then click on **Configuration** and
select **Project Policy Configuration** from the list.

.. image:: /_static/images/site-admin/config-open-project-policy-config.webp
   :alt: Configurations - Open Project Policy Configuration
   :width: 45 %
   :align: center

.. note::

   The Project Policy Configuration file (site-policy-config.xml) is not overridden by environment.
   Learn more about Studio multi-environment support in :ref:`studio-multi-environment-support`.

~~~~~~
Sample
~~~~~~
Here's a sample Project Policy Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample project policy configuration</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-site-policy-config.xml
    :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/site-policy-config.xml*
    :language: xml
    :linenos:

.. raw:: html

   </details>

|
|

.. raw:: html

   <hr>

~~~~~~~~
Examples
~~~~~~~~
Let's take a look at some example project policy configurations.

''''''''''
Mime Types
''''''''''
The example configuration below (as seen in the default project policy configuration) disallows svg image
file uploads:

.. code-block:: xml
   :emphasize-lines: 7-9

      <!-- disable svg files -->
      <statement>
        <target-path-pattern>/.*</target-path-pattern>
        <permitted>
          <mime-types>*/*</mime-types>
        </permitted>
        <denied>
          <mime-types>image/svg+xml</mime-types>
        </denied>
      </statement>

Whenever a user tries to upload an svg image, the user will see a message on the screen informing them that
it doesn't comply with the project policies and can’t be uploaded like below:

.. image:: /_static/images/site-admin/project-policy-cannot-upload.webp
   :alt: Project Policy Configuration - Do not allow svg file uploads
   :width: 55 %
   :align: center

|

''''''''''''''''
File Size Limits
''''''''''''''''
Limiting file size of uploads is supported. Simply add ``<minimum-file-size/>`` and/or <maximum-file-size/>
under ``<permitted>`` where the minimum and maximum file sizes are in bytes

The example configuration below limits image uploads to less than 1MB in folder ``/static-assets/images/``.

.. code-block:: xml

   <!-- Example: only allow images of less than 1 MB -->
   <statement>
     <target-path-pattern>/static-assets/images/.*</target-path-pattern>
     <permitted>
       <maximum-file-size>1000000</maximum-file-size>
       <mime-types>image/*</mime-types>
     </permitted>
   </statement>

Whenever a user tries to upload an image that is larger than 1 MB in the ``/static-assets/images/`` folder, the user
will see a message on the screen informing them that it doesn’t comply with project policies and can’t be uploaded like below:

.. image:: /_static/images/site-admin/project-policy-img-too-big.webp
   :alt: Project Policy Configuration - Do not allow images greater than 1 MB
   :width: 55 %
   :align: center

|

''''''''''''''''''''
Transform File Names
''''''''''''''''''''
CrafterCMS supports transforming filenames of uploaded files and convert the filenames to lower case or upper case.
Simply set **caseTransform** to either ``lowercase`` or ``uppercase`` in ``target-regex`` to convert to your required case.

The example configuration below (as seen in the default project policy configuration) converts
parenthesis ( ``(`` and ``)`` ) and spaces in filenames to a dash ( ``-`` )
and lower cases all the letters in filenames for files uploaded to the ``/static-assets/`` folder .

.. code-block:: xml

   <statement>
     <target-path-pattern>/static-assets/.*</target-path-pattern>
     <permitted>
       <path>
         <source-regex>[\(\)\s]</source-regex>
         <target-regex caseTransform="lowercase">-</target-regex>
       </path>
     </permitted>
   </statement>

Whenever a user uploads a file with upper case letters or spaces and parenthesis in the filename, in the
``/static-assets/`` folder, the user will see a message on the screen informing them that it doesn’t comply
with project policies and will be asked if they would like to continue upload with the suggested name like below:

.. image:: /_static/images/site-admin/project-policy-convert-to-lower-case.webp
   :alt: Project Policy Configuration - Convert filenames to lower case
   :width: 55 %
   :align: center


|hr|

.. _editable-mime-types:

"""""""""""""""""""
Editable Mime Types
"""""""""""""""""""
Here's the default list of MIME-types editable in Studio:

.. code-block:: yaml

    # Item MIME-types that are editable directly in Crafter Studio
    studio.content.item.editableTypes:
    - text/plain
    - text/html
    - text/css
    - text/x-freemarker
    - application/javascript
    - application/json
    - application/xml
    - application/xhtml+xml

These can be updated as needed by overriding the property in one of the override files.

|

|hr|

.. _project-configuration:

""""""""""""""""""""""""""
Project/Site Configuration
""""""""""""""""""""""""""
Crafter Studio allows to configure many aspects of a project/site.
The project configuration file contains the primary configuration for Crafter Studio's behavior. Each project has
its own project configuration file that controls its behavior independently of other projects.

To modify the project configuration, click on |projectTools| from the *Sidebar*, then click on **Configuration**
and select **Project Configuration** from the list.

.. image:: /_static/images/site-admin/config-open-project-config.webp
    :alt: Configurations - Open Project Configuration
    :width: 45%
    :align: center

|

~~~~~~
Sample
~~~~~~
Here's a sample Project Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample Project Configuration</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-site-config.xml
     :language: xml
     :linenos:


.. raw:: html

   </details>

|

~~~~~~~~~~~~~~~~
Enabling Staging
~~~~~~~~~~~~~~~~
The ``staging`` publishing target is an intermediate publishing target where the project can be fully exercised.
To enable the ``staging`` publishing target, set the following to ``true``:

.. code-block:: xml

   <published-repository>
     <enable-staging-environment>false</enable-staging-environment>
   </published-repository>

|

See :ref:`staging-env` for more information on how to setup the ``staging`` publishing target

~~~~~~~~~~~~~~~~~~~~~~~
Escaping Content Fields
~~~~~~~~~~~~~~~~~~~~~~~
To add/remove escaped content fields, modify the following:

.. code-block:: xml

   <!--
   Specifies the regular expression patterns to match content type field
   names that require CDATA escaping.
   -->
   <cdata-escaped-field-patterns>
     <pattern>(_html|_t|_s|_smv|mvs)$</pattern>
     <pattern>internal-name</pattern>
   </cdata-escaped-field-patterns>

|

For more information on escaping content fields, see the notes under :ref:`Variable Names and Search Indexing <variable-names-search-indexing>`

~~~~~~~~~~~~~~~~~~~
Publishing Comments
~~~~~~~~~~~~~~~~~~~
To make comments mandatory for different publishing methods, simply set to ``true`` any applicable methods the
site administrators want to require comments when publishing.

.. code-block:: xml

   <publishing>
     <comments>
       <!-- Global setting would apply to all -->
       <required>false</required>
       <!-- Additional (also optional) specific overrides -->
       <!-- <delete-required/> -->
       <!-- <bulk-publish-required/> -->
       <!-- <publish-by-commit-required/> -->
       <!-- <publish-required/> -->
     </comments>
   </publishing>

|

See :ref:`publishing-and-status` for more information on the different publishing methods available from ``Project Tools``

.. _project-config-require-peer-review:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Requiring Peer Review for Publishing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. version_tag::
    :label: Since
    :version: 4.0.0

A publisher review workflow option is available to make approval of a publish request mandatory for users with
publish permission. To enable the publisher review workflow option, set ``requirePeerReview`` to ``true``.

.. code-block:: xml

   <!--
        This workflow parameter disallows users with _Publish_ permission from publishing their own work.
        Work performed by a user must be approved by a different reviewer before it can be published.
        Set the value to true to enable this feature.
   -->
   <workflow>
     <publisher>
       <requirePeerReview>true</requirePeerReview>
     </publisher>
   </workflow>

.. TODO: Is this the best place for content monitoring or project admin? It's now in both to some extent with project admin having the full article and this links to it.

~~~~~~~~~~~~~~~~~~
Content Monitoring
~~~~~~~~~~~~~~~~~~
Content monitoring allows you to configure watches and notifications on your project. To add content monitors, add the following:

.. code-block:: xml

   <contentMonitoring>
     <monitor>
       <name>Content Expiring Tomorrow</name>
       <query>expired_dt:[now+1d/d TO now+2d/d]</query>
       <paths>
         <path>
           <name>All Site</name>
           <pattern>/site/.*</pattern>
           <emailTemplate>contentExpiringSoon</emailTemplate>
           <emails>admin@example.com</emails>
           <locale>en</locale>
         </path>
       </paths>
     </monitor>
   </contentMonitoring>

|

See :ref:`content-monitoring` for more information on configuring content monitoring.

.. _project-config-protected-folders:

~~~~~~~~~~~~~~~~~
Protected Folders
~~~~~~~~~~~~~~~~~
The protected folders settings allows you to configure paths that can't be deleted, renamed or moved in addition to
the following paths that are protected by default:

- ``/site/website/index.xml``
- ``/site/components``
- ``/site/taxonomy``
- ``/static-assets``
- ``/templates``
- ``/scripts``
- ``/sources``

To add protected folder/s in your project, add your folder path/s like below:

.. code-block:: xml

   <protected-folders-patterns>
     <pattern>/YOUR/FOLDER/PATH/PATTERN</pattern>
     <pattern>/MORE/FOLDER/PATH/PATTERN</pattern>
     ...
   </protected-folders-patterns>

|

Remember to replace ``/YOUR/FOLDER/PATH/PATTERN`` and ``/MORE/FOLDER/PATH/PATTERN`` with the actual folder path
pattern/s that you would like to be protected.

To see an example of configured protected folders, create a project using the ``Video Center`` blueprint from the
Public Marketplace in the ``Create Project`` dialog then open the
``Sidebar`` -> |projectTools| -> ``Configuration`` -> ``Project Configuration``. Scroll down to the
``<protected-folders-patterns>`` tag:

.. code-block:: xml

   <!--
   Prevent deleting, renaming or cutting root folders of sidebar
   -->
   <protected-folders-patterns>
     <pattern>/site/streams</pattern>
     <pattern>/site/videos</pattern>
     <pattern>/site/origins</pattern>
   </protected-folders-patterns>

|hr|

.. _user-interface-configuration:

""""""""""""""""
UI Configuration
""""""""""""""""
Crafter Studio's UI is highly configurable and allows you to customize the look and feel of the UI per project to suit
your needs.

The user interface configuration file defines the widgets shown in the user interface. It allows the user to
configure the items available for interaction in Studio.

It shows different projections of the content in addition to other tools to assist in content authoring
and site administration.

The UI is made up of configurable widgets and can be extended or modified by adding/removing/configuring widgets.
Plugins can make use of this by adding themselves to the UI where required. See :ref:`CrafterCMS Plugin Descriptor <plugin-descriptor-file>` for more information on how a plugin can be wired in the user interface configuration.

Here's a screenshot of Studio showing some of the widgets in the UI in red circles that are defined in the user interface configuration.

.. image:: /_static/images/site-admin/ui-config-widgets.webp
   :alt: Configurations - User Interface Configuration Widgets
   :width: 85 %
   :align: center

|

Here's an annotated version of some of the widgets in the user interface shown in the image above.

.. code-block:: xml
   :linenos:

   <siteUI>
     <widget id="craftercms.components.ToolsPanel">...</widget>     Sidebar widget
     <widget id="craftercms.components.ICEToolsPanel">...</widget>  Experience Builder widget
     <widget id="craftercms.components.Launcher">...</widget>       Navigation Menu widget
     <widget id="craftercms.components.PreviewToolbar">...</widget> Toolbar widget
     <widget id="craftercms.components.Dashboard">...</widget>      Dashboard widget
     <widget id="craftercms.components.TinyMCE">...</widget>        TinyMCE widget
     <references>
        <reference id="craftercms.siteTools">...</reference>
        <reference id="craftercms.freemarkerCodeSnippets">...</reference>
        <reference id="craftercms.groovyCodeSnippets">...</reference>
     </references>
   </siteUI>

|

.. _sidebar-widget:

Let's take a look at the sidebar widget as an example. The Sidebar widget is a panel located on the left
side of Studio. The Sidebar contains, the ``Dashboard``, various path navigators and path navigator trees
such as ``Pages``, ``Components``, etc., and the ``Project Tools``, which are also widgets.
Here's the configuration:

.. code-block:: xml
   :linenos:
   :emphasize-lines: 4-6,11-14, 39-45

   <widget id="craftercms.components.ToolsPanel">
     <configuration>
       <widgets>
         <widget id="craftercms.components.ToolsPanelEmbeddedAppViewButton">
           <configuration>
             <title id="words.dashboard" defaultMessage="Dashboard"/>
             <icon id="@mui/icons-material/DashboardRounded"/>
             <widget id="craftercms.components.SiteDashboard"/>
           </configuration>
         </widget>
         <widget id="craftercms.components.PathNavigator">
           <configuration>
             <id>Pages</id>
             <label>Pages</label>
             <icon id="@mui/icons-material/DescriptionOutlined"/>
             <rootPath>/site/website</rootPath>
             <locale>en</locale>
           </configuration>
         </widget>
         <widget id="craftercms.components.PathNavigator">
           <configuration>
             <id>Components</id>
             <label>Components</label>
             <icon id="craftercms.icons.Component"/>
             <rootPath>/site/components</rootPath>
             <locale>en</locale>
           </configuration>
         </widget>
         <widget id="craftercms.components.PathNavigator">
           <configuration>
             <id>Taxonomy</id>
             <label>Taxonomy</label>
             <icon id="@mui/icons-material/LocalOfferOutlined"/>
             <rootPath>/site/taxonomy</rootPath>
             <locale>en</locale>
           </configuration>
         </widget>
         ...
         <widget id="craftercms.components.ToolsPanelEmbeddedAppViewButton">
           <permittedRoles>
             <role>admin</role>
             <role>developer</role>
           </permittedRoles>
           <configuration>
             <title id="siteTools.title" defaultMessage="Project Tools"/>
             <icon id="@mui/icons-material/ConstructionRounded"/>
             <widget id="craftercms.components.EmbeddedSiteTools"/>
           </configuration>
         </widget>
       </widgets>
     </configuration>
   </widget>
   ...

|

To modify the user interface configuration, click on |projectTools| from the *Sidebar*, then click on **Configuration**
and select **User Interface Configuration** from the list.

.. image:: /_static/images/site-admin/config-open-user-interface-config.webp
   :alt: Configurations - Open User Interface Configuration
   :width: 85 %
   :align: center

|

|hr|

~~~~~~
Sample
~~~~~~
Here's a sample User Interface Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample "ui.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-ui.xml
      :language: xml
      :linenos:


.. raw:: html

   </details>

|
|

|hr|

.. _widget-permissions:

~~~~~~~~~~~~~~~~~~
Widget Permissions
~~~~~~~~~~~~~~~~~~
Limiting who can access a widget via roles is through ``permittedRoles`` in the user interface configuration.
Simply add the following to the widget you want available only to users with the permitted role(s)

.. code-block:: xml
   :linenos:

   <permittedRoles>
     <role>ALLOWED_ROLE</role>
     ...
   </permittedRoles>

|

where ALLOWED ROLE is a role defined in Studio that is allowed to access the widget. See :ref:`roles-and-permissions` for a list of default roles in Crafter Studio

Let's take a look at an example in the configuration where access to the ``Project Tools`` widget is limited to users with the roles ``admin`` and ``developer``.

.. code-block:: xml
   :linenos:
   :emphasize-lines: 2-5

   <widget id="craftercms.components.ToolsPanelPageButton">
     <permittedRoles>
       <role>admin</role>
       <role>developer</role>
     </permittedRoles>
     <configuration>
       <title id="siteTools.title" defaultMessage="Site Tools"/>
       <icon id="@mui/icons-material/TuneRounded"/>
       <widgets>
         <widget id="craftercms.components.SiteToolsPanel"/>
       </widgets>
     </configuration>
   </widget>

|

Here's the sidebar when a user with role ``admin`` is logged in. Notice that ``Project Tools`` is available in the sidebar

.. image:: /_static/images/site-admin/ui-config-permitted-roles-admin.webp
   :alt: Configurations - User Interface Configuration Permitted Roles Admin
   :width: 20 %
   :align: center

|

Here's the sidebar when a user with role ``author`` is logged in. Notice that ``Project Tools`` is not available in the sidebar

.. image:: /_static/images/site-admin/ui-config-permitted-roles-author.webp
   :alt: Configurations - User Interface Configuration Permitted Roles Admin
   :width: 20 %
   :align: center

|

|hr|

.. _sidebar-excludes:

~~~~~~~~~~~~~~~~
Sidebar Excludes
~~~~~~~~~~~~~~~~
To hide items (exclude) in the Sidebar such as path navigators and path navigator trees, use

.. code-block:: xml
   :force:

      ...
      <excludes>
        <exclude PATTERN_TO_EXCLUDE/>
        ...
      </excludes>

|

where:

* PATTERN_TO_EXCLUDE is a prefix of items to hide from the Sidebar

Let's take a look at an example using a project created from the Website Editorial blueprint, to hide the folder ``/site/website/articles/2021/3``.

Here's the Sidebar before the ``2021/3`` folder is hidden

.. image:: /_static/images/site-admin/ui-folders.webp
   :alt: Configurations - User Interface Configuration Folder Structure
   :width: 30 %
   :align: center

|

Here's the configuration to hide the folder:

.. code-block:: xml
   :linenos:
   :emphasize-lines: 8-10

   <widget id="craftercms.components.PathNavigator">
     <configuration>
       <id>Pages</id>
       <label>Pages</label>
       <icon id="@mui/icons-material/DescriptionOutlined"/>
       <rootPath>/site/website</rootPath>
       <locale>en</locale>
       <excludes>
         <exclude>/site/website/articles/2021/3</exclude>
       </excludes>
     </configuration>
   </widget>

|

Here's the Sidebar with the folder ``2021/3`` hidden:

.. image:: /_static/images/site-admin/ui-folder-hidden.webp
   :alt: Configurations - User Interface Configuration Folder Hidden
   :width: 30 %
   :align: center

|

|hr|

.. _sidebar-widget-icon-colors:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Navigator Widgets Styling Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Several styling options are available for the navigator widgets. Both the widget's container element
and the icon element can receive CSS classes and targeted base styles, collapsed styles and expanded styles.
Custom css style sheets may be loaded into Studio via :ref:`plugins`.

Colors, background colors, borders (css properties) etc can be added to widgets in the Sidebar when expanding/collapsing a widget via the ``icon`` and ``container`` properties.

Let's take a look at an example of putting a red border when ``Taxonomy`` is expanded and for ``Templates``, a red font color when expanded and a blue font color when collapsed

Here's the default colors of widgets in the Sidebar

.. image:: /_static/images/site-admin/ui-widget-default-colors.webp
   :alt: Configurations - User Interface Configuration Widget Default Colors
   :width: 25 %
   :align: center

|

Here's the configuration for putting a red border when ``Taxonomy`` is expanded and for ``Templates``, a red font color when expanded and a blue font color when collapsed

.. code-block:: xml
   :linenos:
   :emphasize-lines: 6-11, 19-26

   <widget id="craftercms.components.PathNavigator">
     <configuration>
       <id>Taxonomy</id>
       <label>Taxonomy</label>
       <icon id="@mui/icons-material/LocalOfferOutlined"/>
       <container>
         <expandedStyle>
           <border>solid</border>
           <borderColor>red</borderColor>
         </expandedStyle>
       </container>
       <rootPath>/site/taxonomy</rootPath>
       <locale>en</locale>
     </configuration>
   </widget>
   <widget id="craftercms.components.PathNavigatorTree">
     <configuration>
       <label>Templates</label>
       <icon id="@mui/icons-material/InsertDriveFileOutlined">
         <expandedStyle>
           <color>red</color>
         </expandedStyle>
         <collapsedStyle>
           <color>blue</color>
         </collapsedStyle>
       </icon>
       <rootPath>/templates</rootPath>
       <locale>en</locale>
     </configuration>
   </widget>
   ...

|

Remember that children of ``expandedStyle`` & ``collapsedStyle`` should be camelCased standard css properties.

Here's the Sidebar with the colors and border added:

.. image:: /_static/images/site-admin/ui-widget-color-added.webp
   :alt: Configurations - User Interface Configuration Widget Border and Colors Added
   :width: 25 %

.. image:: /_static/images/content-author/preview-page-components-space.webp
   :width: 5 %

.. image:: /_static/images/site-admin/ui-widget-template-color.webp
   :alt: Configurations - User Interface Configuration Widget Default Colors
   :width: 25 %

|

'''''''''''''''''
Container Options
'''''''''''''''''
- ``baseClass``: a class name (string) applied to the container regardless of state
- ``expandedClass``: a class name (string) applied to the container when expanded
- ``collapsedClass``: a class name (string) applied to the container when collapsed
- ``baseStyle``: a set of CSS rules applied to the container regardless of state (e.g. ``<baseStyles><backgroundColor>red</backgroundColor></baseStyles>``)
- ``expandedStyle``: a set of CSS rules applied to the container when expanded
- ``collapsedStyle``: a set of CSS rules applied to the container when collapsed

''''''''''''
Icon Options
''''''''''''

- ``class``: a class name (string) applied to the icon regardless of state
- ``style``: a set of CSS rules applied to the icon regardless of state  (e.g. ``<baseStyles><backgroundColor>red</backgroundColor></baseStyles>``)
- ``content``: inner content of the icon to use in case you're using a font icon library that uses content to render the icon
- ``expandedStyle``: a set of CSS rules applied to the icon when expanded
- ``collapsedStyle``: a set of CSS rules applied to the icon when collapsed

|hr|

.. _spa-sources-sidebar-cabinet:

~~~~~~~~~~~~~~~~~~~~~~~~~~~
SPA Sources Sidebar Cabinet
~~~~~~~~~~~~~~~~~~~~~~~~~~~
Users may want to manage (edit/view) SPA sources in their projects through Studio.
To view/edit SPA sources, simply add another ``PathNavigatorTree`` or ``PathNavigator`` widget, named ``sources``,
in the Sidebar widget like below:

.. code-block:: xml
   :caption: **SPA sources - ui.xml**
   :linenos:
   :emphasize-lines: 6-14

   <siteUI>
     <widget id="craftercms.components.ToolsPanel">
       <configuration>
         <widgets>
           ...
           <widget id="craftercms.components.PathNavigatorTree">
             <configuration>
               <id>Sources</id>
               <label>Sources</label>
               <icon id="@mui/icons-material/InsertDriveFileOutlined"/>
               <rootPath>/sources/</rootPath>
               <locale>en</locale>
             </configuration>
           </widget>
           ...


.. Note:: Adding SPA sources in top folders other than ``/sources`` is not supported.  Also note that
   items under ``/sources`` are excluded from search.

The ``Video Center`` blueprint from the marketplace contains an example of SPA sources managed in Studio
(``/sources`` added to the sidebar).

.. image:: /_static/images/site-admin/sources-folder-sidebar-ex.webp
   :alt: Configurations - User Interface Configuration Sources Widget in Sidebar
   :width: 90 %
   :align: center

|hr|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
PathNavigatorTree and PathNavigator Sidebar Widget
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The ``PathNavigatorTree`` sidebar widget allows the display of trees with the ability to expand/collapse containers. It shows elements (children) in a level and allows the container children to be further expanded without navigating to the child, allowing many children to be open at the same time. Also, each container child allows filtering/searching via keywords allowing users to find items faster.

.. figure:: /_static/images/site-admin/ui-pathnavigatortree-widget.webp
   :alt: Configurations - PathNavigatorTree Widget in Sidebar

   *PathNavigatorTree widget*

The ``PathNavigator`` sidebar widget shows elements (children) of a level and allows filtering/searching via keywords allowing users to find items faster.  It doesn't provide an overview like the ``PathNavigatorTree``, but, the ``PathNavigator`` is the recommended widget if your project contains thousands of pages where a tree becomes unresponsive due to painting a massive number of items.

.. figure:: /_static/images/site-admin/ui-pathnavigator-widget.webp
   :alt: Configurations - PathNavigator Widget in Sidebar

   *PathNavigator widget*

Here are some options on displaying elements (children) of navigator widgets:

'''''
Limit
'''''
The number of children displayed at a time when expanding a container can be limited via the ``limit`` property like below:

.. code-block:: xml
    :caption: *PathNavigatorTree sidebar widget limit configuration*
    :emphasize-lines: 8

    <widget id="craftercms.components.PathNavigatorTree">
      <configuration>
        <id>StaticAssets</id>
        <label>Static Assets</label>
        <icon id="@mui/icons-material/ImageOutlined"/>
        <rootPath>/static-assets</rootPath>
        <locale>en</locale>
        <limit>5</limit>
      </configuration>
    </widget>

In the example above, the ``Static Assets`` path navigator tree limits the children displayed to 5 items when opening
a container like in the image  on the left below:

.. image:: /_static/images/site-admin/ui-widget-pathnavtree-limit.webp
   :alt: Configurations - User Interface Configuration PathNavigatorTree Widget Limit
   :width: 25 %

.. image:: /_static/images/site-admin/ui-widget-image-spacer.webp
   :width: 5 %

.. image:: /_static/images/site-admin/ui-widget-pathnavtree-limit-more.webp
   :alt: Configurations - User Interface Configuration PathNavigatorTree Widget Limit Expanded
   :width: 25 %


Notice also that when the user clicks on ``10 more items`` displayed on the image on the left, an additional 5 more
items will be displayed as shown on the image on the right.

Remember to do a refresh of your browser after making the limit changes and saving your configuration in order to see
the changes you've made in action.

'''''''
Sorting
'''''''
The order of children displayed may be sorted via the ``sortStrategy`` and ``order`` property like below:

.. code-block:: xml
    :caption: *Sidebar Widget Sorting Configuration Example*
    :emphasize-lines: 8-9

    <widget id="craftercms.components.PathNavigatorTree">
      <configuration>
        <id>Pages</id>
        <label>Pages</label>
        <icon id="@mui/icons-material/DescriptionOutlined"/>
        <rootPath>/site/website/index.xml</rootPath>
        <locale>en</locale>
        <sortStrategy>lastUpdate</sortStrategy>
        <order>DESC</order>
      </configuration>
    </widget>

The following ``sortStrategy`` property options are available:

- ``alphabetical``: sort in alphabetical order
- ``foldersFirst``: sort in alphabetical order, listing folders first
- ``lastUpdate``: sort using the last modified date

The following ``order`` property options are available:

- ``ASC``: display children in ascending order
- ``DESC``: display children in descending order

In the example configuration above, the children of the ``Pages`` folder will be sorted using the last modified
date in descending order. Let's take a look on how the example configuration above affects the order of children
displayed in the ``Pages`` folder.  The image on the left is a baseline screenshot of the ``/articles/2021/3`` folder
under ``Pages`` for our example. The image on the right is a screenshot of the the same folder after editing and saving
changes to the ``Top Books For Young Women`` article, where we expect our edited article to be the first one listed:

.. image:: /_static/images/site-admin/ui-widget-children-sorting-before.webp
   :alt: Configurations - User Interface Configuration widget sorting screenshot of /articles/2021/3 folder children
   :width: 25 %

.. image:: /_static/images/site-admin/ui-widget-children-sorting-spacer.webp
   :width: 5 %

.. image:: /_static/images/site-admin/ui-widget-children-sorting-after-updates-to-a-child.webp
   :alt: Configurations - User Interface Configuration widget sorting screenshot of /articles/2021/3 folder after modifying a child
   :width: 25 %

|hr|

.. _targeting-configuration:

~~~~~~~~~~~~~~~~~~
Audience Targeting
~~~~~~~~~~~~~~~~~~
Audience Targeting allows an author to see what the project would look like if it were being browsed
by a user with a given set of attributes.

.. image:: /_static/images/page/page-targeting-open.webp
    :width: 80 %
    :align: center

|

Here's the  ``Audience Targeting`` configuration out of the box for a project created using the Website Editorial blueprint:

.. code-block:: xml
   :caption: **Audience Targeting - ui.xml**
   :linenos:

   <widget id="craftercms.components.ICEToolsPanel">
     <configuration>
       <widgets>
         <widget id="craftercms.components.ToolsPanelPageButton">
         ...
         <widget id="craftercms.components.ToolsPanelPageButton">
           <configuration>
             <target id="icePanel"/>
             <title id="previewAudiencesPanel.title" defaultMessage="Audience Targeting"/>
             <icon id="@mui/icons-material/EmojiPeopleRounded"/>
             <widgets>
               <widget id="craftercms.components.PreviewAudiencesPanel">
                 <configuration>
                   <fields>
                     <segment>
                       <id>segment</id>
                       <name>Segment</name>
                       <description>User segment.</description>
                       <type>dropdown</type>
                       <defaultValue>anonymous</defaultValue>
                       <values>
                         <value>
                           <label>Guy</label>
                           <value>guy</value>
                         </value>
                         <value>
                           <label>Gal</label>
                           <value>gal</value>
                         </value>
                         <value>
                           <label>Anonymous</label>
                           <value>anonymous</value>
                         </value>
                       </values>
                       <helpText>Setting the segment will change content targeting to the audience selected.</helpText>
                     </segment>
                     <name>
                       <id>name</id>
                       <name>Name</name>
                       <description>User's first and last name.</description>
                       <type>input</type>
                       <helpText>Enter user's first and last name.</helpText>
                     </name>
                   </fields>
                 </configuration>
               </widget>
               ...

|

Here's how the above configuration looks like in the Experience Builder Panel in Studio:

.. image:: /_static/images/page/page-targeting-curr-attributes.webp
    :width: 30 %
    :align: center

|

See :ref:`targeting` for more information on configuring the targeting system of Crafter Studio to help provide Crafter Engine with fake user properties that help drive the targeting system, such as configuring targeting based on roles, etc. and :ref:`audience-targeting` for more information on how content authors use the audience targeting system configured.

|hr|

~~~
RTE
~~~
Rich Text Editors (RTEs) allow the users to edit, arrange and style content however they like, without needing to know HTML.
There are two ways of editing content in Studio: (1) form-based editing and (2) In-context editing (ICE). Form-based editing is done by clicking on ``Options`` (three dots next to the preview address bar at the top of the page, or the three dots next to the page in the Sidebar), then selecting ``Edit``. In-context editing is done by enabling the ``Edit mode`` by clicking on the pencil at the top right of the page (which turns green when enabled), then clicking on the section of the page you want to edit.

To configure the RTE, add/edit the widget ``craftercms.components.TinyMCE``:

.. raw:: html

   <details>
   <summary><a>Example RTE configuration</a></summary>

.. code-block:: xml
   :linenos:

   <widget id="craftercms.components.TinyMCE">
     <configuration>
       <setups>
         <setup id="generic">
           <!-- Configuration options: https://www.tiny.cloud/docs/configure/ -->
           <!-- Plugins: https://www.tiny.cloud/docs/plugins/opensource/ -->
           <tinymceOptions>{
             "menubar": true,
             "theme": "silver",
             "plugins": "print preview searchreplace autolink directionality visualblocks visualchars fullscreen image link media template codesample table charmap hr pagebreak nonbreaking anchor toc insertdatetime advlist lists wordcount textpattern help acecode paste editform",
             "extended_valid_elements": "",
             "valid_children": "",
             "toolbar1": "formatselect | bold italic strikethrough forecolor backcolor | link | alignleft aligncenter alignright alignjustify | numlist bullist outdent indent | removeformat | editform",
             "code_editor_wrap": false,
             "toolbar_sticky": true,
             "image_advtab": true,
             "encoding": "xml",
             "relative_urls": false,
             "remove_script_host": false,
             "convert_urls": false,
             "remove_trailing_brs": false,
             "media_live_embeds": true,
             "autoresize_on_init": false,
             "autoresize_bottom_margin": 0,
             "menu": { "tools": { "title": "Tools", "items": "tinymcespellchecker code acecode wordcount" } },
             "automatic_uploads": true,
             "file_picker_types": "image media file",
             "paste_data_images": true,
             "templates": [],
             "content_css": [],
             "content_style": "body {}",
             "contextmenu": false }
           </tinymceOptions>
         </setup>
       </setups>
     </configuration>
   </widget>

.. raw:: html

   </details>

|
|

Our RTE is based on TinyMCE (https://www.tiny.cloud/) and can leverage all configurations and plugins designed for the TinyMCE editor.

To learn more about configuring the RTE, see :ref:`here <rte-configuration>`

|hr|

.. _project-tools-ui-configuration:

~~~~~~~~~~~~~~~~~~~~~~~~~~~
Project Tools Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~
The Project Config tools configuration section defines what modules are available for administration use when
clicking on |projectTools| from the Sidebar.

.. code-block:: xml
   :linenos:

   <references>
   		<reference id="craftercms.siteTools">
   			<tools>
   				<tool>
   					<title id="dropTargetsMessages.contentTypes" defaultMessage="Content Types"/>
   					<icon id="@mui/icons-material/WidgetsOutlined"/>
   					<url>content-types</url>
   					<widget id="craftercms.components.ContentTypeManagement"/>
   				</tool>
   				<tool>
   					<title id="GlobalMenu.EncryptionToolEntryLabel"
   					       defaultMessage="Encryption Tool"/>
   					<icon id="@mui/icons-material/LockOutlined"/>
   					<url>encrypt-tool</url>
   					<widget id="craftercms.components.SiteEncryptTool"/>
   				</tool>
   				<tool>
   					<title id="words.configuration" defaultMessage="Configuration"/>
   					<icon id="@mui/icons-material/SettingsApplicationsOutlined"/>
   					<url>configuration</url>
   					<widget id="craftercms.components.SiteConfigurationManagement"/>
   				</tool>
   				...

|

'''''''''''''''''''''''
List of available tools
'''''''''''''''''''''''
Here's a list of available tools defined in the Website_Editorial blueprint.

==================== =====================================================================
Tool                 Description
==================== =====================================================================
Content Types        Allows you to create/modify content types
Encryption Tool      Allows the user to encrypt sensitive data such as access keys and passwords
Configuration        Contains all the configuration files managed through Crafter Studio
Audit                Allows you to view your project activity log
Workflow States      Contains a list of all files in the project with its corresponding state
Log Console          Allows you to tail logs depending on what logging levels are set
Publishing           Allows the user to view the publishing status, perform a bulk publish or to publish content using commit ID(s)
Git                  Allows the user to perform Git operations
GraphQL              Allows the user run GraphQL queries and explore the schema documentation for a project without the need of any other tool
Plugin Management    Allows the user to install and to view currently installed, project plugins
==================== =====================================================================

See :ref:`navigating-project-tools` for more information on the available tools in ``Project Tools``.

|hr|

.. _preview-edit-mode-defaults:

~~~~~~~~~~~~~~~~~~~~~~~~~~
Preview Edit Mode Defaults
~~~~~~~~~~~~~~~~~~~~~~~~~~
.. version_tag::
    :label: Since
    :version: 4.1.2

To configure defaults for Preview edit mode, set the following attributes of the
``craftercms.components.Preview`` widget:

.. code-block:: xml
    :linenos:

    <widget
            id="craftercms.components.Preview"
            initialEditModeOn="true"
            initialHighlightMode="all"
            xbDetectionTimeoutMs="5000"
    />

|hr|

.. _rte-configuration:

"""""""""""""""""
RTE Configuration
"""""""""""""""""
Rich Text Editors (RTEs) are more effective/productive for authors when they are configured properly for the specific type of content the
author is managing. A properly and effectively configured RTE has the right styles, menu options and so on.
Every RTE in the system can have a different look and feel, different editing/menu options, available styles, components
and other configurations. You can also SHARE setups between similar RTEs in your project.

This section describes how to configure various configuration options and plugins for the RTE in the :ref:`User Interface Configuration <user-interface-configuration>` file.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Common Configurations for Effective RTEs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Here are some things to consider for setting up effective RTEs:

#. The rich text editor's width should be set to the same width as the region it is intended to edit
#. Project style sheet of your project is imported so it can be applied to the RTE
#. Project styles are being applied appropriately to the markup in the RTE. Note that sometimes styles in CSS are so aggressively specified that the RTE cannot pick them up.
#. Formats and styles are configured to match the part of the project being edited
#. Toolbar is configured with only what is required for the specific use case (reducing options makes it easier for editors)
#. If plugins like ``insert layout`` and so on are enabled it should be fully configured.

|hr|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
What Out-of-the-Box Functionality Does Crafter Studio's RTE Support?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Our RTE is based on TinyMCE 7 (https://www.tiny.cloud/) and can leverage all configurations and plugins designed for the TinyMCE editor.  You can find the documentation for these TinyMCE configurations and settings here: https://www.tiny.cloud/docs/


'''''''''''''''
TinyMCE plugins
'''''''''''''''
Crafter Studio uses standard TinyMCE plugins. To see the list of TinyMCE plugins available in Studio,
look for  ``plugins`` in the configuration:

.. code-block:: xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/ui.xml*
   :force:
   :emphasize-lines: 12

   <widget id="craftercms.components.TinyMCE">
     <configuration>
       <setups>
         <setup id="generic">
           <!-- Configuration options: https://www.tiny.cloud/docs/tinymce/latest/initial-configuration/ -->
           <!-- Plugins: https://www.tiny.cloud/docs/plugins/#open-source-plugins -->
           <tinymceOptions>
             <![CDATA[
               {
                 "menubar": true,
                 "theme": "silver",
                 "plugins": "preview searchreplace autolink directionality visualblocks visualchars fullscreen image link media template codesample table charmap pagebreak nonbreaking anchor insertdatetime advlist lists wordcount help acecode",
   ...

|

See https://www.tiny.cloud/docs/plugins/#open-source-plugins for more information on the TinyMCE plugins.


.. |rteMediaBtn| image:: /_static/images/site-admin/rte/rte-media-button.webp
                   :width: 4%

To add TinyMCE buttons to the toolbar, add the names listed in the **toolbar** tag in the TinyMCE plugin documentation to ``toolbar(n)`` in the configuration.

Crafter Studio by default adds plugins to ``toolbar1`` as seen in the example below.

.. code-block:: xml

   ...
   "toolbar1": "formatselect | bold italic strikethrough forecolor backcolor | link | alignleft aligncenter alignright alignjustify | numlist bullist outdent indent | removeformat | editform",
   ...

|

See https://www.tiny.cloud/docs/tinymce/latest/toolbar-configuration-options/#toolbarn for more information on the toolbar(n) option of Tiny MCE


TinyMCE Plugin Toolbar Example
''''''''''''''''''''''''''''''
Let's take a look at an example of using one of the TinyMCE plugins to add a button in the toolbar.

We'll add a media button to our editor instance to be able to embed a YouTube video:

1. Open the RTE configuration file in Studio by opening the **Sidebar**, then click on |projectTools| -> *Configuration* -> *User Interface Configuration*
2. Add ``toolbar2`` and the button name **media** like below:

   .. code-block:: xml
      :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/ui.xml*
      :emphasize-lines: 2

      "toolbar1": "formatselect | bold italic strikethrough forecolor backcolor | link | alignleft aligncenter alignright alignjustify | numlist bullist outdent indent | removeformat",
      "toolbar2": "media",

   An **Insert/Edit Embedded Media** button |rteMediaBtn| will now be available for users of the RTE.

   .. figure:: /_static/images/site-admin/rte/rte-media-button-added.webp
      :alt: RTE Setup - Media button added to editor instance
      :width: 75%
      :align: center

   |

2. Click on the |rteMediaBtn| button to add the link to the YouTube video you'd like to embed in the RTE and to setup other parameters. In the **General** tab, fill in the **Source** field with the URL of the YouTube video you'd like to embed and finally, fill in the **Dimensions** field to the size desired. Click on the **Ok** button.

   .. figure:: /_static/images/site-admin/rte/rte-media-config.webp
      :alt: RTE Setup - Insert/Edit Embedded Media Example
      :width: 35%
      :align: center

   |

3. Save your changes, and your video should now be embedded in your page

   .. figure:: /_static/images/site-admin/rte/rte-media-preview.webp
      :alt: RTE Setup - YouTube video embedded in page, inserted through the RTE
      :width: 65%
      :align: center

   |

.. _rte-configuration-tinymce-plugin-template-example:

TinyMCE Plugin Template Example
'''''''''''''''''''''''''''''''
Let's take a look at another example of using the TinyMCE plugin, ``template``.

The ``template`` plugin adds support for custom templates. The default editor instance only adds the menu item ``Insert template...`` under the ``Insert`` menu in the menubar. On TinyMCE, it adds a menu item ``Insert template`` under the ``Insert`` menu and a toolbar button.

Note that the open-source ``template`` plugin and associated config options have been removed in TinyMCE 7. To achieve
an equivalent TinyMCE config before the version 7 upgrade, the ``insert`` menu needs to be added/customised to the config as shown below:

.. code-block:: xml
    :emphasize-lines: 3

    "menu": {
      "tools": { "title": "Tools", "items": "tinymcespellchecker code acecode wordcount" },
      "insert": { "title": "Insert", "items": "image link media template codesample inserttable | charmap hr | pagebreak nonbreaking anchor | insertdatetime" }
    },

To add a template to the RTE, simply add ``templates`` under ``setup`` in the RTE configuration.
Under ``templates``, add ``title``, ``description`` and ``content``

.. code-block:: xml
   :linenos:
   :force:
   :emphasize-lines: 12,14-20

   <widget id="craftercms.components.TinyMCE">
   <configuration>
     <setups>
       <setup id="...">
          <tinymceOptions>
            <![CDATA[
              {
                "menubar": true,
                ...
                "menu": {
                  "tools": { "title": "Tools", "items": "tinymcespellchecker code acecode wordcount" },
                  "insert": { "title": "Insert", "items": "image link media template codesample inserttable | charmap hr | pagebreak nonbreaking anchor | insertdatetime" }
                },
                "templates" : [
                  {
                    "title": "Your Template Title",
                    "content": "Your template content",
                    "description": "Your Template Description "
                   },
                ]
             }
        ]]>
        ...

|

Let us take a look at an example of adding two templates to the RTE configuration

1. Open the RTE configuration file in your project by opening the **Sidebar**, then click on |projectTools| -> *Configuration* -> *User Interface Configuration*

2. Scroll down to the TinyMCE section and add in the following templates under ``<setup />``:

   .. code-block:: xml
      :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/ui.xml*
      :linenos:

      "templates" : [
        {
          "title": "Test template 1",
          "content": "Test 1",
          "description": "Test1 Description "
        },
        {
          "title": "Test template 2",
          "content": "<div class='test'><h1>This is a title</h1><p>Look at this paragraph!</p></div>",
          "description": "Test 2 description"
        }
      ]

   |

3. Save your changes. The configured templates should now be available under ``Insert templates`` of the ``Insert`` menu.

   .. figure:: /_static/images/site-admin/rte/rte-template-plugin-example.webp
      :alt: RTE Setup - RTE template plugin example in action
      :width: 65%
      :align: center

   |

See https://www.tiny.cloud/docs/tinymce/6/template/ for more information on the template plugin.

.. _rte-paste-plugin-hooks:

TinyMCE paste plugin callback hooks
'''''''''''''''''''''''''''''''''''
The TinyMCE ``paste`` plugin enables you to modify the pasted content before it gets inserted into the editor (``paste_preprocess``) and before it gets inserted into the editor but after it’s been parsed into a DOM structure (``paste_postprocess``). For more information on these options, see https://www.tiny.cloud/docs/tinymce/latest/copy-and-paste/#paste_preprocess.

In order to hook into the callback (``paste_preprocess`` and ``paste_postprocess``), do the following in the RTE configuration:

1) Add the default ``paste`` plugin in ``plugins`` if not already included

   .. code-block:: xml
      :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/ui.xml*

      "plugins": "print preview searchreplace autolink directionality visualblocks visualchars fullscreen image link media template codesample table charmap hr pagebreak nonbreaking anchor toc insertdatetime advlist lists wordcount textpattern help acecode paste"

   |

2) Create an :ref:`external plugin <adding-external-plugins>` by following the structure of the example plugin `here <https://github.com/craftercms/studio-ui/blob/develop/static-assets/js/tinymce-plugins/craftercms_paste_extension/craftercms_tinymce_hooks.sample.js>`__. To modify the pasted content, add your code under ``paste_preprocess()`` or ``paste_postprocess()`` depending on your needs.

3) Add the plugin created in the previous step as an external plugin under the ``craftercms_tinymce_hooks`` tag.

   .. code-block:: xml
      :force:
      :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/ui.xml*

      "external_plugins": {
        "craftercms_tinymce_hooks": "/studio/1/plugin/file?siteId={site}&pluginId=craftercms&type=tinymce&name=craftercms_paste_extension&filename=samplepasteplugin.js"
      }

   |

   For more information on ``craftercms_tinymce_hooks``, see :ref:`here <extending-tinymce>`

.. note::
   When Tiny's ``paste`` plugin is included, ``craftercms_paste_cleanup`` extension is also enabled. CrafterCMS' extension performs some additional paste cleanup from what Tiny's plugin does. To disable these additional processing of the paste input, you may add ``<craftercms_paste_cleanup>false</craftercms_paste_cleanup>`` to the RTE configuration


.. _rte-add-allowable-elements:

'''''''''''''''''''''''''
Adding Allowable Elements
'''''''''''''''''''''''''
Tiny MCE allows only a certain set of elements (HTML tags) as valid (rule set) by default in the code editor and will strip elements not in the allowable list  when it outputs its HTML. For example, if you try adding in the ``<script />`` element , or the ``<iframe />`` element, it will be stripped out of the HTML output. To add specific elements that should also be valid, in addition to the existing rule set, we use the ``extended_valid_elements`` in the RTE configuration. Simply add the elements you would like added to the existing rule set in the ``<extended_valid_elements />`` tag in RTE Configuration file.

.. code-block:: xml

   "extended_valid_elements": "script mycustomtag",   <!-- elements whitelist (won't be stripped out) -->

|

Example allowing script element
'''''''''''''''''''''''''''''''

Let's take a look at an example of adding ``<script />`` to the allowable elements (rule set). We'll be using a project created using the Website Editorial blueprint.

1. Open the RTE configuration file in Studio by opening the **Sidebar**, then click on |projectTools| -> *Configuration* -> *User Interface Configuration* then scroll down to the ``craftercms.components.TinyMCE`` widget section

2. Scroll down to ``extended_valid_elements`` and add ``script`` and save.

   .. code-block:: xml

      "extended_valid_elements": "script"   <!-- elements whitelist (won't be stripped out) -->

   |

3. We'll now add ``<script />`` in the RTE to verify it works.

   Open the **Sidebar** and edit one of the articles. Navigate to ``/articles/2020/7/`` then right click on ``New ACME Phone Released Today`` and select ``Edit``.

   Scroll down to the ``Content`` part of the form and Under ``Sections``, click on ``Add Another``

4. Click on the newly added section, then click on ``Tools`` -> ``Code Editor`` from the RTE menubar.

   .. figure:: /_static/images/site-admin/rte/rte-open-code-editor.webp
      :alt: RTE Setup - Open RTE code editor
      :width: 85%
      :align: center

   |

5. Add a script in the code editor then save the changes. This will display a dialog saying ``Hello`` when you preview the article ``New ACME Phone Released Today``

   .. code-block:: html

      <script>alert('Hello!')</script>

   |

6. Preview the page. A dialog saying ``Hello`` should pop up before the page is displayed

   .. figure:: /_static/images/site-admin/rte/rte-script-run.webp
      :alt: RTE Setup - Preview page with <script /> added in RTE
      :width: 45%
      :align: center

   |

   Please note that TinyMCE gives this warning when allowing script elements (<script />):

   .. Warning:: Allowing script elements (<script>) in TinyMCE exposes users to cross-site scripting (XSS) attacks.


Example allowing a custom element
'''''''''''''''''''''''''''''''''
You can also add custom elements to the rule set and can be done by simply adding the custom tag to ``extended_valid_elements``. Let's take a look at an example of adding the tag  ``mycustomtag`` to the rule set.

.. note:: Case sensitive custom elements are not supported in TinyMCE 5. Remember to **use only lowercase for custom elements** (e.g. ``myattr`` is supported but *myAttr* is not supported).

1. Open the RTE configuration file in Studio by opening the **Sidebar**, then click on |projectTools| -> *Configuration* -> *User Interface Configuration* then scroll down to the ``craftercms.components.TinyMCE`` widget section

2. Scroll down to ``extended_valid_elements``  and add ``mycustomtag`` and save.

   .. code-block:: xml

      "extended_valid_elements": [ "script", "mycustomtag"]

   |

3. We'll now add the ``<mycustomtag />`` in the RTE to verify it works.

   Open the **Sidebar** and edit one of the articles. Navigate to ``/articles/2020/7/`` then right click on ``New ACME Phone Released Today`` and select ``Edit``.

   Scroll down to the ``Content`` part of the form and Under ``Sections``, click on one of the section, then click on ``Tools`` -> ``Code Editor`` from the RTE menubar, then use  ``<mycustomtag />``

      .. code-block:: xml

         <mycustomtag>my custom tag</mycustomtag>

      |

   .. figure:: /_static/images/site-admin/rte/rte-custom-tag-added.webp
      :alt: RTE Setup - Open RTE code editor
      :width: 85%
      :align: center

   |

.. _adding-external-plugins:

'''''''''''''''''''''''
Adding External Plugins
'''''''''''''''''''''''
TinyMCE provides an option to specify URLS to plugins outside the tinymce plugins directory. These external plugins allow the user to extend TinyMCE. For example, you can create custom dialogs, buttons, menu items, etc.

For more information on the Tiny MCE external_plugins option, see https://www.tiny.cloud/docs/tinymce/latest/editor-important-options/#external_plugins

The Crafter Studio developer does not have full control of the tinymce initialization. To add a custom button to the toolbar in Crafter Studio, it would be done using the external plugin route since, what TinyMCE docs advise – i.e. using the ``setup`` function to add the button – is not viable in Studio without creating a :ref:`form control plugin <building-plugins-controls>` where they'd have full control of tinymce initialization.

To add an external plugin, use ``external_plugins`` in the RTE configuration.
Use the Crafter Studio API that gets a file for a given plugin, the getPluginFile API found here :base_url:`getPluginFile <_static/api/studio.html#tag/plugin/operation/getPluginFile>` to get the Tiny MCE external plugin file to pass to the RTE.

Example External Plugin
'''''''''''''''''''''''
Let's take a look at an example of a simple external plugin that creates a custom button which inserts text in the RTE.
We'll load our external plugin (a custom button) and add it to the RTE's toolbar. For our example, we'll be using a site created using the empty blueprint named ``hello``.

1. Open the RTE configuration file in Studio by opening the **Sidebar**, then click on |projectTools| -> *Configuration* -> *User Interface Configuration* then scroll down to the ``craftercms.components.TinyMCE`` widget section

2. We'll add the configuration for TinyMCE to load the plugin using Crafter Studio's getPluginFile API. We achieve this by using  ``external_plugins`` and adding child tags with the id of the plugin as tag name and the target URL as the tag's content |br|

   .. code-block:: xml
      :force:

      "external_plugins": {
        "my_button": "/studio/1/plugin/file?siteId={site}&pluginId=my_button&type=tinymce&name=my_button&filename=plugin.js"
      }

   |

   where:

      {site}: a macro that inserts the current siteId


3. Add the custom button we're creating to the toolbar of the RTE. Scroll to the ``toolbar(n)`` tag and add the custom button we are creating ``my_button`` to ``toolbar2``

   .. code-block:: xml

      "toolbar2": "my_button"

   |

4. Finally, we'll create our plugin file and add it in to Studio. See :ref:`plugins` for more information on creating a Crafter Studio plugin.

   * Using information from step 2 for our external plugin, create the required directory structure for the plugin file, then create our plugin file named ``plugin.js``

     .. code-block:: js
        :linenos:
        :caption: *$CRAFTER_HOME/data/repos/sites/SITE_NAME/sandbox/config/studio/plugins/tinymce/my_button/plugin.js*

        (function () {

          'use strict';

          tinymce.PluginManager.add("my_button", function (editor, url) {

            function _onAction()
            {
              // Write something in the RTE when the plugin is triggered
              editor.insertContent("<p>Content added from my button.</p>")
            }

            // Define the Toolbar button
            editor.ui.registry.addButton('my_button', {
                text: "My Button",
                onAction: _onAction
            });
          });

          // Return details to be displayed in TinyMCE's "Help" plugin, if you use it
          // This is optional.
          return {
            getMetadata: function () {
              return {
                name: "My Button example",
                url: "http://exampleplugindocsurl.com"
              };
            }
          };
        })();

     |

     We recommend minimizing the ``plugin.js`` file. If your plugin is minimized, remember to change the external_plugins > my_button URL in the RTE configuration to load the minified version.

   * Remember to commit the new file so Studio will pick it up by doing a ``git add`` then a ``git commit``. Whenever you edit directly in the filesystem, you need to commit your changes to ensure they are properly reflected.

5. Let's see the TinyMCE external plugin we created in action.

   Edit the ``Home`` page by opening the ``Sidebar`` then under ``Pages``, right-click on ``Home``, then select edit. |br|
   Scroll down to the ``Main Content`` section of the form to view the RTE. Notice that the button we created is in the toolbar.

   .. figure:: /_static/images/site-admin/rte/rte-custom-button-added.webp
      :alt: RTE showing custom button
      :width: 85%
      :align: center

   |

   Click on our custom button in the RTE ``My Button``, and the line *Content added from my button.* will be inserted into the RTE

   .. figure:: /_static/images/site-admin/rte/rte-custom-button-clicked.webp
      :alt: RTE custom button clicked - text inserted in RTE
      :width: 85%
      :align: center

   |

'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''
Adding support for valid child elements within a parent element
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''
TinyMCE provides an option to control what child elements can exist within specified parent elements.
By adding/removing child elements that can exist within a parent element, you can force which elements are valid children of the parent element.

To add/remove child elements to the list of valid child elements, add/remove the element in the **valid_children** tag in the RTE Configuration file. To add a child element to a parent element, use a ``+`` before the parent element then enclose in square brackets the child element/s you want to add e.g. ``+a[div|p]``. To remove a child element, use a ``-`` before the parent element then enclose in square brackets the child element/s you want to remove,  e.g. ``-a[img]``. You can add multiple parent elements by using a comma separated list of parents with elements that should be added/removed as valid children

   .. code-block:: xml
      :caption: *Example adding/removing elements for the specified parent*

      "valid_children" : "+body[style],-body[div],p[strong|a|#text]"

   |

The example above shows you how to add **style** as a valid child of **body** and remove **div** as a valid child. It also forces only *strong* and **a** and *text contents* to be valid children of **p**.


For more information on the TinyMCE ``valid_children`` option, see https://www.tiny.cloud/docs/tinymce/latest/content-filtering/#valid_children

Example adding valid child elements to parent element
'''''''''''''''''''''''''''''''''''''''''''''''''''''
Let's take a look at an example of how to add **div** and *text content* as valid children of **a** (html anchor) using the website editorial blueprint.

1. Open the RTE configuration file in Studio by opening the **Sidebar**, then click on |projectTools| -> *Configuration* -> *User Interface Configuration* then scroll down to the ``craftercms.components.TinyMCE`` widget section

2. Add ``valid_children`` and add **div** and text contents as child elements of **a** and save.

   .. code-block:: xml
      :caption: *RTE Configuration File*

      "valid_children": "+a[div|#text]"

   |

3. We'll now disable ``Force Root Block p Tag`` and ``Force p tags New Lines`` so that markup we enter in the RTE code editor will remain unchanged after saving your changes. Setting the ``Force Root Block p Tag``  option to false will never produce **p** tags on enter, or, automatically it will instead produce **br** elements and Shift+Enter will produce a **p**.

   Open the *Article* content type by opening the **Sidebar**, then click on |projectTools| -> *Content Types* -> *Article* -> *Open Type*.
   Scroll down to the ``Sections Repeating Group`` field, then click on the ``section_html`` field, which is an RTE.

   In the ``Properties Explorer`` on the right, remove the check mark on the property ``Force Root Block p Tag`` and ``Force p tags New Lines``.

4. We'll now add markup in the RTE to test that **div** is now allowed to be a child element (nested) of parent element **a**.

   Open the **Sidebar** then click on *Site Explorer* and edit one of the articles. Navigate to ``/articles/2020/7/`` then right click on ``New ACME Phone Released Today`` and select ``Edit``.

   Scroll down to the ``Content`` part of the form and under ``Sections``, click on ``Add Another``.

   Click on the newly added section, then click on ``Tools`` -> ``Code Editor`` from the RTE menubar, then add the following:

   .. code-block:: xml

      <a href="#">
        <div class="nesting_test_div">
          <img src="/static-assets/images/castle-pic.jpg" alt="" />
          <div class="nesting_test" title="Testing nesting elements">This is a test for nesting elements</div>
        </div>
      </a>

   |

   After saving your changes, preview the page and it should now display an image and text that's a link. Re-open the RTE code editor and verify that the markup you inputted is unchanged.

   .. figure:: /_static/images/site-admin/rte/rte-add-child-element-ex.webp
      :alt: RTE div child element added
      :width: 85%
      :align: center

   |

|hr|

~~~~~~~~~~~~~~~~~~~~~
Creating an RTE Setup
~~~~~~~~~~~~~~~~~~~~~
The RTE's configuration file looks like this:

.. code-block:: xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/ui.xml*
   :linenos:
   :emphasize-lines: 7

   <?xml version="1.0" encoding="UTF-8"?>
   <siteUi>
     ...
     <widget id="craftercms.components.TinyMCE">
        <configuration>
          <setups>
            <setup id="generic">
              <!-- Configuration options: https://www.tiny.cloud/docs/tinymce/latest/initial-configuration/ -->
              <!-- Plugins: https://www.tiny.cloud/docs/tinymce/latest/plugins/#open-source-plugins -->
              <tinymceOptions>
                <![CDATA[
                  {
                    "menubar": true,
                    "theme": "silver",
                    "plugins": "print preview searchreplace autolink directionality visualblocks visualchars fullscreen image link media template codesample table charmap hr pagebreak nonbreaking anchor toc insertdatetime advlist lists wordcount textpattern help acecode paste",
                    "extended_valid_elements": "",
                    "valid_children": "",
                    "toolbar1": "formatselect | bold italic strikethrough forecolor backcolor | link | alignleft aligncenter alignright alignjustify | numlist bullist outdent indent | removeformat",
                    "code_editor_wrap": false,
                    "toolbar_sticky": true,
                    "image_advtab": true,
                    "encoding": "xml",
                    "relative_urls": false,
                    "remove_script_host": false,
                    "convert_urls": false,
                    "remove_trailing_brs": false,
                    "media_live_embeds": true,
                    "autoresize_on_init": false,
                    "autoresize_bottom_margin": 0,
                    "menu": {
                      "tools": { "title": "Tools", "items": "tinymcespellchecker code acecode wordcount" },
                      "insert": { "title": "Insert", "items": "image link media template codesample inserttable | charmap hr | pagebreak nonbreaking anchor | insertdatetime" }
                      },
                      "automatic_uploads": true,
                      "file_picker_types":  "image media file",
                      "paste_data_images": true,
                      "templates": [],
                      "content_css": [],
                      "content_style": "body {}",
                      "contextmenu": false
                    }
                  ]]>
                </tinymceOptions>
              </setup>
            </setups>
          </configuration>
        </widget>
        ...

|

You can access the ``RTE Configuration`` file by going to the **Sidebar** then clicking on  |projectTools|. In the **Project Tools**, click on **Configuration**, then from the list, select ``User Interface Configuration``. Scroll down to the ``craftercms.components.TinyMCE`` widget section.

.. figure:: /_static/images/site-admin/rte/rte-setup-config-file-access.webp
   :alt: RTE Setup - Open RTE Configuration File in Studio
   :align: center
   :width: 80%

|

Inside the ``<setups>`` tag, there can be multiple ``<setup>`` tags. Each setup represents a possible RTE configuration that can be specified to be used by a RTE control. To add your own configuration, create a new ``<setup>`` tag. Each ``<setup>`` tag contains:

* An ``<id>`` tag with the name that must be specified for an RTE control to use this configuration.
* An ``<tinymceOptions>`` tag containing TinyMCE Configuration options (see https://www.tiny.cloud/docs/tinymce/latest/initial-configuration/ for more information) and plugins (see https://www.tiny.cloud/docs/tinymce/latest/plugins/#open-source-plugins for more information)

|hr|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Attaching an RTE in a Form to an RTE Setup
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To attach an RTE setup to an RTE in a form, open the content type that you want to add an RTE to, then go to the **Properties Explorer** and click on RTE Configuration and type in an RTE setup name.

.. figure:: /_static/images/site-admin/rte/rte-setup-form.webp
   :alt: RTE Setup - Add an RTE in the Form
   :align: center

|

In the image above, the RTE setup name used is **generic**. Please see the section above on how to create an RTE Setup, where the example shows an RTE Setup named **generic**.

|hr|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Inserting Links to Pages in the Rich Text Editor (RTE)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Users sometimes need to link to a page in the site to selected text in their document.
This section details how to setup the Rich Text Editor (RTE) to allow a user to browse or search for pages and insert links to them.

'''''''''''''''''''''''''''''
Basic Setup and Configuration
'''''''''''''''''''''''''''''
#. Open the content type with the Rich Text Editor (RTE) to be setup. Open the **Sidebar** and click on |projectTools| and select **Content Types**. Select the content type with the RTE you'd like to setup, then click on the **Open Type** button.
#. Setup the data source to select a page from the site. From the content model definition, go to the **Data Sources** panel and drag ``File Browse`` to the the ``Data Sources`` section of the form and fill in the following properties:

   * Title : Data source title to show on the form e.g. ``Pages``
   * Name : Name of variable to store the final result in e.g. ``pages``
   * Repository Path : Path where to browse the pages from e.g. ``/site/website``

#. Bind the data source setup above to the RTE. From the content model definition, click on the RTE you want to be able to browse or search for pages and insert links to them. Next, go to the **Properties Explorer** panel and scroll to the ``File Manager`` property. Put a check mark on the box next to the data source previously setup to bind it to the RTE.

#. Click on the **Save** button to save your changes. The RTE is now setup to allow a user to browse or search for pages and insert links to them.

'''''''
Example
'''''''
Let's take a look at an example using a site created using the ``Website Editorial`` blueprint. We will setup the RTE in the ``Article`` content type to allow a user to browse or search for pages and insert links to them. We will first setup the RTE, then see it in action.

#. Open the content type with the Rich Text Editor (RTE) to be setup. Open the **Sidebar** and click on |projectTools| and select **Content Types**. Click on **Open Existing Type**, and select the content type ``Article`` then click on the **Open Type** button.

#. Setup the data source to select a page from the site. From the content model definition, go to the **Data Sources** panel and drag ``File Browse`` to the ``Data Sources`` section of the form.

   .. figure:: /_static/images/developer/rte-add-file-browse-ds.webp
      :alt: Allow user to browse pages and insert link - add "File Browse" data source
      :width: 75%
      :align: center

   |

   Fill in the following properties:

   * Title : Pages
   * Name : pages
   * Repository Path : /site/website

   .. figure:: /_static/images/developer/rte-setup-ds-for-page-link.webp
      :alt: Allow user to browse pages and insert link - data source setup
      :width: 75%
      :align: center

   |

#. Bind the data source setup above to the RTE. From the content model definition, click on the RTE ``Section``. Next, go to the **Properties Explorer** panel and scroll to the ``File Manager`` property. Put a check mark on the box next to ``Pages``, the data source previously setup, to bind it to the RTE.

   .. figure:: /_static/images/developer/rte-link-bind-ds.webp
      :alt: Allow user to browse pages and insert link - bind the data source to RTE
      :width: 75%
      :align: center

   |

#. Click on the ``Save`` button.

Let's now take a look at the data source we setup and bound to the RTE in action.

#. Preview the article ``Coffee is Good for Your Health`` by either opening the **Sidebar** and navigating to ``/articles/2016/6/coffee-is-good-for-your-health`` or, from the ``Home`` page, click on the ``Health`` category, then click on ``Coffee is Good for Your Health``

#. Edit the article, then scroll down to the ``Section``
#. Select a word in the RTE. For our example, let's highlight the first word, ``Class``, then click on ``Insert/edit link`` from the toolbar

   .. figure:: /_static/images/developer/rte-select-word.webp
      :alt: Allow user to browse pages and insert link - select "Class" then click on "Insert/edit link"
      :width: 75%
      :align: center

   |

#. Click on the button next to ``URL`` then select ``Pages``. This is the data source we setup.

   .. figure:: /_static/images/developer/rte-insert-edit-link.webp
      :alt: Allow user to browse pages and insert link - Click on button next to "URL" then click on "Pages"
      :width: 35%
      :align: center

   |

#. Select a page to link to. We will link the page ``/article/2017/2/top-romantic-valentine-movies`` to the selected text in our RTE

   .. figure:: /_static/images/developer/rte-select-page-to-link-to.webp
      :alt: Allow user to browse pages and insert link - Click on button next to "URL" then click on "Pages"
      :width: 55%
      :align: center

   |

#. Save the link.

   .. figure:: /_static/images/developer/rte-save-link.webp
      :alt: Allow user to browse pages and insert link - Save the link"
      :width: 35%
      :align: center

   |

#. The link is now setup.

   .. figure:: /_static/images/developer/rte-link-to-page-created.webp
      :alt: Allow user to browse pages and insert link - Link created on word "Class" in RTE"
      :width: 75%
      :align: center

   |

|hr|

.. _extending-tinymce:

~~~~~~~~~~~~~~~~~
Extending TinyMCE
~~~~~~~~~~~~~~~~~
CrafterCMS  provides a general tool for extending TinyMCE via the ``craftercms_tinymce_hooks``.  It currently allows for hooking into the following (as shown by the example `here <https://github.com/craftercms/studio-ui/blob/master/static-assets/js/tinymce-plugins/craftercms_paste_extension/craftercms_tinymce_hooks.sample.js>`__):

- ``paste_preprocess`` callback
- ``paste_postprocess`` callback
- ``setup`` function

To hook into the paste pre/post process of TinyMCE, see :ref:`here <rte-paste-plugin-hooks>`.

|hr|

.. _studio-preview-deployer-config:

""""""""""""""""""""""""""""""
Preview Deployer Configuration
""""""""""""""""""""""""""""""
The following section of Studio's configuration overrides allows you to setup your deployer URLs

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    ############################################################
    ##                    Preview Deployer                    ##
    ############################################################

    # Default preview deployer URL (can be overridden per site)
    studio.preview.defaultPreviewDeployerUrl: ${env:DEPLOYER_URL}/api/1/target/deploy/{siteEnv}/{siteName}
    # Default preview create target URL (can be overridden per site)
    studio.preview.createTargetUrl: ${env:DEPLOYER_URL}/api/1/target/create_if_not_exists
    # Default preview create target URL (can be overridden per site)
    studio.preview.deleteTargetUrl: ${env:DEPLOYER_URL}/api/1/target/delete-if-exists/{siteEnv}/{siteName}
    # URL to the preview repository (aka Sandbox) where authors save work-in-progress
    studio.preview.repoUrl: ${env:CRAFTER_DATA_DIR}/repos/sites/{siteName}/sandbox

|

|hr|

.. _studio-preview-search-config:

""""""""""""""""""""""""""""
Preview Search Configuration
""""""""""""""""""""""""""""
The following section of Studio's configuration overrides allows you to set URLs for search in preview.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    ############################################################
    ##                   Preview Search                       ##
    ############################################################

    studio.preview.search.createUrl: ${env:SEARCH_URL}/api/2/admin/index/create
    studio.preview.search.deleteUrl: ${env:SEARCH_URL}/api/2/admin/index/delete/{siteName}

|

|hr|

.. _studio-search:

""""""
Search
""""""
The following section of Studio's configuration overrides allows you to setup the url for search

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    ################################################################
    ##                           Search                           ##
    ################################################################
    # URLs to connect to Search
    studio.search.urls: ${env:SEARCH_URL}
    # The username for Search
    studio.search.username: ${env:SEARCH_USERNAME}
    # The password for Search
    studio.search.password: ${env:SEARCH_PASSWORD}
    # The connection timeout in milliseconds, if set to -1 the default will be used
    studio.search.timeout.connect: -1
    # The socket timeout in milliseconds, if set to -1 the default will be used
    studio.search.timeout.socket: -1
    # The number of threads to use, if set to -1 the default will be used
    studio.search.threads: -1
    # Indicates if keep alive should be enabled for sockets used by the search client, defaults to false
    studio.search.keepAlive: false

|

The following properties allow you to configure the search connection pool max values for total connections and connections per route:

.. version_tag::
    :label: Since
    :version: 4.5.0

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    # The max total connections, if set to -1 the default will be used
    studio.search.maxTotalConnections: -1
    # The max connections per route, if set to -1 the default will be used
    studio.search.maxConnectionsPerRoute: -1

|

The default max connections per route is set to 2 and max total connections is set to 20.

|hr|

.. _cache-settings:

""""""""""""""
Cache Settings
""""""""""""""
Here are the cache control settings for templates and assets:

.. code-block:: yaml

    # If Studio should cache its FreeMarker templates
    studio.cache.templates: true
    # Indicates if the browser should cache responses for static-assets
    studio.cache.assets.enabled: true
    # The max age in seconds that the browser should cache responses for requests matching `studio.cache.assets.maxAge.includeUrls`
    studio.cache.assets.maxAge: 3600
    # The urls that should include max-age=<studio.cache.assets.maxAge> in Cache-Control header. Other urls will be set to default max-age=0, must-revalidate
    studio.cache.assets.maxAge.includeUrls: /static-assets/**,/1/plugin/file/**

|

|hr|

.. _studio-forwarded-headers:

"""""""""""""""""
Forwarded Headers
"""""""""""""""""
The following section of Studio's configuration overrides allows you to configure forwarded headers to resolve the actual hostname and protocol when it is behind a load balancer or reverse proxy. This is especially useful when setting up Studio behind a load balancer in AWS.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    ##################################################
    ##             Forwarded Headers                ##
    ##################################################
    # Indicates if Forwarded or X-Forwarded headers should be used when resolving the client-originated protocol and
    # address. Enable when Studio is behind a reverse proxy or load balancer that sends these.
    studio.forwarded.headers.enabled: false

|

|hr|

.. _studio-policy-headers:

""""""""""""""
Policy Headers
""""""""""""""
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Content Security Policy (CSP) Header
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. version_tag::
	:label: Since
	:version: 4.1.2

The following allows you to configure which resources can be loaded (e.g. JavaScript, CSS, Images, etc.)
and the URLs that they can be loaded from.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    # Value for the Content-Security-Policy header
    studio.security.headers.contentSecurityPolicy.value: default-src 'unsafe-inline'
    # Set to true to enable the Content-Security-Policy-Report-Only header (this will report in the user agent console instead of actually blocking the requests)
    studio.security.headers.contentSecurityPolicy.reportOnly: true

To block offending requests, set ``studio.security.headers.contentSecurityPolicy.reportOnly`` to ``false``.
This property is set to ``true`` by default

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
X-Permitted-Cross-Domain-Policies Header
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The following allows you to configure what other domains you want to allow access to your domain.
The X-PERMITTED-CROSS-DOMAIN-POLICIES header is set to ``none`` (do not allow any embedding) by default.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    # Value for the X-PERMITTED-CROSS-DOMAIN-POLICIES header
    studio.security.headers.permittedCrossDomainPolicies.value: none


|

|hr|

.. _studio-referrer-policy:

""""""""""""""""""""""
Referrer Policy Header
""""""""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.3.1

The following allows you to limit the information available in the Referer header or to not send the Referer header.
The referrer policy header is set to ``NO_REFERRER`` (Never send the Referer header) by default.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    # Value for Referrer-Policy header.
    # Possible values are defined in org.springframework.security.web.header.writers.ReferrerPolicyHeaderWriter.ReferrerPolicy
    studio.security.headers.referrerPolicy.value: NO_REFERRER

.. note:: Possible referrer policy values are defined in `org.springframework.security.web.header.writers.ReferrerPolicyHeaderWriter.ReferrerPolicy <https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/web/header/writers/ReferrerPolicyHeaderWriter.ReferrerPolicy.html>`__

|hr|

.. _studio-crafterSite-cookie-domain:

"""""""""""""""""""""""""
crafterSite Cookie Domain
"""""""""""""""""""""""""
.. version_tag::
	:label: Since
	:version: 4.0.1

The following section of Studio's configuration overrides allows you to set the ``crafterSite`` cookie at the base domain instead of a subdomain to allow visibility of the ``crafterSite`` cookie across subdomains.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    # Use base domain instead of subdomain for the crafterSite cookie
    studio.cookie.useBaseDomain: false

|

|hr|

.. _studio-deployer-http-request-timeout:

""""""""""""""""""""""
Deployer HTTP Requests
""""""""""""""""""""""
.. version_tag::
	:label: Since
	:version: 4.2.0

The following section of Studio's configuration overrides allows you to set the timeout for Deployer HTTP requests.
The default timeout is 5 minutes.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    # Response timeout in seconds for deployer http requests
    studio.deployer.request.timeoutSeconds: 300

|

|hr|

.. _studio-serverless-delivery-targets:

"""""""""""""""""""""""""""
Serverless Delivery Targets
"""""""""""""""""""""""""""
The following section of Studio's configuration overrides allows you to set up serverless delivery.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    ##########################################################
    ##                 Serverless Delivery                  ##
    ##########################################################
    # Indicates if serverless delivery is enabled
    # studio.serverless.delivery.enabled: false
    # The URL for the serverless delivery deployer create URL
    # studio.serverless.delivery.deployer.target.createUrl: ${studio.preview.createTargetUrl}
    # The URL for the serverless delivery deployer delete URL
    # studio.serverless.delivery.deployer.target.deleteUrl: ${studio.preview.deleteTargetUrl}
    # The template name for serverless deployer targets
    # studio.serverless.delivery.deployer.target.template: aws-cloudformed-s3
    # Replace existing target configuration if one exists?
    # studio.serverless.delivery.deployer.target.replace: false
    # The URL the deployer will use to clone/pull the site's published repo. When the deployer is in a separate node
    # (because of clustering), this URL should be an SSH/HTTP URL to the load balancer in front of the Studios
    # studio.serverless.delivery.deployer.target.remoteRepoUrl: ${env:CRAFTER_DATA_DIR}/repos/sites/{siteName}/published
    # The deployer's local path where it will store the clone of the published site. This property is not needed if
    # the deployer is not the preview deployer, so you can leave an empty string ('') instead
    # studio.serverless.delivery.deployer.target.localRepoPath: ${env:CRAFTER_DATA_DIR}/repos/aws/{siteName}
    # Parameters for the target template. Please check the deployer template documentation for the possible parameters.
    # The following parameters will be sent automatically, and you don't need to specify them: env, site_name, replace,
    # disable_deploy_cron, local_repo_path, repo_url, use_crafter_search
    # studio.serverless.delivery.deployer.target.template.params:
    #   # The delivery search endpoint (optional is authoring is using the same one, specified in the SEARCH_URL env variable)
    #   search_url:
    #   aws:
    #     # AWS region (optional if specified through default AWS chain)
    #     region: us-east-1
    #     # AWS access key (optional if specified through default AWS chain)
    #     default_access_key:
    #     # AWS secret key (optional if specified through default AWS chain)
    #     default_secret_key:
    #     cloudformation:
    #       # AWS access key (optional if aws.accessKey is specified)
    #       access_key:
    #       # AWS secret key (optional if aws.secretKey is specified)
    #       secret_key:
    #       # Namespace to use for CloudFormation resources (required when target template is aws-cloudformed-s3)
    #       namespace: myorganization
    #       # The domain name of the serverless delivery LB (required when target template is aws-cloudformed-s3)
    #       deliveryLBDomainName:
    #       # The SSL certificate ARN the CloudFront CDN should use (optional when target template is aws-cloudformed-s3)
    #       cloudfrontCertificateArn:
    #       # The alternate domains names (besides *.cloudfront.net) for the CloudFront CDN (optional when target template is aws-cloudformed-s3)
    #       alternateCloudFrontDomainNames:

|

|hr|

.. _studio-cloudformation-capabilities:

"""""""""""""""""""""""""""
CloudFormation Capabilities
"""""""""""""""""""""""""""
.. version_tag::
	:label: Since
	:version: 4.2.0

The following section of Studio's configuration overrides allows you to configure CloudFormation capabilities.
This allows users to pass in the capabilities from the Deployer target configuration in ``custom-serverless-site-stack.yaml``
See :ref:`aws-cloudformation-target` for more information.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    # # A comma-separated string listing the required capabilities for the CloudFormation stack. Ex: CAPABILITY_IAM,CAPABILITY_NAMED_IAM,CAPABILITY_AUTO_EXPAND (optional)
    # stackCapabilities:

|

Here's an example of configuring the ``CAPABILITY_IAM``, ``CAPABILITY_NAMED_IAM`` and ``CAPABILITY_AUTO_EXPAND`` stack
capabilities:

.. code-block:: yaml
    :emphasize-lines: 6
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

    studio.serverless.delivery.deployer.target.template.params:
      aws:
        ...
        cloudformation:
        ...
          stackCapabilities: CAPABILITY_IAM,CAPABILITY_NAMED_IAM,CAPABILITY_AUTO_EXPAND

|

.. _notifications-configuration:

""""""""""""""""""""""""""""""""""""
Workflow Notifications Configuration
""""""""""""""""""""""""""""""""""""
Crafter Studio provides a simple workflow option that includes submission, review/reject and approve and
publish immediate / publish on a schedule options. This document covers the configuration of the HTML notifications
that can be sent at each point in the workflow. To setup your email server, please see :ref:`studio-smtp-config`

~~~~~~
Basics
~~~~~~
All configuration for the notification system is done by a site admin (on a per site basis) in the following configuration file:

'''''
Where
'''''
.. code-block:: xml
    :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/notifications.xml*

    <notificationConfig>
        ...
    </notificationConfig>

This can be modified/accessed through Crafter Studio, by going to the **Sidebar**, then clicking on |projectTools| -> **Configuration** -> **Notification Configuration**

.. image:: /_static/images/site-admin/notification-config-open.webp
    :align: center
    :width: 50%
    :alt: Configuration - Open Notification Configuration

'''''''''
Templates
'''''''''
Templates are used for the email messages sent for workflow states in the configuration file mentioned above. The template used is Freemarker (also known as FTL).
Variables are referenced in the template like `${VARIABLE}` or as part of a Freemarker statement like `<#list files as file>...</#list>`
Dates can be formatted like so: `scheduleDate?string["MMMMM dd, yyyy 'at' hh:mm a"]}`

A full guide to FTL can be found here: http://freemarker.org/

~~~~~~~~~~~~~~~~~~
Template Variables
~~~~~~~~~~~~~~~~~~
Here are some template variables used in CrafterCMS:

''''''''''''''''
Common Variables
''''''''''''''''
+-----------------------------+-----------------------------------------------------------+
|| Variable Name              || Description                                              |
+=============================+===========================================================+
|| date                       || Date for submission                                      |
+-----------------------------+-----------------------------------------------------------+
|| files                      || Collection of file objects in submission.                |
||                            || Usually iterated over `<#list files as file>...</#list>` |
+-----------------------------+-----------------------------------------------------------+
|| `file`.name                || File name including full repository path                 |
+-----------------------------+-----------------------------------------------------------+
|| `file`.internalName        || File internal CMS label                                  |
+-----------------------------+-----------------------------------------------------------+
|| submitter                  || Content submitter object, has sub properties             |
+-----------------------------+-----------------------------------------------------------+
|| submitter.firstName        || First name                                               |
+-----------------------------+-----------------------------------------------------------+
|| submitter.lastName         || Last Name                                                |
+-----------------------------+-----------------------------------------------------------+
|| submitter.username         || Authoring User Name / ID                                 |
+-----------------------------+-----------------------------------------------------------+
|| submissionComments         || String containing submission comments                    |
+-----------------------------+-----------------------------------------------------------+
|| scheduleDate               || Date content is scheduled for                            |
+-----------------------------+-----------------------------------------------------------+
|| siteName                   || ID of the site                                           |
+-----------------------------+-----------------------------------------------------------+
|| liveUrl                    || Live Server URL base                                     |
+-----------------------------+-----------------------------------------------------------+
|| authoringUrl               || Authoring Server URL base                                |
+-----------------------------+-----------------------------------------------------------+


''''''''''''''''''''''''''''''''
Deployment Error Notice Variable
''''''''''''''''''''''''''''''''
+-----------------------------+---------------------------------------------------------+
|| Variable Name              || Description                                            |
+=============================+=========================================================+
|| deploymentError            || Error message on deployment. Currently must be         |
||                            || addressed as ${deploymentError.toString()}             |
+-----------------------------+---------------------------------------------------------+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Configure Who Gets Notifications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Configure who gets notifications by entering the email addresses of the people you want to send notifications to, in between the tags ``<deploymentFailureNotification>`` and/or ``<approverEmails>``

.. code-block:: xml
    :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/notifications.xml*
    :linenos:

    <notificationConfig>
      <lang name="en">
        <deploymentFailureNotification>
          <email>EMAIL ADDRESS TO NOTIFY ON FAILURE</email>
        </deploymentFailureNotification>
        <approverEmails>
          <email>EMAIL ADDRESS TO NOTIFY SUBMISSION</email>
          <email>EMAIL ADDRESS TO NOTIFY SUBMISSION</email>
        </approverEmails>

            ...
      </lang>
    </notificationConfig>

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Configure Studio Workflow Dialog Messages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Below is a sample of Studio workflow dialog messages defined in our notifications configuration file.

.. code-block:: xml
    :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/notifications.xml*
    :linenos:

        <notificationConfig>
          <lang name="en">
            ...

            <generalMessages>
              <content key="scheduling-policy"><![CDATA[The {siteName} processes all publishing requests each business day, between 4PM EST and 6PM EST, unless a specific date/time is requested.<br/><br/>All requests received after 4PM EST may not be processed until the next business day.<br/><br/>If you have any questions about this policy or need a publish request processed immediately, please email the administrator.]]>
              </content>
            </generalMessages>

            <cannedMessages>
              <content  title="Not Approved" key="NotApproved"><![CDATA[Please make the following revisions and resubmit.]]></content>
              <content  title="Typos" key="Typos"><![CDATA[This content has multiple misspellings and/or grammatical errors. Please correct and re-submit.]]></content>
              <content  title="Incorrect Branding" key="IB"><![CDATA[This content uses incorrect or outdated terms, images, and/or colors. Please correct and re-submit.]]></content>
              <content  title="Broken Links" key="BrokenLinks"><![CDATA[This content has non-working links that may be due to incomplete and/or misspelled URLs. Any links directing users to websites without the Acme.com primary navigation, or directing users to a document must open in a new browser window. Please correct and re-submit.]]></content>
              <content  title="Needs Section Owner's Approval" key="NSOA"><![CDATA[This content needs the approval of its section&apos;s owner to insure there is no negative impact on other pages/areas of section, etc. Once you have their approval please email the Web Marketing Operations Team and re-submit this publish request.]]></content>
            </cannedMessages>

            <completeMessages>
              <content key="submitToGoLive"><![CDATA[An email notification has been sent to the team. Your content will be reviewed and (if approved) pushed live between 4PM EST and 6PM EST of the business day that the request was received. If this request is sent after business hours, it will be reviewed and (if approved) pushed live as soon as possible, the next business day.<br/><br/>If you need to make further revisions to this item, please re-submit this publish request after making them.<br/><br/>If this request needs immediate attention, please email the administrator.]]></content>
              <content key="delete">
                Item(s) has been pushed for delete. It will be deleted shortly.
              </content>
              <content key="go-live">Item(s) has been pushed live. It will be visible on the live site shortly.</content>
              <content key="schedule-to-go-live">The scheduled item(s) will go live on: ${date}.&lt;br/&gt;&lt;br/&gt;</content>
              <content key="reject">Rejection has been sent. Item(s) have NOT been pushed live and have returned to draft state.</content>
              <content key="delete">Item(s) has been pushed for delete. It will be deleted shortly.</content>
              <content key="schedule-to-go-live">Item(s) have been scheduled to go live.</content>
            </completeMessages>

              ...
          </lang>
        </notificationConfig>

~~~~~~~~~~~~~~~~~~~
Configure Templates
~~~~~~~~~~~~~~~~~~~
Below is an example of a configured email messages for each point in the workflow, found in between the tag <emailTemplates> in the notifications configuration file.

.. code-block:: xml
    :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/notifications.xml*
    :linenos:

    <notificationConfig>
      <lang name="en">
        ...
        <emailTemplates>
          <emailTemplate key="deploymentError">
            <subject>Deployment error on site ${siteName}</subject>
            <body><![CDATA[
                    <html>
                      <head>
                        <meta charset="utf-8"/>
                      </head>
                      <body style=" font-size: 12pt;">
                        <p>
                          The following content was unable to deploy:
                          <ul>
                            <#list files as file>
                              <li>${file.internalName!file.name}</li>
                            </#list>
                          </ul>
                          Error:<br/>
                          ${deploymentError.toString()}
                          <br/><br/>
                          <a href="${liveUrl}" >
                            <img style="max-width: 350px;  max-height: 350px;" src="${liveUrl}/static-assets/images/workflow-email-footer.png" alt="" />
                          </a>
                        </p>
                      </body>
                    </html>
            ]]></body>
          </emailTemplate>

          <emailTemplate key="contentApproval">
            <subject><![CDATA[<#if scheduleDate??>Content Scheduled <#else>Content Approved</#if>]]></subject>
            <!-- Timezone can/is being overwritten in the following template -->
            <body><![CDATA[
                     <#setting time_zone='EST'>
                     <html>
                       <head>
                         <meta charset="utf-8"/>
                       </head>
                       <body style=" font-size: 12pt;">
                         <p>
                           <#if scheduleDate??>
                             The following content has been scheduled for publishing on ${scheduleDate?string["MMM dd, yyyy 'at' hh:mm a"]} Eastern Time.
                           <#else>
                             The following content has been reviewed and approved by ${approver.firstName!approver.username} ${approver.lastName!""}:
                           </#if>
                           <ul>
                             <#list files as file>
                               <#if file.page>
                                 <a href="${liveUrl}/${file.browserUri!""}">
                               </#if>
                               <li>${file.internalName!file.name}</li>
                                 <#if file.page>
                                   </a>
                                 </#if>
                             </#list>
                           </ul><br/>
                           <#if scheduleDate??>
                             <a href="${liveUrl}">Click Here to View Your Published Content</a>
                             <br/>
                           </#if>
                           <a href="${authoringUrl}/site-dashboard" >
                             <img style="max-width: 350px;  max-height: 350px;" src="${liveUrl}/static-assets/images/workflow-email-footer.png" alt="" />
                           </a>
                         </p>
                       </body>
                     </html>
                     ]]></body>
          </emailTemplate>

          <emailTemplate key="submitToApproval">
            <subject>Content Review</subject>
            <body><![CDATA[
                     <#setting time_zone='EST'>
                     <html>
                       <head>
                         <meta charset="utf-8"/>
                       </head>
                       <body style=" font-size: 12pt">
                         <p>
                           ${submitter.firstName!submitter.username} ${submitter.lastName} has submitted items for your review:
                           <ul>
                             <#list files as file>
                               <#if file.page>
                                 <a href="${authoringUrl}/preview/#/?page=${file.browserUri!""}&site=${siteName}">
                               </#if>
                               <li>${file.internalName!file.name}</li>
                               <#if file.page>
   	                             </a>
                               </#if>
                             </#list>
                           </ul>
                           <#if submissionComments?has_content>
                             Comments:<br/>
                             ${submissionComments!""}
                             <br/>
                           </#if><br/>
                           <a href="${previewUrl}/site-dashboard">Click Here to View Content Waiting for Approval</a>
                           <br/><br/>
                           <a href="${liveUrl}" >
                             <img style="max-width: 350px;  max-height: 350px;" src="${liveUrl}/static-assets/images/workflow-email-footer.png" alt="" />
                           </a>
                         </p>
                       </body>
                     </html>
                     ]]></body>
          </emailTemplate>

          <emailTemplate key="contentRejected">
            <subject>Content Requires Revision</subject>
            <body><![CDATA[
   			         <#setting time_zone='EST'>
                     <html>
                       <head>
                         <meta charset="utf-8"/>
                       </head>
                       <body style=" font-size: 12pt;">
                         <p>
                           The following content has been reviewed and requires some revision before it can be approved:
                           <ul>
                             <#list files as file>
                               <#if file.page>
                                 <a href="${authoringUrl}/preview/#/?page=${file.browserUri!""}&site=${siteName}">
                               </#if>
                               <li>${file.internalName!file.name}</li>
                               <#if file.page>
                                 </a>
                               </#if>
                             </#list>
                           </ul>
                           Reason:<br/>
                           ${rejectionReason!""}
                           <br/><br/>
                           <a href="${authoringUrl}/site-dashboard" >
                             <img style="max-width: 350px;  max-height: 350px;" src="${liveUrl}/static-assets/images/workflow-email-footer.png" alt="" />
                           </a>
                         </p>
                       </body>
                     </html>
                     ]]></body>
          </emailTemplate>
        </emailTemplates>
      </lang>
    </notificationConfig>

|hr|

.. _studio-validations-regex:

"""""""""""""""""
Validations Regex
"""""""""""""""""
.. version_tag::
	:label: Since
	:version: 4.0.3

CrafterCMS validates API requests related to users and groups through regex restrictions to avoid malicious payloads.

The following section of Studio's configuration overrides allows you to configure the regex used for validating user names and group names to suit your needs.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

    ##########################################################
    ##                  Input Validations                   ##
    ##########################################################
    # These properties override default validation regex patterns
    # from crafter common validations.
    # Key should have the form `studio.validation.regex.KEY_NAME`
    # Value should be a valid java regex.
    #
    # studio.validation.regex.HTTPParameterName: "^[a-zA-Z0-9_\\-]{1,32}$"
    # studio.validation.regex.SITEID: "^[a-z0-9\-_]*$"
    # studio.validation.regex.EMAIL: "^([\\w\\d._\\-#])+@([\\w\\d._\\-#]+[.][\\w\\d._\\-#]+)+$"
    # studio.validation.regex.USERNAME: "^[a-zA-Z][\\w.\\-@+]+$"
    # studio.validation.regex.GROUP_NAME: "^[a-zA-Z][\\w.\\-]*$"
    # studio.validation.regex.ALPHANUMERIC: "^[a-zA-Z0-9]*$"
    # studio.validation.regex.SEARCH_KEYWORDS: "^[\\w\\s\\-\\\"\\'\\.!@#$%&\\*\\/\\(\\)\\[\\]\\p{IsLatin}]*$"
    # studio.validation.regex.CONTENT_PATH_WRITE: "^/?([\\w\\- ]+/?)*(((crafter\\-level\\-descriptor\\.level)|([\\w\\- ]))+\\.[\\w]+)?$"
    # studio.validation.regex.CONTENT_PATH_READ: "^/?([\\w\\p{IsLatin}@$%^&{}\\[\\]()+\\-=,.:~'`]+(\\s*[\\w\\p{IsLatin}/@$%^&{}\\[\\]()+\\-=,.:~'`])*(/?))*$"
    # studio.validation.regex.CONTENT_FILE_NAME_WRITE: "^((crafter\\-level\\-descriptor\\.level)|([a-z0-9_\\-])+)\\.xml$"
    # studio.validation.regex.CONFIGURATION_PATH: "^([a-z0-9\\-_/]+([.]*[a-z0-9\\-_])+)*(\\.[\w]+)?/?$"

|

|hr|

.. _studio-disk-monitoring:

"""""""""""""""
Disk Monitoring
"""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.4.3

Crafter Studio watches disk utilization for the ``data/repos`` directory and takes action when a threshold is reached.

The following section of Studio's configuration overrides allows you to configure the thresholds, notification email
and webhook when a threshold is reached.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

    ##################################################
    ##               Disk monitoring                ##
    ##################################################
    studio.monitoring.disk.highWaterMark: 90
    studio.monitoring.disk.lowWaterMark: 85
    studio.monitoring.disk.cron: '0 0/15 * * * ?'

    studio.monitoring.disk.notifications.encoding: UTF-8
    studio.monitoring.disk.notifications.dateTimePattern: MM/dd/yyyy hh:mm:ss.SSS a z

    # Email configs
    studio.monitoring.disk.notifications.email.enabled: true
    studio.monitoring.disk.notifications.email.subject: Disk space warning
    studio.monitoring.disk.notifications.email.to: admin@example.com
    studio.monitoring.disk.notifications.email.template: notification/templates/email/disk-monitor-alarm.ftl
    studio.monitoring.disk.notifications.email.html: true
    # Webhook configs
    studio.monitoring.disk.notifications.webhook.enabled: false
    studio.monitoring.disk.notifications.webhook.url: http://example.com/notify
    studio.monitoring.disk.notifications.webhook.method: POST
    studio.monitoring.disk.notifications.webhook.contentType: application/json
    studio.monitoring.disk.notifications.webhook.template: notification/templates/webhook/disk-monitor-alarm.ftl

Where:

- **studio.monitoring.disk.highWaterMark**: The disk utilization percentage level that triggers an action to perform
  ``git gc``, then the low watermark threshold is checked after, which determines whether an alarm is set.
- **studio.monitoring.disk.lowWaterMark**: The disk utilization percentage level that triggers the low disk space alarm.
  Once the low disk space alarm is set, it gets cleared when the disk utilization percentage goes below the low watermark.
  Note that the low watermark value **must be less than the high watermark value**.
- **studio.monitoring.disk.cron**: The cron expression for scheduling how often disk utilization is checked against the
  configured thresholds (high and low watermark).
- **studio.monitoring.disk.notifications.webhook.method**: The webhook HTTP request method (verb), e.g. POST, PUT, GET, etc.
- **studio.monitoring.disk.notifications.email.template**: The template used for sending the alarm state email
  Note also that emails are sent using the email in ``studio.mail.from.default`` as the from address.
  See :ref:`studio-smtp-config` for more information on configuring the mail client to send emails from Crafter Studio.
- **studio.monitoring.disk.notifications.webhook.template**:
  The default built-in template for webhook notification in ``studio.monitoring.disk.notifications.webhook.template``
  works for `slack <https://slack.com>`_. If you wish to change the webhook response, simply add your customized templates
  to ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension``.

To change the default templates (webhook template or email template) to a custom one, simply add your customized
templates to ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension``.

For example, we have a custom webhook template ``my-disk-monitor-alarm.ftl`` that we will now place under
``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/my-disk-monitor-alarm.ftl``
that we want to use instead of the default. We'll now configure Studio to use our custom template by modifying the
``studio.monitoring.disk.notifications.webhook.template`` value in the Studio configuration override file:

.. code-block::
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

    studio.monitoring.disk.notifications.webhook.template: my-disk-monitor-alarm.ftl

|hr|

.. _studio-commit-message:

""""""""""""""
Commit Message
""""""""""""""
Here are the default commit messages when someone makes content changes and can be customized by overriding them
using one of the override files.

.. code-block:: yaml
    :linenos:

    # Repository commit prologue message
    studio.repo.commitMessagePrologue:
    # Repository commit postscript message
    studio.repo.commitMessagePostscript:
    # Sandbox repository write commit message
    studio.repo.sandbox.write.commitMessage: "User {username} wrote content {path}"
    # Published repository commit message
    studio.repo.published.commitMessage: "Publish event triggered by {username} on {datetime} via {source}.\n\nPublish note from user: \"{message}\"\n\nCommit ID: {commit_id}\n\nPackage ID: {package_id}"
    # Commit message to mark commit not to process when syncing database
    studio.repo.syncDB.commitMessage.noProcessing: "STUDIO: NO PROCESSING"
    # Create new repository commit message
    studio.repo.createRepository.commitMessage: "Create new repository."
    # Create sandbox branch commit message
    studio.repo.createSandboxBranch.commitMessage: "Create {sandbox} branch."
    # Initial commit message
    studio.repo.initialCommit.commitMessage: "Initial commit."
    # Create as orphan commit message
    studio.repo.createAsOrphan.commitMessage: "Created as orphan."
    # Blueprints updated commit message
    studio.repo.blueprintsUpdated.commitMessage: "Blueprints updated."
    # Create folder commit message
    studio.repo.createFolder.commitMessage: "Created folder site: {site} path: {path}"
    # Delete content commit message
    studio.repo.deleteContent.commitMessage: "Delete file {path}"
    # Move content commit message
    studio.repo.moveContent.commitMessage: "Moving {fromPath} to {toPath}"
    # Copy content commit message
    studio.repo.copyContent.commitMessage: "Copying {fromPath} to {toPath}"

|

|hr|

.. _studio-audit-log:

"""""""""
Audit Log
"""""""""
.. version_tag::
	:label: Since
	:version: 4.1.3

CrafterCMS allows disabling the job for populating the audit log from external git changes. When disabled, the audit table will not log external operations synced from git. Crafter Studio updates and changes are always audited. Disabling this job improves performance for large git pull operations.

To disable populating the audit log, set the ``studio.clockJob.task.auditLogProcessing.disableAudit`` property to ``true``.

.. code-block:: yaml
    :caption: *studio-config-override.yaml*

    # Disable the db audit log population
    studio.clockJob.task.auditLogProcessing.disableAudit: false

|

|hr|

.. _publishing-blacklist:

""""""""""""""""""""
Publishing Blacklist
""""""""""""""""""""
CrafterCMS allows the creation of a publishing blacklist to prevent certain unwanted items from being published.

A comma-separated list of regexes is used to configure items that should not be published.

To configure the publishing blacklist, using your favorite editor open ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml`` or open the Global Studio Configuration Override file **studio-config-override.yaml** located under ``CRAFTER_HOME/data/repos/global/configuration`` that can be accessed from Studio from the ``Main Menu`` under ``Global Config``.

Add the following lines with the regex for the item you wish not to be published. By default, ``.keep`` files are not published by CrafterCMS. Just add a ``,`` then your regex after ``.*/\.keep``:

.. code-block:: yaml
    :caption: *studio-config-override.yaml*
    :emphasize-lines: 6

    # Paths matching git pathspecs on this list will never be published.
    # For information on git pathspec, see https://git-scm.com/docs/gitglossary#Documentation/gitglossary.txt-aiddefpathspecapathspec
    # .keep files are always ignored
    # e.g.:
    # studio.configuration.publishing.blacklist.pathspecs: "*.png"
    studio.configuration.publishing.blacklist.pathspecs: ""

|

Items in the publishing blacklist will not be published but will instead be marked as published and logged (debug level) in the tomcat log, why the item was not published.

.. code-block:: text

    [DEBUG] 2021-04-22T08:16:01,023 [studio.clockTaskExecutor-42] [deployment.PublishingManagerImpl] | File /static-assets/css/.keep of the site mysite will not be published because it matches the configured publishing blacklist regex patterns.

~~~~~~~
Example
~~~~~~~
Let's take a look at an example.

Create a site using the website editorial blueprint, then create the folder ``mytempimages`` under ``/static-assets/images``.

Say, you do not want files under ``/static-assets/images/mytempimages`` to be published when a user performs a bulk publish or *Approve & Publish* of multiple items from the dashboard. We'll add to the ``studio.configuration.publishing.blacklist.pathspecs`` the pathspecs for items under ``/static-assets/images/mytempimages``

.. code-block:: yaml
    :caption: *studio-config-override.yaml*

    # Publishing blacklist configuration, items matching regexes on this list will never be published
    studio.configuration.publishing.blacklist.pathspecs: >-
        .*/\.keep,\/static-assets\/images\/mytempimages\/.*

|

Save your changes and restart Studio.

Upload an image under ``/static-assets/images/mytempimages``

.. image:: /_static/images/system-admin/studio/publishing-blacklist-example.webp
   :alt: System Administrator - Publishing blacklist example file uploaded that will not be published"
   :width: 30 %
   :align: center

Publish the uploaded image by right-clicking on the image, then select **Approve & Publish**. The **Approve for Publish** dialog will open up. Select **Items should be published now**, then click on the **Submit** button.

After publishing, open the **Sidebar** again and navigate to ``/static-assets/images/mytempimages``. Notice that your file has been marked published.

.. image:: /_static/images/system-admin/studio/publishing-blacklist-example-published.webp
   :alt: System Administrator - Publishing blacklist example file published"
   :width: 45 %
   :align: center

Let's take a look at the tomcat log, notice that it was logged that the file we uploaded will not be published because it is in the publishing blacklist:

.. code-block:: text
   :caption: *Tomcat log of item in publishing blacklist*
   :emphasize-lines: 3

   [INFO] 2021-04-22T12:48:24,903 [studio.clockTaskExecutor-36] [job.StudioPublisherTask] | Starting publishing on environment live for site mysite
   [DEBUG] 2021-04-22T12:48:28,990 [studio.clockTaskExecutor-36] [deployment.PublishingManagerImpl] | Environment is live, transition item to LIVE state mysite:/static-assets/images/mytempimages/26072150271_848c0008f0_o.jpg
   [DEBUG] 2021-04-22T12:48:28,992 [studio.clockTaskExecutor-36] [deployment.PublishingManagerImpl] | File /static-assets/images/mytempimages/26072150271_848c0008f0_o.jpg of the site mysite will not be published because it matches the configured publishing blacklist regex patterns.
   [INFO] 2021-04-22T12:48:29,014 [studio.clockTaskExecutor-36] [job.StudioPublisherTask] | Finished publishing environment live for site mysite

|

|hr|

.. _configuration-files-maximum:

"""""""""""""""""""""""""""
Configuration Files Maximum
"""""""""""""""""""""""""""
.. version_tag::
	:label: Since
	:version: 4.1.4

To set the maximum size of a project/site configuration file for the :base_url:`write_configuration <_static/api/studio.html#tag/configuration/operation/writeConfiguration>` API, set the following property:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/data/repos/global/configuration/studio-config-override.yaml*

    # The maximum length of configuration content for the configuration service. Default to 512kB -> 512 * 1024
    studio.configuration.maxContentSize: 524288

|

|hr|

.. _git-configuration:

"""""""""""""""""
Git Configuration
"""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.4.3

To enable updating the Git properties listed below, set the following to ``true``:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/data/repos/global/configuration/studio-config-override.yaml*
    :emphasize-lines: 6

    ##################################################
    ##        Git configuration properties          ##
    ##################################################
    # Git config update to following properties is performed on global configuration and might
    # affect unintended repositories. Opt-out of this by setting this property to false.
    studio.repo.git.global.update.enabled: true

To configure the ``git gc`` options prune expire and auto pack limit, set the following properties:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/data/repos/global/configuration/studio-config-override.yaml*
    :emphasize-lines: 5,7

    # git gc --auto will consolidate into one the packs older than this value
    # Notice that this property works when the number of packs is greater than gc.autoPackLimit.
    # See https://git-scm.com/docs/git-gc#Documentation/git-gc.txt-gcautoPackLimit
    # This value is used to configure "gc.prunePackExpire" property, leave empty to skip property setting
    studio.repo.gc.prunePackExpire: 1.hour.ago
    # Set global gc.autoPackLimit property
    studio.repo.gc.autoPackLimit: 50

where:

- **studio.repo.gc.prunePackExpire**: This maps to Git's "gc.pruneExpire". Unreachable objects older than this value
  are pruned when running ``git gc``. Example values accepted are: ``1.hour.ago``, ``2.weeks.ago`` , ``now``, or ``never``.
  See `Prune expire <https://git-scm.com/docs/git-config#Documentation/git-config.txt-gcpruneExpire>`__ in the Git docs
  for valid values for the option.

- **studio.repo.gc.autoPackLimit**: This maps to Git's "gc.autoPackLimit". Packs are consolidated when the number
  of packs exceeds the value in this property.
  See `Auto pack limit <https://git-scm.com/docs/git-config#Documentation/git-config.txt-gcautoPackLimit>`__ in the Git
  docs for valid values for the option.

These properties are configured on Studio startup as git global properties. Values configured are just passed to git.
Remember that you need to restart Studio for the changes you make to the above properties to take effect.

.. important::
    The ``studio.repo.git.global.update.enabled`` property allows you to opt-out of the git config update.
    Note the importance of disabling this property for local developer deployment since it's a global change
    of Git's default behavior and may affect unintended repositories.

|

|hr|

.. _content-type-editor-configuration:

""""""""""""""""""""""""""
Content Type Editor Config
""""""""""""""""""""""""""
The Content Type Editor Config configuration file defines what tools are available in the Content Type Editor.
This configuration is unique in that a configuration file exists in the following location of
each project: ``SITENAME/config/studio/administration/site-config-tools.xml``

.. image:: /_static/images/site-admin/configuration-tool-config.webp
    :align: center
    :width: 25%
    :alt: Content Type Editor Config

|

To modify the Content Type Editor Config configuration, click on |projectTools| from the bottom of the *Sidebar*,
then click on **Configuration** and select **Content Type Editor Config** from the list.

.. image:: /_static/images/site-admin/config-open-content-type-editor-config.webp
    :alt: Configurations - Open Content Type Editor Config Tools
    :width: 65 %
    :align: center

|

~~~~~~
Sample
~~~~~~
Here is a sample Content Type Editor Config configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample Content Type Editor Config configuration file</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-site-config-tools.xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/administration/site-config-tools.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|
|

~~~~~~~~~~~
Description
~~~~~~~~~~~
''''''''''''''''''''''''''''''''''''''''
Content Type Specific tool configuration
''''''''''''''''''''''''''''''''''''''''

    ``/config/tools/tool/controls``
        List of available content type form controls
    ``/config/tools/tool/controls/control``
        Control name (JavaScript control module name)
    ``/config/tools/tool/datasources``
        List of available datasources for content type form controls
    ``/config/tools/tool/datasources/datasource``
        Datasource name (JavaScript datasource module name)
    ``/config/tools/tool/objectTypes``
        List of available object types
    ``/config/tools/tool/objectTypes/type``
        Type configuration (Page or Component) - name, label, properties

''''''''''''''''''''''''''''''''''''''''''''
List of available content type form controls
''''''''''''''''''''''''''''''''''''''''''''

.. include:: /includes/form-controls/list-form-controls.rst

'''''''''''''''''''''''''''''''''''''''''''
List of available content type data sources
'''''''''''''''''''''''''''''''''''''''''''

.. include:: /includes/form-sources/list-form-sources.rst

|hr|

.. _dependency-resolver-configuration:

"""""""""""""""""""""""""""""""""
Dependency Resolver Configuration
"""""""""""""""""""""""""""""""""
Crafter Studio extracts and tracks dependencies between content items to assist authors with publishing, workflow and
core content operations like copy and delete. This file configures what file paths Crafter considers a dependency and
how they should be extracted.

To modify the Dependency Resolver configuration, click on |projectTools| from the bottom of the Sidebar, then click on **Configuration** and select **Dependency Resolver** from the list.

.. image:: /_static/images/site-admin/config-open-dependency-config.webp
    :alt: Configurations - Open Dependency Resolver Configuration
    :width: 45%
    :align: center

~~~~~~
Sample
~~~~~~
Here's a sample Dependency Resolver Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample dependency resolver configuration</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-resolver-config.xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/dependency/resolver-config.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|
|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Soft Dependencies Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. version_tag::
    :label: Since
    :version: 4.2.0

Soft dependencies are referenced items that are in a modified state and are optional. When calculating soft
dependencies, CrafterCMS follows dependencies recursively. To set the depth of soft dependencies calculated, configure
the maximum recursion iterations property ``studio.db.maxRecursiveIterations`` with a value between 0 and 20.
The default value is 10.

.. code-block:: yaml
    :caption: *bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

    # DB max_recursive_iterations value. This property should be set to a value between 0 and 20 (hard limit)
    studio.db.maxRecursiveIterations: 10


|hr|

.. _project-tools-configuration:

"""""""""""""""""""""""""""
Project Tools Configuration
"""""""""""""""""""""""""""
Studio's Project Tools can be configured to list/de-list configuration files.
The ``Configurations`` configuration file allows you to specify which items can be accessed from the list in
**Project Tools** -> **Configuration**.

To find this configuration XML through Studio follow the next instructions:

#. Click on |projectTools| located in the Sidebar.
#. Choose **Configuration** from the menu.
#. Select **Configurations**.

.. image:: /_static/images/site-admin/configuration.webp
    :alt: Configurations - Open Configurations
    :width: 45%
    :align: center

|

~~~~~~
Sample
~~~~~~
Here's a sample ``config-list.xml`` file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample "config-list.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-config-list.xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/administration/config-list.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|
|

~~~~~~~~~~~
Description
~~~~~~~~~~~
List of available configuration tags

+-----------------+-------------------------------------------------------------------------------+
|| Tag            || Description                                                                  |
+=================+===============================================================================+
|| files          || This tag contains each  file.                                                |
+-----------------+-------------------------------------------------------------------------------+
|| file           || This tag contains the configuration of each file.                            |
+-----------------+-------------------------------------------------------------------------------+
|| module         || CrafterCMS module                                                            |
+-----------------+-------------------------------------------------------------------------------+
|| path           || Path where the system will find the specific xml file                        |
+-----------------+-------------------------------------------------------------------------------+
|| title          || This tag refers to file title. It will be shown in the configuration         |
||                || list on the left side of the page. See #1 in the image above                 |
+-----------------+-------------------------------------------------------------------------------+
|| description    || This tag refers to file description. It will be shown to explain the file    |
||                || functionality. See #2 in the image above                                     |
+-----------------+-------------------------------------------------------------------------------+
|| samplePath     || Path where the system will find an example of the specific xml.              |
+-----------------+-------------------------------------------------------------------------------+

~~~~~~~~~~~
Sample File
~~~~~~~~~~~
You can click on the **View Sample** button to see a configuration file example.

.. image:: /_static/images/site-admin/basic-configuration-sample.webp
    :align: center
    :alt: Basic Configuration Sample

|hr|

.. _asset-processing-configuration:

""""""""""""""""""""""""""""""
Asset Processing Configuration
""""""""""""""""""""""""""""""
Asset processing allows you to define transformations for static assets (currently only images), through a series of processor pipelines that are executed when the assets are uploaded to Studio.

To modify the Asset Processing configuration, click on |projectTools| from the bottom of the Sidebar, then click on **Configuration** and select **Asset Processing** from the dropdown list.

.. image:: /_static/images/site-admin/config-open-asset-proc-config.webp
    :alt: Configurations - Open Asset Processing Configuration
    :width: 65 %
    :align: center

~~~~~~
Sample
~~~~~~
Here's a sample Asset Processing Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample "asset-processing-config.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-asset-processing-config.xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/asset-processing/asset-processing-config.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|
|

For more details on asset processing, see :ref:`asset-processing`


|hr|

.. _aws-profile-configuration:

""""""""""""""""""""""""""
AWS Profiles Configuration
""""""""""""""""""""""""""
The AWS Profiles configuration file allows you to configure 0 or more AWS profiles with the information required by AWS services.
To modify the AWS Profiles configuration, click on |projectTools| from the bottom of the *Sidebar*, then click on **Configuration** and select **AWS Profiles** from the dropdown list.

.. image:: /_static/images/site-admin/config-open-aws-config.webp
    :alt: Configurations - Open AWS Profiles Configuration
    :width: 65 %
    :align: center

~~~~~~
Sample
~~~~~~
Here's a sample AWS Profiles Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample "aws.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-aws.xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/aws/aws.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|
|


For more information on Amazon S3, please see: https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html

For more information on the AWS elastic transcoder, please see: https://docs.aws.amazon.com/elastictranscoder/latest/developerguide/introduction.html

For more information on the AWS mediaconvert, please see: https://docs.aws.amazon.com/mediaconvert/latest/ug/what-is.html

|hr|

.. _box-profile-configuration:

""""""""""""""""""""""""""
Box Profiles Configuration
""""""""""""""""""""""""""
.. version_tag::
	:label: Until
	:version: 4.2

CrafterCMS integrates with Box. The Box Profiles configuration file allows you to configure Box profiles with the
information required by Box services. To modify the Box Profiles configuration, click on |projectTools| from the bottom
of the *Sidebar*, then click on **Configuration** and select **Box Profiles** from the list.

.. image:: /_static/images/site-admin/config-open-box-config.webp
    :alt: Configurations - Open Box Profiles Configuration
    :width: 45%
    :align: center

~~~~~~
Sample
~~~~~~
Here's a sample Box Profiles Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample "box.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.1.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-box.xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/box/box.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|
|

~~~~~~~~~~~~~~~~~
Box Configuration
~~~~~~~~~~~~~~~~~
To obtain the clientId, clientSecret, enterpriseId, publicKeyId, privateKey and privateKeyPassword
you need to use a Box Developer Account to create a new App and configure it to use OAuth 2.0 with
JWT.

For more details you can follow the `official documentation <https://developer.box.com/docs/authentication-with-jwt>`_.

.. note::
  If you are using a JRE older than ``1.8.0_151`` you need to install the JCE Unlimited Strength
  Jurisdiction Policy Files. For newer versions you only need to enable the unlimited strength setting.

For more information on how to manage/encode your secrets such as your Box credentials, please see :ref:`managing-secrets`

'''''''
Example
'''''''
For an example of configuring Studio to use Box, see :ref:`box-asset-access`

|hr|

.. _webdav-profiles-configuration:

"""""""""""""""""""""""""""""
WebDAV Profiles Configuration
"""""""""""""""""""""""""""""
CrafterCMS integrates with WebDAV. The WebDAV Profiles configuration file allows you to configure profiles with the
information required to connect to a WebDAV server. To modify the WebDAV Profiles configuration, click on |projectTools|
from the bottom of the *Sidebar*, then click on **Configuration** and select **WebDAV Profiles** from the dropdown list.

.. image:: /_static/images/site-admin/config-open-webdav-config.webp
    :alt: Configurations - Open WebDAV Profiles Configuration
    :width: 45%
    :align: center

~~~~~~
Sample
~~~~~~
Here's a sample WebDAV Profiles Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample WebDAV profiles configuration</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-webdav.xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/webdav/webdav.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|
|

.. note:: Preemptive authentication may be needed if network timeouts are happening during uploads. To enable preemptive authentication, simply set the option ``preemptiveAuth`` to ``true`` in the configuration file.

|hr|

.. _studio-multi-environment-support:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Studio Multi-environment Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To set up a Studio environment, do the following:

#. Create a folder under ``CRAFTER_HOME/data/repos/sites/${site}/sandbox/config/studio`` called ``env``
#. Inside the folder, create a directory called ``myenv`` (or whatever you want to call the environment)
#. Copy the configuration file you want to override in the new environment you are setting up, inside your ``myenv`` folder
   following the folder structure under ``config/studio``.
#. Remember to commit the files copied so Studio will pick it up.
#. In the ``crafter-setenv.sh`` file in ``TOMCAT/bin`` set the
   following property to desired environment:

      .. code-block:: bash
         :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*

         # -------------------- Configuration variables --------------------
         export CRAFTER_ENVIRONMENT=${CRAFTER_ENVIRONMENT:=myenv}

      |

#. Restart Studio

.. note::
    All configuration files under ``CRAFTER_HOME/data/repos/sites/${site}/sandbox/config/studio`` can be overridden by environment, except for the Project Policy Configuration (site-policy-config.xml) and Content Types (items under the ``content-types`` folder).

"""""""
Example
"""""""
Let's take a look at an example of creating a new environment, called ``mycustomenv`` with the ``rte-setup-tinymce5.xml`` file overridden in the new environment:

#. We'll create a folder called ``env`` under ``CRAFTER_HOME/data/repos/site/my-awesome-editorial/sandbox/config/studio``

   .. code-block:: text
       :linenos:
       :emphasize-lines: 12

       data/
         repos/
           sites/
             my-awesome-editorial/
               sandbox/
                 config/
                   studio/
                     administration/
                     content-types/
                     data-sources/
                     dependency/
                     env/
                     permission-mappings-config.xml
                     role-mappings-config.xml
                     site-config.xml
                     studio_version.xml
                     translation-config.xml
                     ui.xml
                     workflow/

   |

#. Inside the ``env`` folder, create a directory called ``mycustomenv``
#. We will now copy the configuration file for the ``ui.xml`` that we want to override in the new environment we are setting up, inside our ``mycustomenv`` folder, following the folder structure under ``config/studio``. For our example, the ``ui.xml`` file is under ``config/studio/``:

   .. code-block:: text
       :emphasize-lines: 3

       env/
         mycustomenv/
           ui.xml

   |

#. Remember to commit the files copied so Studio will pick it up.

   .. code-block:: bash

       ➜  sandbox git:(master) ✗ git add .
       ➜  sandbox git:(master) ✗ git commit -m "Add updated ui.xml file for mycustomenv"

   |

#. Open the ``crafter-setenv.sh`` file in ``TOMCAT/bin`` and set the value of ``CRAFTER_ENVIRONMENT`` to the
   environment we setup above to make it the active environment:

   .. code-block:: bash
       :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*

       # -------------------- Configuration variables --------------------
       export CRAFTER_ENVIRONMENT=${CRAFTER_ENVIRONMENT:=mycustomenv}

   |

#. Restart Studio. To verify our newly setup environment, open the ``Sidebar`` and click on |projectTools|, then select ``Configuration``. Notice that the active environment ``mycustomenv`` will be displayed on top of the configurations list:

   .. image:: /_static/images/site-admin/env-custom-configurations.webp
      :align: center
      :alt: Active Environment Displayed in Project Config Configuration

.. _studio-access-and-permissions:

^^^^^^^^^^^^^^^^^^^^^^
Access and Permissions
^^^^^^^^^^^^^^^^^^^^^^

To configure access to Crafter Studio beyond adding groups and users, you'll need to configure the system-wide configuration files:

.. _global-role-mappings-config:

"""""""""""""""""""""""""""
Global Role Mappings Config
"""""""""""""""""""""""""""
The global role mappings configuration file maps user :ref:`groups <groups-management>` to one or more roles and serves
as the base for every project in your CrafterCMS installation. All role mappings configured here are in addition to what
is configured in the project role mappings.

Role mappings per project are managed within Crafter Studio's UI. See :ref:`project-role-mappings` for more information.

To access the global role mappings config file, using your favorite editor, navigate to ``CRAFTER_HOME/data/repos/global/configuration/``
then open the file ``global-role-mappings-config.xml``. Remember to restart Crafter so your changes to the file will take effect.

Here's the default global role mappings configuration (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample "global-role-mappings-config.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/global-role-mappings-config.xml
       :language: xml
       :caption: *CRAFTER_HOME/data/repos/global/configuration/global-role-mappings-config.xml*
       :linenos:


.. raw:: html

   </details>

|

~~~~~~~~~~~~~~~~~~~
Default Global Role
~~~~~~~~~~~~~~~~~~~
CrafterCMS comes with a predefined global role ``system_admin`` out of the box.

Users with the ``system_admin`` role have access to everything in the CMS such as all the modules in the Main Menu for managing users, groups, etc., all the sites and configuration files, creating/editing layouts, templates, taxonomies, content types, scripts, etc. in addition to creating and editing content, as well as the ability to approve and reject workflow.

See :ref:`global-permission-mappings-config` for more information on all items accessible for the ``system_admin`` role.

|

|hr|

.. _global-permission-mappings-config:

"""""""""""""""""""""""""""""""""
Global Permission Mappings Config
"""""""""""""""""""""""""""""""""
The global permission mappings configuration file lets you configure the permissions to a role globally for the entire application.

Permissions per project are managed within Crafter Studio's UI. See :ref:`permission-mappings` for more information on project permissions.

Here's the default global permissions configuration (click on the triangle on the left to expand/collapse). It contains the permissions mappings for the roles defined in the :ref:`global role mappings configuration <global-role-mappings-config>` file. To access the file, using your favorite editor, navigate to ``CRAFTER_HOME/data/repos/global/configuration/`` then open the file ``global-permission-mappings-config.xml``. Remember to restart CrafterCMS so your changes to the file will take effect.

.. raw:: html

   <details>
   <summary><a>Sample "global-permission-mappings-config.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/global-permission-mappings-config.xml
       :language: xml
       :caption: *CRAFTER_HOME/data/repos/global/configuration/global-permission-mappings-config.xml*
       :linenos:


.. raw:: html

   </details>

|

~~~~~~~~~~~~~~~~~~~~~~~
Permission Descriptions
~~~~~~~~~~~~~~~~~~~~~~~
.. include:: /includes/available-permissions-system-scope.rst

.. _global-menu-config:

^^^^^^^^^^^^^^^^^^
Global Menu Config
^^^^^^^^^^^^^^^^^^
The Global Menu Config configuration file defines what modules are available for administration use when clicking on the ``Navigation Menu`` from the top bar.

To see the default modules available from the ``Navigation Menu``, see :ref:`navigating-main-menu`

Here is the default Global Menu Config configuration file (click on the triangle on the left to expand/collapse).
To access the file, using your favorite editor, navigate to ``CRAFTER_HOME/data/repos/global/configuration/``and then
open the file ``global-menu-config.xml``. Remember to restart Crafter so your changes to the file will take effect.

.. raw:: html

   <details>
   <summary><a>Sample "global-menu-config.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/global-menu-config.xml
   :caption: *CRAFTER_HOME/data/repos/global/configuration/global-menu-config.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|

|hr|

.. _studio-security:

--------
Security
--------
^^^^^^^^^^^^^^
Authentication
^^^^^^^^^^^^^^
Users are authenticated by Studio through the internal database by default. CrafterCMS can be configured so that users are authenticated using an external authentication protocol such as Lightweight Directory Access Protocol (LDAP), Security Assertion Markup Language (SAML), or integrate with any Single-Sign-On (SSO) solution that can provide headers to Studio to indicate successful authentication.

Here's a list of security providers supported by CrafterCMS for accessing the repository:

- :ref:`Studio SAML security <crafter-studio-configure-studio-saml>` |enterpriseOnly|
- :ref:`Header-Based (use when authenticating via headers) <crafter-studio-configure-header-based-auth>` |enterpriseOnly|
- :ref:`LDAP (users are imported from LDAP into the database) <crafter-studio-configure-ldap>` |enterpriseOnly|
- Internal database (users are stored in database)

To configure an external authentication method, please follow one of the guides below:

When using an external authentication method, user accounts are automatically created in the internal database upon each user's first successful login, using the attributes from the responses received. Users added to the internal database after the user's first successful login through external authentication are marked as **Externally Managed**.

.. _configure-authentication-chain:

""""""""""""""""""""""""""""""
Configure Authentication Chain
""""""""""""""""""""""""""""""
CrafterCMS supports multiple security providers and allows configuration of multiple authentication providers in a chain that are then iterated through until either the user is authenticated and granted access or authentication fails and an HTTP 401 Unauthorized is returned to the user. This allows Studio to support multiple security providers that appears like a single authentication module to users.

The following authentication providers can be configured in a chain:

    - Headers
    - LDAP
    - Internal database

.. note:: SAML2 authentication cannot be configured in a chain. SAML2 authentication is a standalone authentication provider.

When an authentication chain is configured when a user logs in, Studio will try to authenticate the user using the first security provider in the chain as defined in the ``studio-config-override.yaml`` file. If authentication fails, it will then move on to the next authentication provider in the list and try to authenticate the user again. It will continue moving on to the next security provider in the chain until the user is authenticated or the authentication fails.

To set up the authentication chain, open the file ``studio-config-override.yaml`` under ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension``. Another way to access the ``studio-config-override.yaml`` file is by clicking on the |mainMenu| **Navigation Menu** from the context nav in Studio, then clicking on ``Global Config``.

Below is a sample configuration for the authentication chain. There are four authentication providers in the example below: (1) Headers Authentication, (2) LDAP1, (3) LDAP2 (4) Internal Database.

.. code-block:: yaml
    :linenos:

    # Studio authentication chain configuration
    studio.authentication.chain:
    # Authentication provider type
    - provider: HEADERS
    # Authentication via headers enabled
      enabled: true
      # Authentication header for secure key
      secureKeyHeader: secure_key
      # Authentication headers secure key that is expected to match secure key value from headers
      # Typically this is placed in the header by the authentication agent
      secureKeyHeaderValue: secure
      # Authentication header for username
      usernameHeader: username
      # Authentication header for first name
      firstNameHeader: firstname
      # Authentication header for last name
      lastNameHeader: lastname
      # Authentication header for email
      emailHeader: email
      # Authentication header for groups: comma separated list of groups
      #   Example:
      #   site_author,site_xyz_developer
      groupsHeader: groups
      # Enable/disable logout for headers authenticated users (SSO)
      # logoutEnabled: false
      # If logout is enabled for headers authenticated users (SSO), set the endpoint of the SP or IdP logout, which should
      # be called after local logout. The {baseUrl} macro is provided so that the browser is redirected back to Studio
      # after logout (https://STUDIO_SERVER:STUDIO_PORT/studio)
      # logoutUrl: /YOUR_DOMAIN/logout?ReturnTo={baseUrl}
    # Authentication provider type
    - provider: LDAP
      # Authentication via LDAP enabled
      enabled: false
      # LDAP Server url
      ldapUrl: ldap://localhost:389
      # LDAP bind DN (user)
      ldapUsername: cn=Manager,dc=my-domain,dc=com
      # LDAP bind password
      ldapPassword: secret
      # LDAP base context (directory root)
      ldapBaseContext: dc=my-domain,dc=com
      # LDAP username attribute
      usernameLdapAttribute: uid
      # LDAP first name attribute
      firstNameLdapAttribute: cn
      # LDAP last name attribute
      lastNameLdapAttribute: sn
      # Authentication header for email
      emailLdapAttribute: mail
      # LDAP groups attribute
      groupNameLdapAttribute: crafterGroup
      # LDAP groups attribute name regex
      groupNameLdapAttributeRegex: .*
      # LDAP groups attribute match index
      groupNameLdapAttributeMatchIndex: 0
    # Authentication provider type
    - provider: LDAP
      # Authentication via LDAP enabled
      enabled: false
      # LDAP Server url
      ldapUrl: ldap://localhost:390
      # LDAP bind DN (user)
      ldapUsername: cn=Manager,dc=my-domain,dc=com
      # LDAP bind password
      ldapPassword: secret
      # LDAP base context (directory root)
      ldapBaseContext: dc=my-domain,dc=com
      # LDAP username attribute
      usernameLdapAttribute: uid
      # LDAP first name attribute
      firstNameLdapAttribute: cn
      # LDAP last name attribute
      lastNameLdapAttribute: sn
      # Authentication header for email
      emailLdapAttribute: mail
      # LDAP groups attribute
      groupNameLdapAttribute: crafterGroup
      # LDAP groups attribute name regex
      groupNameLdapAttributeRegex: .*
      # LDAP groups attribute match index
      groupNameLdapAttributeMatchIndex: 0
    # Authentication provider type
    - provider: DB
      # Authentication via DB enabled
      enabled: true

|

In the configuration above, when a user tries to authenticate, the user's credentials will be passed first to the headers authentication provider. If the authentication succeeds, the processing in the chain is done and the user is allowed to proceed. If the authentication fails, the user credentials will then be passed to LDAP1. If authentication is successful, processing in the chain is done, otherwise, the user credentials are then passed on to LDAP2. LDAP2 will then try to authenticate user. If successful, processing in the chain is done, otherwise, the user credentials are then passed to the final provider in the chain, the internal database. The final provider in the chain then determines whether the user is successfully authenticated or rejected and sent an HTTP 401 Unauthorized message. Below is a diagram showing the authentication chain process using the above configuration:

.. image:: /_static/images/system-admin/auth-chain-example.webp
    :alt: Static Assets - Example Authentication Chain Process
    :width: 55%
    :align: center

.. _crafter-studio-configure-studio-saml:

"""""""""""""""""""""""""""""""""""""""""""
Studio SAML2 Configuration |enterpriseOnly|
"""""""""""""""""""""""""""""""""""""""""""
.. version_tag::
	:label: Since
	:version: 4.0.3

Crafter Studio can be configured to support SAML2 SSO out of the box without using any additional plugins.

.. important::
   *This document only applies to* **CrafterCMS version 4.0.3 and later** |br|
   *Please see* :ref:`here <crafter-studio-configure-studio-saml-up-to-4-0-2>` *for version 4.0.2 and earlier.*

~~~~~~~~~~~~
Requirements
~~~~~~~~~~~~
#. A SAML2-compatible Identity Provider (IdP) properly configured; this configuration will not be covered here
#. A private key and certificate. This can be generated like so:

    ``openssl req -newkey rsa:2048 -nodes -keyout rp-private.key -x509 -days 365 -out rp-certificate.crt``

    Take note of the values of the following options used to generate your key and certificate that will be used later for configuring Studio:

    * **keyout**: The value used for this option wil be used in the ``studio.security.saml.rp.privateKey.location`` property
    * **out**: The value used for this option will be used in the ``studio.security.saml.rp.certificate.location`` property

.. note::
   ``IdP`` is the asserting party and ``SP`` is the relying party (Studio)

~~~~~~~~~
Configure
~~~~~~~~~
To configure Studio SAML2, in your Authoring installation, we need to enable SAML security then we'll setup the required SAML configuration properties.

To enable SAML security, go to ``CRAFTER_HOME/bin``, open the ``crafter-setenv.sh`` file, and uncomment the line ``export SPRING_PROFILES_ACTIVE=crafter_studio_samlSecurity``:

.. code-block:: sh
   :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*

   # -------------------- Spring Profiles --------------------
   ...
   # Uncomment to enable Crafter Studio SAML2 security
   export SPRING_PROFILES_ACTIVE=crafter_studio_samlSecurity
   # For multiple active spring profiles, create comma separated list

|

Next, we'll set up SAML configuration properties. Go to ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension`` and add/uncomment the following lines to ``studio-config-override.yaml`` (of course, make any appropriate configuration changes according to your system):

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ###############################################################
   ##               SAML Security                               ##
   ###############################################################
   # SAML attribute name for email
   # studio.security.saml.attributeName.email: email
   # SAML attribute name for first name
   # studio.security.saml.attributeName.firstName: givenName
   # SAML attribute name for last name
   # studio.security.saml.attributeName.lastName: surname
   # SAML attribute name for group
   # studio.security.saml.attributeName.group: Role
   ###############################################################
   ##         SAML Security Relying Party (SP) configuration    ##
   ###############################################################
   # {baseUrl} and {registrationId} are pre-defined macros and should not be modified
   # SAML relying party (SP) registration ID. {registrationId} macro will be replaced with this value
   # studio.security.saml.rp.registration.id: SSO
   # SAML relying party (SP) entity ID
   # studio.security.saml.rp.entity.id: "{baseUrl}/saml/metadata"
   # SAML relying party (SP) login processing url. Must end with {registrationId}
   # studio.security.saml.rp.loginProcessingUrl: "/saml/{registrationId}"
   # SAML relying party (SP) assertion consumer service location. Must end with {registrationId}
   # studio.security.saml.rp.assertion.consumer.service.location: "{baseUrl}/saml/{registrationId}"
   # SAML relying party (SP) assertion consumer service biding (POST or REDIRECT)
   # studio.security.saml.rp.assertion.consumer.service.binding: POST
   # SAML logout URL without prefix /studio
   # studio.security.saml.rp.logoutUrl: /saml/logout
   # SAML relying party (SP) single logout service location
   # studio.security.saml.rp.logout.service.location: "{baseUrl}/saml/logout"
   # SAML relying party (SP) logout service binding (POST or REDIRECT)
   # studio.security.saml.rp.logout.service.binding: POST
   # SAML relying party (SP) metadata endpoint
   # studio.security.saml.rp.metadata.endpoint: /saml/metadata
   # SAML relying party (SP) private key location
   # studio.security.saml.rp.privateKey.location: classpath:crafter/studio/extension/saml/rp-private.key
   # SAML relying party (SP) certificate location
   # studio.security.saml.rp.certificate.location: classpath:crafter/studio/extension/saml/rp-certificate.crt
   ###############################################################
   ##      SAML Security Asserting Party (IdP) configuration    ##
   ###############################################################
   # SAML asserting party (IdP) entity ID:
   # studio.security.saml.ap.entityId: https://ap.example.org/ap-entity-id
   # SAML asserting party (IdP) single sign on service location
   # studio.security.saml.ap.single.signOn.service.location: https://ap.example.org/sso/saml
   # SAML asserting party (IdP) single sign on service binding (POST or REDIRECT)
   # studio.security.saml.ap.single.signOn.service.binding: POST
   # SAML asserting party (IdP) logout service location
   # studio.security.saml.ap.single.logout.service.location: https://ap.example.org/slo/saml
   # SAML asserting party (IdP) logout service binding (POST or REDIRECT)
   # studio.security.saml.ap.single.logout.service.binding: POST
   # SAML asserting party (IdP) want authn request signed
   # studio.security.saml.ap.want.authn.request.signed: false
   # SAML asserting party (IdP) certificate location
   # studio.security.saml.ap.certificate.location: classpath:crafter/studio/extension/saml/idp-certificate.crt
   ###############################################################
   ##            SAML Security other configuration              ##
   ###############################################################
   # SAML Web SSO profile options: authenticate the user silently
   # studio.security.saml.webSSOProfileOptions.passive: false
   # SAML Web SSO profile options: force user to re-authenticate
   # studio.security.saml.webSSOProfileOptions.forceAuthn: false

|

where

- ``studio.security.saml.enabled``: Indicates if SAML2 is enabled or not
- The following are attributes that Studio expects from the Identity Provider:

  - ``studio.security.saml.attributeName.email``
  - ``studio.security.saml.attributeName.firstName``
  - ``studio.security.saml.attributeName.lastName``
  - ``studio.security.saml.attributeName.group``

- ``studio.security.saml.rp.privateKey.location``: The path of the relying party (SP) private key in the classpath
- ``studio.security.saml.rp.certificate.location``: The path of the relying party (SP) certificate in the classpath
- ``studio.security.saml.ap.entityId``: The asserting party (IdP) entity ID
- ``studio.security.saml.ap.single.signOn.service.location``: The asserting party (IdP) single sign on URL
- ``studio.security.saml.ap.single.logout.service.location``: The asserting party (IdP) single logout URL
- ``studio.security.saml.ap.certificate.location``:  The path of the asserting party (IdP) certificate in the classpath
- ``studio.security.saml.webSSOProfileOptions.passive``: Indicates if user is authenticated silently
- ``studio.security.saml.webSSOProfileOptions.forceAuthn``: Indicates if user will be forced to re-authenticate

The classpath is located in your Authoring installation, under ``CRAFTER_HOME/bin/apache-tomcat/shared/classes``. As shown in the example above, the relying party private key is located in your Authoring installation under ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/saml`` folder.

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

   # SAML relying party (SP) private key location
   studio.security.saml.rp.privateKey.location: classpath:crafter/studio/extension/saml/rp-private.key

|

Restart your Authoring installation after configuring the above.

|hr|

.. _crafter-studio-configure-header-based-auth:

""""""""""""""""""""""""""""""""""""""""""""""""""""""
Configure Header-Based Authentication |enterpriseOnly|
""""""""""""""""""""""""""""""""""""""""""""""""""""""
Crafter Studio can integrate with any authentication system that sends custom HTTP headers containing information that will be used to authenticate the user in Studio. This section details how to set up Studio for header-based authentication.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Configure Studio for Header-Based Authentication
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Configuring Studio for header-based authentication is very simple: in your Authoring installation, go to ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension`` and add the following lines to ``studio-config-override.yaml`` (of course, make any appropriate configuration changes according to your system):

.. code-block:: properties
    :linenos:
    :emphasize-lines: 6,8,13,15,17,19,23,25

    # Studio authentication chain configuration
    # studio.authentication.chain:
      # Authentication provider type
      # - provider: HEADERS
        # Authentication via headers enabled
        # enabled: true
        # Authentication header for secure key
        # secureKeyHeader: secure_key
        # Authentication headers secure key that is expected to match secure key value from headers
        # Typically this is placed in the header by the authentication agent
        # secureKeyHeaderValue: secure
        # Authentication header for username
        # usernameHeader: username
        # Authentication header for first name
        # firstNameHeader: firstname
        # Authentication header for last name
        # lastNameHeader: lastname
        # Authentication header for email
        # emailHeader: email
        # Authentication header for groups: comma separated list of sites and groups
        #   Example:
        #   site_author,site_xyz_developer
        # groupsHeader: groups
        # (Optional) All authentication header values are in this JWT header's claims.
        # jwtAuthTokenHeader: x-crafter-oidc-data
        # Enable/disable logout for headers authenticated users (SSO)
        # logoutEnabled: false
        # If logout is enabled for headers authenticated users (SSO), set the endpoint of the SP or IdP logout, which should
        # be called after local logout. The {baseUrl} macro is provided so that the browser is redirected back to Studio
        # after logout (https://STUDIO_SERVER:STUDIO_PORT/studio)
        # logoutUrl: /YOUR_DOMAIN/logout?ReturnTo={baseUrl}

The attribute ``enabled`` enables/disables headers authentication; make sure this is set to **true** for header-based authentication |br|

The ``secure_key`` attribute is a secret shared between the authentication agent and Studio via this header. Note that this ``secure_key`` is
**required** and header-based authentication will not proceed unless the ``secure_key`` sent to Studio matches this configuration.

Upon matching the ``secure_key`` header, Studio will then look for the principal. This can come in one of two formats:

#. A set of loose headers indicate the principal's attributes: ``username``, ``firstname``, ``lastname``, ``email``, and ``groups``; or
#. A JWT-wrapped principal's attributes as specified by ``x-crafter-oidc-data``

Depending on your authentication agent, configure Studio to look for either the loose attributes or JWT.

''''''''''''''''''
Configuring Logout
''''''''''''''''''
The **Sign out** button link is disabled/hidden by default when header-based authentication is enabled.

To enable **Sign out** for users signed in using header-based authentication, change the following lines (as described from the above configuration) in your ``studio-config-override.yaml`` file (of course, make any appropriate configuration changes according to your system):

.. code-block:: yaml

    # Enable/disable logout for headers authenticated users (SSO)
    # logoutEnabled: false
    # If logout is enabled for headers authenticated users (SSO), set the endpoint of the SP or IdP logout, which should
    # be called after local logout. The {baseUrl} macro is provided so that the browser is redirected back to Studio
    # after logout (https://STUDIO_SERVER:STUDIO_PORT/studio)
    # logoutUrl: /YOUR_DOMAIN/logout?ReturnTo={baseUrl}

|

|hr|

.. _crafter-studio-configure-ldap:

""""""""""""""""""""""""""""""""""""""""""""""
Configure LDAP Authentication |enterpriseOnly|
""""""""""""""""""""""""""""""""""""""""""""""
To configure LDAP authentication, in your Authoring installation, go to ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension`` and uncomment the
following lines to the ``studio-config-override.yaml`` file.

.. note:: The values for the parameters listed below are just examples. Remember to make any appropriate configuration changes according to your directory service in use.

.. code-block:: properties
    :linenos:
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

    # Studio authentication chain configuration
    studio.authentication.chain:
      # Authentication provider type
      - provider: LDAP
        # Authentication via LDAP enabled
        enabled: true
        # LDAP Server url
        ldapUrl: ldap://localhost:389
        # LDAP bind DN (user)
        ldapUsername: cn=Manager,dc=my-domain,dc=com
        # LDAP bind password
        ldapPassword: secret
        # LDAP base context (directory root)
        ldapBaseContext: dc=my-domain,dc=com
        # LDAP username attribute
        usernameLdapAttribute: uid
        # LDAP first name attribute
        firstNameLdapAttribute: cn
        # LDAP last name attribute
        lastNameLdapAttribute: sn
        # LDAP email attribute
        emailLdapAttribute: mail
        # LDAP groups attribute
        groupNameLdapAttribute: crafterGroup
        # LDAP groups attribute name regex
        groupNameLdapAttributeRegex: .*
        # LDAP groups attribute match index
        groupNameLdapAttributeMatchIndex: 0

|

Some notes on the properties above:

- ``enabled`` enables/disables LDAP authentication, make sure this is set to **true** for LDAP authentication
- ``serverUrl`` is just the URL where the LDAP server is listening for requests.
- ``bindDN`` and ``bindPassword`` are basically the credentials used to connect initially to the LDAP server.
- ``baseContext`` is the LDAP tree root where the user entries can be located.
- ``username``, ``firstName``, ``lastName`` and ``email`` are basic user attributes.
- ``groupName`` indicates the groups the user belongs to (can have multiple values). You can specify a regex to extract the group name of a user.

Studio will then do a query against the LDAP server whenever a user attempts to log in and the user is not yet in the DB. If there's a match in LDAP, the user is
created in the database with the imported LDAP attributes, and finally added to the groups specified in LDAP.

Also, please note that Studio needs all the attributes listed in the config to be present in the LDAP user's attributes, otherwise, Studio is not able to authenticate the user. When an attribute is missing, an error message will be displayed in the login screen: ``A system error has occurred. Please wait a few minutes or contact an administrator``. Please look at the tomcat log to check which attribute was not found. Here's an example log:

.. code-block:: none

    [WARN] 2017-10-11 12:42:57,487 [http-nio-8080-exec-2] [security.DbWithLdapExtensionSecurityProvider] | No LDAP attribute crafterGroup found for username jbloggs

|

Here are a few things to take note of when configuring LDAP authentication in Studio:

Make sure that at least one of the **groupName** attribute of the LDAP user exists in Studio and has Roles and Permission setup. If there is no **groupName** attribute setup in Studio with Roles and Permissions, please make sure that the system administrator assigns a role to at least one group in Studio so the user can access the site, otherwise, once the user gets into the **Sites** screen and tries to Preview the site or view the dashboard, the user will get a notification that the site is invalid.

    .. image:: /_static/images/system-admin/ldap-user-group-no-role-assigned.webp
        :alt: System Admin LDAP Config - LDAP user group attribute not assigned to a role
        :width: 25%
        :align: center

|

To assign a role to a group, please follow the guide :ref:`project-role-mappings`. To assign permissions to a role, please see :ref:`permission-mappings`

For an example of setting up LDAP, see :ref:`setting-up-simple-ldap-server`

|hr|

^^^^^^^^^^^^^
Authorization
^^^^^^^^^^^^^
.. _project-role-mappings:

"""""""""""""
Role Mappings
"""""""""""""
Users are allowed to perform actions on the items that they have been granted access to based on the permissions granted
to the role they have been assigned to. Note that site members have read permission to the entire project/site
regardless of the role that they are assigned.

The role mappings configuration file maps user groups to one or more roles which then get a set of permissions within a project.

To modify the role mappings, click on |projectTools| from the bottom of the *Sidebar*, then click on **Configuration**
and select **Role Mappings** from the list.

.. image:: /_static/images/site-admin/config-open-role-mappings.webp
    :alt: Configurations - Open Role Mappings
    :width: 40%
    :align: center

To configure the role mappings for groups created in CrafterCMS that need global permissions see :ref:`global-role-mappings-config`

~~~~~~
Sample
~~~~~~
Here's a sample Role Mappings Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample role mappings configuration</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-role-mappings-config.xml
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/config/studio/role-mappings-config.xml*
   :language: xml
   :linenos:

.. raw:: html

   </details>

|

~~~~~~~~~~~
Description
~~~~~~~~~~~
    ``/role-mappings/groups/group@name``
        Name of the user group

    ``/role-mappings/groups/role``
        Name of authoring role that the group will map to

~~~~~~~~~~~~~~~~~~~~~
Default Project Roles
~~~~~~~~~~~~~~~~~~~~~
CrafterCMS comes with predefined roles out of the box for projects.
Here's a list of predefined roles for projects:

* **admin**: Users with the ``admin`` role have access to project configuration files, creating/editing layouts, templates, taxonomies, content types, scripts, etc. in addition to creating and editing content, as well as the ability to approve and reject workflow

* **developer**: Users with the ``developer`` role have access to project configuration files, creating/editing layouts, templates, taxonomies, content types, scripts, etc. in addition to creating and editing content, as well as the ability to approve and reject workflow

* **reviewer**: Users with the ``reviewer`` role have the ability to approve and reject workflow. They also have access to a number of actions in the dashboard that are not available to content contributors (users with the role ``author``), including ``Pending Approval`` and ``Scheduled Publish``. They do not have access to edit content.

* **publisher**: Users with the ``publisher`` role have the ability to approve and reject workflow. They also have access to a number of actions in the dashboard that are not available to content contributors (users with the role ``author``), including ``Pending Approval`` and ``Scheduled Publish``. In addition, they also have access to create, edit, and submit content like the ``author`` role.

* **author**: Users with the role ``author`` have access to create, edit and submit content

See :ref:`permission-mappings` for more information on all items accessible for each role in a project.

|hr|

.. _permission-mappings:

"""""""""""""""""""
Permission Mappings
"""""""""""""""""""
The permission mappings configuration file allows you to assign permissions to folders and objects in a project/site giving specific Roles rights to the object. The permission mappings config file contains the permissions mappings for the roles defined in the role mappings config file. When applying permissions to Roles, rights are granted by adding permissions inside the tag ``<allowed-permissions>``. The absence of permissions means the permission is denied. Rules have a regex expression that governs the scope of the permissions assigned. A list of available permissions that can be granted to Roles is available after the sample configuration file.

Permissions are defined per:
    project/site > role > rule

For example, to grant the role component_author the ability to read/write
components and read-only to everything else:

.. code-block:: xml
      :linenos:

      <role name="component_author">
        <rule regex="/site/website/.*">
          <allowed-permissions>
            <permission>content_read</permission>
          </allowed-permissions>
        </rule>
        <rule regex="/site/components/.*">
          <allowed-permissions>
            <permission>content_read</permission>
            <permission>content_write</permission>
            <permission>content_create</permission>
            <permission>folder_create</permission>
          </allowed-permissions>
        </rule>
        <rule regex="/static-assets/.*">
          <allowed-permissions>
            <permission>content_read</permission>
          </allowed-permissions>
        </rule>
      </role>

|

To modify/view the permission mappings for your project/site in Studio, click on |projectTools| at the bottom of the *Sidebar*, then click on **Configurations** and select **Permissions Mapping** from the list.

.. image:: /_static/images/site-admin/config-open-permission-mappings.webp
    :alt: Configurations - Open Permission Mappings
    :width: 40%
    :align: center

Note that permissions assigned is a union, so a user can perform the action as long as:

- The list of permissions contains the requested ACTION.
- OR the list of permissions contains "*"

To configure the permissions to a role globally for the entire application, see :ref:`global-permission-mappings-config`.

~~~~~~
Sample
~~~~~~
Here's a sample Permission Mappings Configuration file (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample "permission-mappings-config.xml"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftercms/studio/support/4.x/src/main/webapp/repo-bootstrap/global/configuration/samples/sample-permission-mappings-config.xml
       :language: xml
       :linenos:


.. raw:: html

   </details>

|

where:

- ``/permissions/site/role@name``
  Role name
- ``/permissions/site/role/rule@regex``
  Regular expression to filter paths where permission is applied.
- ``/permissions/site/role/rule/allowed-permissions/permission``
  Allowed permission for role and rule (possible values given in the table above)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
System (Global) Scope Permissions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. include:: /includes/available-permissions-system-scope.rst

~~~~~~~~~~~~~~~~~~~~~~~~~
Project Scope Permissions
~~~~~~~~~~~~~~~~~~~~~~~~~
.. include:: /includes/available-permissions-project-scope.rst

~~~~~~~~~~~~~~~~~~~~~~
Path Scope Permissions
~~~~~~~~~~~~~~~~~~~~~~
.. include:: /includes/available-permissions-path-scope.rst

|hr|

^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Other Security Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. _studio-password-config:

""""""""""""""""""""""""""""""""""""""
Configure Studio Password Requirements
""""""""""""""""""""""""""""""""""""""
Password requirements validation allows the admin to setup rules that ensures users create passwords based on an organization’s password security policy.

Crafter Studio uses `zxcvbn <https://github.com/dropbox/zxcvbn>`__ for password strength management.

.. version_tag::
	:label: Since
	:version: 4.0.3

|

The password strength configured here is displayed to the user when resetting a password or creating a user.

.. image:: /_static/images/system-admin/password-requirements.webp
   :alt: System Administrator - Password Requirements Display
   :align: center
   :width: 55%

|

To configure the password strength, click on |mainMenu| **Main Menu** then click on ``Global Config``.
Scroll to the section ``Security`` and change the value of ``studio.security.passwordRequirements.minimumComplexity``
to desired minimum password complexity required:

.. code-block:: yaml
   :linenos:
   :caption: *CRAFTER_HOME/data/repos/global/configuration/studio-config-override.yaml*

   # Password requirements minimum complexity
   # This is based on https://github.com/dropbox/zxcvbn
   # The minimum complexity corresponds to the password score
   # You can try this out here https://lowe.github.io/tryzxcvbn/
   #  score      # Integer from 0-4 (useful for implementing a strength bar)
   #  0 # too guessable: risky password. (guesses < 10^3)
   #  1 # very guessable: protection from throttled online attacks. (guesses < 10^6)
   #  2 # somewhat guessable: protection from unthrottled online attacks. (guesses < 10^8)
   #  3 # safely unguessable: moderate protection from offline slow-hash scenario. (guesses < 10^10)
   #  4 # very unguessable: strong protection from offline slow-hash scenario. (guesses >= 10^10)
   # The default value is 3
   studio.security.passwordRequirements.minimumComplexity: 3

|

Crafter Studio's default minimum password complexity required is set to 3 (which translate to a score
of 80 in the UI), and until the user setting/changing the password has met the minimum required,
the ``Submit`` button will not be enabled. Also, once the minimum password strength score has been
reached, the score will be displayed in green.

.. image:: /_static/images/system-admin/password-reqts-80-score.webp
    :alt: System Administrator - Password Requirements Display Score 80
    :align: center
    :width: 35%

|

Below, are some of the messages displayed as a user is inputting a new password:

.. image:: /_static/images/system-admin/password-reqts-20-score.webp
    :alt: System Administrator - Password Requirements Display Score 20
    :align: center
    :width: 35%

|

.. image:: /_static/images/system-admin/password-reqts-40-score.webp
    :alt: System Administrator - Password Requirements Display Score 40
    :align: center
    :width: 35%

|

.. image:: /_static/images/system-admin/password-reqts-60-score.webp
    :alt: System Administrator - Password Requirements Display Score 60
    :align: center
    :width: 35%

|

.. image:: /_static/images/system-admin/password-reqts-100-score.webp
    :alt: System Administrator - Password Requirements Display Score 100
    :align: center
    :width: 35%

|

|hr|

.. _randomize-admin-password:

"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
Randomize Authoring's "admin" Password for CrafterCMS Fresh Install
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
CrafterCMS gives you the option to randomize the **admin** password on a fresh install. To randomize the **admin** password, before starting CrafterCMS for the very first time, in your Authoring installation, go to  the following folder: ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/`` and add the following to the ``studio-config-override.yaml`` file:

.. code-block:: yaml
       :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
       :linenos:

       ##################################################
       ##                   Security                   ##
       ##################################################
       # Enable random admin password generation
       studio.db.initializer.randomAdminPassword.enabled: false
       # Random admin password length
       studio.db.initializer.randomAdminPassword.length: 16
       # Random admin password allowed chars
       studio.db.initializer.randomAdminPassword.chars: ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*_=+-/

To enable the random admin password generation, just set ``studio.db.initializer.randomAdminPassword.enabled`` to ``true`` and specify your desired password length and allowed characters for the password. Save the file after making your changes.

After saving the ``studio-config-override.yaml`` file, start CrafterCMS. You'll then need to look at the authoring tomcat log, and search for the following string to get the random password generated for user **admin**: `*** Admin Account Password:`

Here's a sample password generated for the admin as listed in the Tomcat log:

    ``INFO: *** Admin Account Password: "WXOIK$O$yGixio2h" ***``

You can now log in as the user **admin** using the randomly generated password listed in the Tomcat log.

|hr|

.. _studio-timeout:

"""""""""""""""
Studio Timeouts
"""""""""""""""
.. _changing-session-timeout:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Changing the Session Timeout
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
CrafterCMS has configurable timeouts for session lifetime and session inactivity.

Session lifetime timeout is the amount of time a session is valid before requiring the user to re-authenticate.

Session inactivity timeout is the amount of time of user inactivity before requiring the user to re-authenticate.

In some cases, some operations in CrafterCMS may last longer than the user session inactivity timeout settings.
For this scenario, the session inactivity timeout will need to be modified to allow the operation to finish
without the session timing out. Also, you may want to change the timeouts from the default settings.

Here's a summary of the session timeouts available in CrafterCMS:

.. list-table::
   :widths: 1 1 8
   :header-rows: 1

   * - Timeout Name
     - Default Value |br|
       *(in minutes)*
     - Description
   * - ``sessionTimeout``
     - 480
     - **Studio session lifetime timeout** |br|
       *Location:* |br|
       *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml* |br| |br|
       The amount of time a session is valid counting from when a user is logged in. |br|
       After this amount of time,a session timeout will be forced in the application layer even if the user is active.
   * - ``inactivityTimeout``
     - 30
     - **Studio session inactivity timeout** |br|
       *Location:* |br|
       *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml* |br| |br|
       The amount of time of user inactivity, tracked by Studio, before requiring the user to re-authenticate. |br|
       Remember to set the ``inactivityTimeout`` value less than the ``session-timeout`` value in the ``web.xml`` file. |br|
       The session inactivity time tracked by Studio is different from the session inactivity time tracked by Tomcat. |br|
       This is because there are some API calls that are not tracked as active by Studio.
   * - ``session-timeout``
     - 30
     - **Tomcat session timeout** |br|
       *Location:* |br|
       *CRAFTER_HOME/bin/apache-tomcat/webapps/studio/WEB-INF/web.xml* |br| |br|
       The amount of time of user inactivity, tracked by Tomcat, before requiring the user to re-authenticate. |br|
       This value must be greater than or equal to ``inactivityTimeout`` since that timeout can and does kick in |br|
       before this one.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Change Session Lifetime Timeout
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To change the session lifetime timeout, in your
``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml``,
change the value for ``studio.security.sessionTimeout`` to desired amount of time the session is valid
in minutes for users.

.. code-block:: properties

   # Time in minutes after which active users will be required to login again
   # studio.security.sessionTimeout: 480

|

Make sure to stop and **restart Studio** after making your changes.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Change Session Inactivity Timeout
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
There are two timeouts you can configure for the session inactivity timeout as described in the above table.

- ``session-timeout`` in the Tomcat ``web.xml`` file
  This is the default Tomcat timeout for handling idle connections (inactive)
- ``inactivityTimeout`` in the Studio override configuration file
  This is the Studio session inactivity timeout

To change the session inactivity timeout, follow the instructions below:

#. In your ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml``,
   change the value for ``studio.security.inactivityTimeout`` to set the amount of time in minutes the amount of
   time a user can be inactive before the user's session times out.

   .. code-block:: properties

      # Time in minutes after which inactive users will be required to login again
      # studio.security.inactivityTimeout: 30

   |

#. In your ``CRAFTER_HOME/bin/apache-tomcat/webapps/studio/WEB-INF/web.xml`` file, change the value in
   between the ``session-timeout`` tags to desired amount of time the session will exist in minutes:

   .. code-block:: xml

      <session-config>
        <session-timeout>30</session-timeout>
        <tracking-mode>COOKIE</tracking-mode>
	  </session-config>

   |


Remember to keep the Studio session inactivity timeout ``inactivityTimeout`` from the ``studio-config-override.yaml`` file less than the Tomcat ``session-timeout`` from the ``CRAFTER_HOME/bin/apache-tomcat/webapps/studio/WEB-INF/web.xml`` file.

Make sure to stop and **restart Studio after making your changes**.

You can also change the Studio session timeouts from the |mainMenu| **Main Menu** in Studio under ``Global Config``

|

|hr|

.. _studio-cipher-configuration:

~~~~~~~~~~~~~~~~~~~~
Cipher Configuration
~~~~~~~~~~~~~~~~~~~~
.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    ##################################################
    ##                   Security                   ##
    ##################################################
    # Time in minutes after which active users will be required to login again
    # studio.security.sessionTimeout: 480
    # Time in minutes after which inactive users will be required to login again
    # studio.security.inactivityTimeout: 30
    #
    # Salt for encrypting
    studio.security.cipher.salt: ${env:CRAFTER_SYSTEM_ENCRYPTION_SALT}
    # Key for encrypting
    studio.security.cipher.key: ${env:CRAFTER_SYSTEM_ENCRYPTION_KEY}

    # The key used for encryption of configuration properties
    studio.security.encryption.key: ${env:CRAFTER_ENCRYPTION_KEY}
    # The salt used for encryption of configuration properties
    studio.security.encryption.salt: ${env:CRAFTER_ENCRYPTION_SALT}

    # The path of the folder used for the SSH configuration
    studio.security.ssh.config: ${env:CRAFTER_SSH_CONFIG}

    # Defines name used for environment specific configuration. It is used for environment overrides in studio. Default value is default.
    studio.configuration.environment.active: ${env:CRAFTER_ENVIRONMENT}

|

|hr|

.. _studio-access-tokens:

"""""""""""""
Access Tokens
"""""""""""""
.. version_tag::
	:label: Since
	:version: 4.0.0

The following section of Studio's configuration overrides allows you to configure settings for the Studio access tokens.
Access tokens can then be used to invoke :base_url:`Crafter Studio's REST APIs <_static/api/studio.html>` from the out
of the box UI as well as any customized JavaScript, CURL commands, or used in :ref:`crafter-cli` to perform operations on Studio.

Studio access tokens uses JWT tokens for authentication. The following environment variables are used to customize the
default behavior of the JWT token that is used.

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    ##################################################
    ##               Access Tokens                  ##
    ##################################################

    # Issuer for the generated access tokens
    studio.security.token.issuer: ${env:STUDIO_TOKEN_ISSUER}
    # List of accepted issuers for validation of access tokens (separated by commas)
    studio.security.token.validIssuers: ${env:STUDIO_TOKEN_VALID_ISSUERS}
    # The audience for generation and validation of access tokens (if empty the instance id will be used)
    studio.security.token.audience: ${env:STUDIO_TOKEN_AUDIENCE}
    # Time in minutes for the expiration of the access tokens
    studio.security.token.timeout: ${env:STUDIO_TOKEN_TIMEOUT}
    # Password for signing the access tokens (needs to be equal or greater than 512 bits in length)
    studio.security.token.password.sign: ${env:STUDIO_TOKEN_SIGN_PASSWORD}
    # Password for encrypting the access tokens
    studio.security.token.password.encrypt: ${env:STUDIO_TOKEN_ENCRYPT_PASSWORD}
    # Name of the cookie to store the refresh token
    studio.security.token.cookie.name: ${env:STUDIO_REFRESH_TOKEN_NAME}
    # Time in seconds for the expiration of the refresh token cookie
    studio.security.token.cookie.maxAge: ${env:STUDIO_REFRESH_TOKEN_MAX_AGE}
    # Indicates if the refresh token cookie should be secure (should be true for production environments behind HTTPS)
    studio.security.token.cookie.secure: ${env:STUDIO_REFRESH_TOKEN_SECURE}

where:

- ``STUDIO_TOKEN_ISSUER``, ``STUDIO_TOKEN_VALID_ISSUERS``, ``STUDIO_TOKEN_AUDIENCE`` |br|
  These variables are used in the JWT claims set. See https://datatracker.ietf.org/doc/html/rfc7519#section-4.1 for more
  information on JWT claims set.

- ``STUDIO_TOKEN_TIMEOUT`` |br|
  This variable sets the expiration of the JWT token in minutes (default is 5 minutes). The expiration value is also
  injected into the JWT claims when a token is published. This cannot be changed after the token is published.
  After the expiration time, the token is invalid and a new token must be published to use for API calls. (This can be
  done automatically with the ``refresh_token`` in the UI)

- ``STUDIO_TOKEN_SIGN_PASSWORD`` |br|
  This variable is used for the Signature part of the JWT token. The signature is used to verify the message wasn't
  changed along the way, and, in the case of tokens signed with a private key, it can also verify that the sender of
  the JWT is who it says it is. For Studio, we use HMAC_SHA512 algorithm for the signature.

- ``STUDIO_TOKEN_ENCRYPT_PASSWORD`` |br|
  This variable is used for encrypting the JWT token itself so that it won't be decrypted without a password.

- ``STUDIO_REFRESH_TOKEN_NAME``, ``STUDIO_REFRESH_TOKEN_MAX_AGE``, ``STUDIO_REFRESH_TOKEN_SECURE`` |br|
  These variables are used for customizing the refresh token cookie. JWT token is short lived in general and we use a
  refresh token to exchange for a new JWT token when the old one is expired. By default the cookie name is ``refresh_token``.
  When creating a new access token, the backend will validate if the refresh token cookie is valid. You should find
  this from the cookies in the browser while logging in with Studio.

For more information on JWT tokens in general, see https://jwt.io/introduction.
For information on creating access tokens in Studio, see :ref:`here <api-token>`.

.. _studio-preview-cookie:

""""""""""""""
Preview Cookie
""""""""""""""
.. version_tag::
	:label: Since
	:version: 4.2.0

The following section of Studio's configuration overrides allows you to configure settings for the Preview cookie.
Studio adds a short-lived encrypted cookie called ``crafterPreview`` with the current preview site. This cookie gets
re-issued along with the JWT auth token (if ``crafterSite`` is already set).

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :linenos:

    ##############################################################
    ##                      Preview Cookie                      ##
    ##############################################################
    # Name of the preview
    studio.security.token.previewCookie.name: crafterPreview
    # Time in seconds for the expiration of the preview cookie
    studio.security.token.previewCookie.maxAge: 600
    # The path used to set the preview cookie
    studio.security.token.previewCookie.path: /
    # The domain used to set the preview cookie (if set to null or empty the domain will be detected from the request)
    studio.security.token.previewCookie.domain: null
    # Indicates if the preview cookie should be secure (should be true for production environments behind HTTPS)
    studio.security.token.previewCookie.secure: false
    # Indicates if the preview cookie should be HTTPOnly
    studio.security.token.previewCookie.httpOnly: true
    Password requirements validation allows the admin to setup rules that ensures users create passwords based on an organization’s password security policy.

The Preview cookie  ``crafterPreview`` is encrypted using the encryption option for configuration files (which are
shared between Studio and Engine) and admins will need to update the default configurations for the encryption key and
salt in :ref:`Studio <studio-cipher-configuration>` and in :ref:`Engine <engine-configuration-properties-encryption>`.

Use the API :base_url:`switchPreviewSite <_static/api/studio.html#tag/users/operation/getCurrentUserSites>` to refresh
the ``crafterPreview`` cookie. This API must be called whenever the ``crafterSite`` cookie value is updated

|hr|

.. _studio-groovy-sandbox-configuration:

""""""""""""""""""""""""""""
Groovy Sandbox Configuration
""""""""""""""""""""""""""""
.. include:: /includes/groovy-sandbox-configuration.rst

~~~~~~~~~~~~~~~~~~~~~~~~~
Groovy Sandbox Properties
~~~~~~~~~~~~~~~~~~~~~~~~~
The following allows you to configure the Groovy sandbox.
The Groovy sandbox is enabled by default and can be disabled by changing the property ``studio.scripting.sandbox.enable`` to ``false``.

.. code-block:: yaml
    :linenos:
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

    # Indicates if the sandbox should be enabled
    studio.scripting.sandbox.enable: true
    # Indicates if the blacklist should be enabled (this will have no effect if the sandbox is disabled)
    studio.scripting.sandbox.blacklist.enable: true
    # The location of the default blacklist to use (this will have no effect if the sandbox is disabled)
    studio.scripting.sandbox.blacklist.path: classpath:crafter/studio/groovy/blacklist
    # Indicates if the whitelist should be enabled (this will have no effect if the sandbox is disabled)
    studio.scripting.sandbox.whitelist.enable: false
    # The location of the default whitelist to use (this will have no effect if the sandbox is disabled)
    studio.scripting.sandbox.whitelist.path: classpath:crafter/studio/groovy/whitelist

|

~~~~~~~~~~~~~~~~~~~~~~~~
Using a Custom Blacklist
~~~~~~~~~~~~~~~~~~~~~~~~
Crafter Studio includes a default blacklist that you can find
`here <https://github.com/craftercms/studio/blob/support/4.x/src/main/resources/crafter/studio/groovy/blacklist>`__.
Make sure you review the branch/tag you're using.

To use a custom blacklist follow these steps:

#. Copy the default blacklist file to your classpath, for example:

    ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/groovy/blacklist``

#. Remove or comment (adding a ``#`` at the beginning of the line) the expressions that your scripts require
#. Update the ``studio-config-override.yaml`` configuration file to load the custom blacklist:

    .. code-block:: yaml
        :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

        # The location of the default blacklist to use (this will have no effect if the sandbox is disabled)
        studio.scripting.sandbox.blacklist.path: classpath:crafter/studio/extension/groovy/blacklist

#. Restart CrafterCMS

Now you can execute the same script without any issues.

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Disabling the Sandbox Blacklist
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
It is possible to disable the blacklist to allow the execution of most expressions, in case you need to use a
considerable number of the expression included in the blacklist while keeping some basic restrictions. To disable
the blacklist for all projects/sites update the ``studio-config-override.yaml`` configuration file:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

    # Indicates if the blacklist should be enabled (this will have no effect if the sandbox is disabled)
    studio.scripting.sandbox.blacklist.enable: false

|

~~~~~~~~~~~~~~~~~~~~~~~~
Using a Custom Whitelist
~~~~~~~~~~~~~~~~~~~~~~~~
.. version_tag::
    :label: Since
    :version: 4.5.0

Crafter Studio includes a default whitelist that you can find `here <https://github.com/craftercms/studio/blob/support/4.x/src/main/resources/crafter/studio/groovy/whitelist>`__.
Make sure you review the branch/tag you're using.

To use a custom whitelist follow these steps:

#. Copy the default whitelist file to your classpath, for example:

    ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/groovy/whitelist``

#. Add, remove or comment (adding a ``#`` at the beginning of the line) the expressions that your scripts require
#. Update the ``studio-config-override.yaml`` configuration file to load the custom whitelist:

    .. code-block:: yaml
        :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

        # The location of the default whitelist to use (this will have no effect if the sandbox is disabled)
        studio.scripting.sandbox.whitelist.path: classpath:crafter/studio/extension/groovy/whitelist

#. Restart CrafterCMS

Now you can execute the same script without any issues.

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Enabling the Sandbox Whitelist
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. version_tag::
    :label: Since
    :version: 4.5.0

It is possible to only allow the execution of approved expressions included in a list to prevent operations that
could compromise the system, by using the sandbox whitelist. To create your custom sandbox whitelist, see above.

The sandbox whitelist is disabled by default. To enable the whitelist for all projects/sites update the
``studio-config-override.yaml`` configuration file:

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*

    # Indicates if the whitelist should be enabled (this will have no effect if the sandbox is disabled)
    studio.scripting.sandbox.whitelist.enable: true

|

~~~~~~~~~~~~~~~~~~~
Grape Configuration
~~~~~~~~~~~~~~~~~~~
.. include:: /includes/groovy-grape-configuration.rst

|

.. _studio-grapes-download:

~~~~~~~~~~~~~~~
Grapes Download
~~~~~~~~~~~~~~~
.. version_tag::
    :label: Since
    :version: 4.5.0

The following allows you to enable or disable automatic Groovy dependency (grapes) downloads for scripts (@grab):

.. code-block:: yaml
    :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
    :emphasize-lines: 3

    # Indicates if @Grab annotations should download grapes
    # If false, only already downloaded grapes will be available
    studio.scripting.grapes.download.enabled: false

Automatic grapes download is disabled by default. Set ``studio.scripting.grapes.download.enabled`` to true to enable
automatic grapes download.

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Installing Grapes from the Command Line
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. include:: /includes/groovy-grape-install.rst

|

~~~~~~~~~~~~~~~
Important Notes
~~~~~~~~~~~~~~~
.. include:: /includes/groovy-sandbox-important-notes.rst

|hr|


""""""""""""""""""""""""""""""
HTTP Security Response Headers
""""""""""""""""""""""""""""""
Properly configured HTTP response headers help prevent security vulnerabilities like cross-site scripting (XSS),
clickjacking, and other common types of attacks.

HTTP security response headers for Crafter Studio are configured in the **studio-config-override.yaml** under
``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml``

See :ref:`studio-policy-headers` and :ref:`studio-cors` for more information on how to configure HTTP security response
headers.


|hr|

.. _studio-admin:

--------------
Administration
--------------

Much of the administration of Crafter Studio can be done via the UI. This section describes how to perform these basic tasks.

.. _navigating-main-menu:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Navigating the Navigation Menu
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In this section, we discuss the Navigation Menu tools available in Studio. To access, click the ``Navigation Menu`` icon from the top right of the browser

.. image:: /_static/images/system-admin/main-menu/open-main-menu.webp
    :alt: System Administrator - Open Navigation Menu
    :align: center
    :width: 100%

|

Here are the list of tools available when using an out of the box blueprint. The ``Navigation Menu`` tools described below are available to users belonging to the ``system_admin`` group.

.. image:: /_static/images/system-admin/main-menu/main-menu.webp
    :alt: System Administrator - Navigation Menu
    :align: center
    :width: 20%

|

The configuration files for the Navigation Menu is located in ``CRAFTER_HOME/data/repos/global/configuration/`` where:

* :ref:`global-menu-config.xml <global-menu-config>` lets you setup the list of tools available from the Navigation Menu sidebar
* :ref:`global-permission-mappings-config.xml <global-permission-mappings-config>` lets you configure the permissions to a role globally for the entire application
* :ref:`global-role-mappings-config.xml <global-role-mappings-config>` lets you configure the mapping between the group and the role

The tools available in the Navigation Menu is configured similar to how the Project Tools Sidebar is configured :ref:`here <project-tools-ui-configuration>` using the :ref:`global menu config <global-menu-config>` configuration file mentioned above.

.. _main-menu-tool-projects:

""""""""
Projects
""""""""
``Projects`` contains a list of all the projects the logged in user has access to. The section :ref:`author-screens` in ``Content Authors`` contains descriptions on some of the actions that can be performed from the Projects screen.  This also allows users with the system admin role to create new projects either from a :ref:`blueprint <your-first-editorial-project>`, a :ref:`remote repository <create-project-with-link-to-remote-repo>` or an :ref:`existing project <duplicate-project>`.

.. image:: /_static/images/system-admin/main-menu/main-menu-sites.webp
    :alt: System Administrator - Navigation Menu Projects
    :align: center
    :width: 85%

|

.. _users-management:

"""""
Users
"""""
A user is anybody who uses CrafterCMS. The ``Users`` management console lets the administrator manage who has access to
Crafter Studio.

For information on managing users and groups, see :ref:`user-group-management`.

~~~~~~~~~~~
Description
~~~~~~~~~~~
The ``Users`` management console allows you to control and set up who can access and manage the sites. All users are listed on
this console.

To find the ``Users`` management console follow the next instructions:

1. Click on the **Navigation Menu** |mainMenu| option located at the top right of the browser, then click on
   **Users** in the sidebar located on the left side of the browser:

   .. image:: /_static/images/users/users-manage-access.webp
       :alt: Users - Manage Access
       :align: center
       :width: 65%

   |

2. Here's the screen that will appear after clicking on **Users**

   .. image:: /_static/images/system-admin/main-menu/main-menu-users.webp
       :alt: Users Dialog
       :align: center
       :width: 65%

   |

~~~~~~~
Actions
~~~~~~~
You can list, search, add or delete users, as well as view specific information.

'''''''''''''
Listing Users
'''''''''''''
To see a list of all existing users, make sure that there are no search terms entered in the search bar. You can also change the number of users listed per page by selecting a different number in the dropdown box at the bottom right of the screen

.. image:: /_static/images/users/users-list-all.webp
    :alt: Users - List All
    :align: center
    :width: 65%

|

'''''''''''''''
Searching Users
'''''''''''''''
You can search for a specific user. To search users, click on the magnifying glass icon on the top right then go
to the search field and type user name, last name, user name or mail.
In the following example we typed "jane", we obtained only one related user: "Jane".

.. image:: /_static/images/users/users-search.webp
    :alt: Users - Search
    :align: center
    :width: 65%

|

.. _creating-a-user:

'''''''''''''''''''
Creating a New User
'''''''''''''''''''
To create a new user, please click on the "Create User" button at the top of the page.

.. image:: /_static/images/users/users-add-new.webp
    :alt: Users - Add New
    :align: center
    :width: 65%

|

A modal dialog will be displayed, please fill out all the fields and finally click on the "**Submit**" button.
If you do not want to create a new user, please click on the "**Cancel**" button.

.. image:: /_static/images/users/users-add.webp
    :alt: Users - Add
    :align: center
    :width: 65%

|

A notification will appear on the screen for a few seconds on successful creation of a new user

.. image:: /_static/images/users/users-create-notification.webp
    :alt: Users - Created Notification
    :align: center

|

.. _editing-a-user:

''''''''''''''''''''''''''''''''''''
Viewing and Editing an Existing User
''''''''''''''''''''''''''''''''''''
To view/edit a specific user, please click on the row of the name you want to edit:

.. image:: /_static/images/users/users-view-btn.webp
    :alt: Users - Click on Name to View Details
    :align: center

|

A modal dialog will be displayed with the user information. To finish viewing, click on the "**X**" (close icon) button.

.. image:: /_static/images/users/users-view.webp
    :alt: Users - View User Info
    :align: center
    :width: 65%

|

Once the dialog is displayed, to edit a specific user, simply click on the field that you want to change.
In the above dialog the **Externally Managed** label is displayed which indicates that the user is externally
managed such as the case in LDAP. Notice that since the user is externally managed, the only change that
can be made for the user is the group membership.

For the user dialog displayed below, since the user is not externally managed, all the fields can be changed
for the user. In this dialog, you can modify the user information such as email, first name, last name and
user name, group membership, reset the user's password and delete the user. You can also activate/de-activate
the user currently being viewed by clicking on the slider labeled **Enabled**. Edit the fields you
want to change and then click on the "**Save**" button. If you do not want to edit the user, please click
on the "Cancel" button.

.. image:: /_static/images/users/users-edit.webp
    :alt: Users - Edit
    :align: center
    :width: 65%

|

'''''''''''''''''''''''''''''''''''''
Resetting an Existing User's Password
'''''''''''''''''''''''''''''''''''''
To reset the password of a specific user, please click on the key icon in the user modal dialog as shown in
the following example.

.. image:: /_static/images/users/users-reset-btn.webp
    :alt: Users - Reset Password Icon
    :width: 65%
    :align: center

|

A modal dialog will be displayed, where the admin can reset the users password. Click on ``Save`` to reset the password.

.. image:: /_static/images/users/users-reset.webp
    :alt: Users - Reset Password
    :align: center
    :width: 55%

|

.. _deleting-a-user:

'''''''''''''''''''''''''
Removing an Existing User
'''''''''''''''''''''''''
To remove a specific user, please click on the trash can icon located in the user modal dialog as shown in
the following example.

.. image:: /_static/images/users/users-remove-btn.webp
    :alt: Users - Remove Icon
    :align: center
    :width: 65%

|

A confirmation pop up will be displayed, please click on "**Yes**" to remove the user and click on "**No**" if you do not want to remove it.

.. image:: /_static/images/users/users-remove.webp
    :alt: Users - Remove
    :align: center
    :width: 50%

|

A notification will appear on the screen for a few seconds on successful deletion of a user

.. image:: /_static/images/users/users-delete-notification.webp
    :alt: Users - Deleted Notification
    :align: center

|

.. important::
   When a user is deleted, the deleted user cannot be re-created. Instead of deleting a user,
   we recommend disabling the user, which prevents them from connecting to the system.

   To disable a user, simply click the ``Enabled`` slider to turn it off and a notification snack
   bar at the bottom will appear informing you that the user has been disabled successfully.

   .. image:: /_static/images/users/user-disabled-notification.webp
      :alt: Users - Deleted Notification
      :width: 25%
      :align: center

   |

.. _groups-management:

""""""
Groups
""""""
A group consists of a collection of users. The ``Groups`` management console lets the administrator manage groups,
members belonging to a group, etc.

For information on managing users and groups, see :ref:`user-group-management`.

~~~~~~~~~~~
Description
~~~~~~~~~~~
The ``Groups`` management console allows you to administrate the groups created on CrafterCMS. You can add, remove,
edit, and manage the users that will belong to the groups and you can also add and remove groups.

Here's a list of predefined groups and roles in CrafterCMS:

+---------------------+------------------------+----------------+
|| Group              || Description           || Role          |
+=====================+========================+================+
|| system_admin       || System administrator  || system_admin  |
+---------------------+------------------------+----------------+
|| site_admin         || Site administrator    || admin         |
+---------------------+------------------------+----------------+
|| site_author        || Site author           || author        |
+---------------------+------------------------+----------------+
|| site_developer     || Site developer        || developer     |
+---------------------+------------------------+----------------+
|| site_reviewer      || Site reviewer         || reviewer      |
+---------------------+------------------------+----------------+
|| site_publisher     || Site publisher        || publisher     |
+---------------------+------------------------+----------------+

You can add more groups defined whenever needed. The list above is just a starting point for when you first
create your project. The following sections will give you more details on users and groups. The next sections,
Permission Mappings and Role Mappings describes how to setup/assign permissions and roles.

To find this section through studio follow the next instructions:

#. Click on ``Navigation Menu`` |mainMenu| at the top right of your browser.
#. Click on **Groups** from the main menu on the left side of your browser.

.. image:: /_static/images/system-admin/main-menu/main-menu-groups.webp
    :width: 70%
    :alt: Groups Management
    :align: center

|

~~~~~~~~~~~~~~~~
Searching Groups
~~~~~~~~~~~~~~~~
You can search for groups by their properties (Display Name, Description), simply enter your search term
into the search bar by clicking on the magnifying glass icon on the top right and it will show results
that match your search term.

.. image:: /_static/images/groups/groups-search.webp
    :width: 60%
    :alt: Groups Management Search
    :align: center

|

.. _create-a-new-group:

~~~~~~~~~~~~~~~~~~
Adding a New Group
~~~~~~~~~~~~~~~~~~
To create a new group, you just need to click on the "**Create Group**" button,

.. image:: /_static/images/groups/groups-new-btn.webp
    :width: 60%
    :alt: Main Menu - Groups New
    :align: center

|

then, a modal dialog will show up with the required fields for the group creation.
Enter a display name and a short description for the new group.
After filling the form, click on **Save**, and the new group will show in the groups table.

.. image:: /_static/images/groups/groups-create.webp
    :width: 60%
    :alt: Main Menu - Groups Create Dialog
    :align: center

|

A notification of successful group creation will pop up for a few seconds after clicking on the **Create** button.

.. image:: /_static/images/groups/groups-created-notification.webp
   :width: 40%
   :alt: Main Menu - Groups Created Notification
   :align: center

|

.. _deleting-a-group:

~~~~~~~~~~~~~~~~
Removing a Group
~~~~~~~~~~~~~~~~
To remove a group, select a group from the list which will open a dialog for the selected group.
Click on the trash can icon on the top right of the group dialog.

.. image:: /_static/images/groups/groups-remove-icon.webp
   :width: 60%
   :alt: Main Menu - Groups Remove Icon
   :align: center

|

A confirmation popup will appear asking you if you want to delete the group, as seen above.
Click on **Yes** to remove the group.

On successful removal of the group, a notification will appear for a few seconds that the group has been deleted.

.. image:: /_static/images/groups/groups-removed-notification.webp
   :width: 40%
   :alt: Main Menu - Groups Removed Notification
   :align: center

|

~~~~~~~~~~~~~~~~~~~~~~~~~
Editing an Existing Group
~~~~~~~~~~~~~~~~~~~~~~~~~
To edit a group, select a group from the list which will open a dialog for the selected group.
In this dialog, you can modify the group description, just click on the **Save** button after making your
changes. You can also add/remove users from the group. Finally, you'll see a list of all users that belong to the group. To return to the list of all groups in your project, click on the **X** at the top right of the dialog.

.. image:: /_static/images/groups/groups-edit.webp
    :width: 60%
    :alt: Main Menu - Groups Edit
    :align: center

|

.. _adding-users-to-a-group:

~~~~~~~~~~~~~~~~~~~~~~~
Adding Users to a Group
~~~~~~~~~~~~~~~~~~~~~~~
To add a user to a group, click on the group you want to add users. In the ``Users`` column found on the left
in the ``Edit Group Members`` section, you can click on the search box then type in the name, username or
email of the user you want to add to the group.

.. image:: /_static/images/groups/groups-add-user-search.webp
    :width: 60%
    :alt: Main Menu - Groups Add User Search
    :align: center

|

Notice that it will give you a list of matching users, select the user you want to add by clicking on the
checkbox next to it, and if you want to add some more users to the group, just type in the names, and put
a checkmark next to them, then click on the **>** (greater than icon) button.

.. image:: /_static/images/groups/groups-add-members.webp
    :width: 60%
    :alt: Main Menu - Groups Add Members
    :align: center

|

It will then give you a notification that the user(s) has been successfully added to the group.

.. image:: /_static/images/groups/groups-users-added-notification.webp
    :width: 30%
    :alt: Main Menu - Groups Members Added Notification
    :align: center

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~
Removing Users from a Group
~~~~~~~~~~~~~~~~~~~~~~~~~~~
To remove a user from a group, click on the group you want to remove users. In the ``Members`` column
found on the right in the ``Edit Group Members`` section, you can click on the search box then type in
the name, username or email of the user you want to remove from the group. Select the user you want to
remove from the group by clicking on the checkbox next to it, and if you want to remove some more users
from the group, just type in the names and put a checkmark next to them, then click on
the **<** (less than icon) button.

.. image:: /_static/images/groups/groups-remove-user.webp
    :width: 60%
    :alt: Main Menu - Groups Remove Members
    :align: center

|

It will then give you a notification that the user(s) has been successfully deleted from the group.

.. image:: /_static/images/groups/groups-remove-user-confirm.webp
    :width: 30%
    :alt: Main Menu - Groups Members Removed Notification
    :align: center

|

.. _main-menu-tool-cluster:

""""""""""""""""""""""""
Cluster |enterpriseOnly|
""""""""""""""""""""""""
``Cluster`` lets the administrator manage Studio clusters. See :ref:`studio-clustering` for more information on how to setup clustering and available actions from ``Cluster`` from the Main Menu

.. image:: /_static/images/system-admin/main-menu/main-menu-cluster.webp
    :alt: System Administrator - Navigation Menu Cluster
    :align: center
    :width: 85%

|

.. _nav-menu-audit:

"""""
Audit
"""""
Audit logs displays the date, time, user and action performed to content in all the projects available as well as actions
performed in Studio such as logins/logouts, user removal, group addition, etc.

~~~~~~~~~~~
Description
~~~~~~~~~~~
CrafterCMS tracks the date, time, user and action performed to content and the system through an audit log.

To view the audit logs, from the top right of your browser, click on the ``Navigation Menu`` icon, then click on ``Audit``.

.. image:: /_static/images/system-admin/main-menu/main-menu-audit.webp
    :alt: System Administrator - Main Menu Audit
    :align: center
    :width: 85%

|

You can filter the logs displayed based on the following:

~~~~~~~~~~~~~~~~~~~~~~~~~
Audit Logs Project Filter
~~~~~~~~~~~~~~~~~~~~~~~~~
``Project`` filters the log by project . Clicking on ``Project`` gives you a list of all the projects in Studio and the option to see system logs or logs for all the projects.

.. image:: /_static/images/system-admin/main-menu/audit-site-filter.webp
    :alt: System Administrator - Main Menu Audit Project Filter
    :align: center
    :width: 65%

|

~~~~~~~~~~~~~~~~~~~~~~
Audit Logs User Filter
~~~~~~~~~~~~~~~~~~~~~~
``Username`` filters the log by username. Clicking on ``Username`` gives you a list of all the users in Studio and the option to see logs for all users.

.. image:: /_static/images/system-admin/main-menu/audit-user-filter.webp
    :alt: System Administrator - Main Menu Audit User Filter
    :align: center
    :width: 65%

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Audit Logs Operations Filter
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
``Operation`` filters the log by operations. Clicking on ``Operation`` gives you a list of all operations logged.

.. image:: /_static/images/system-admin/main-menu/audit-operations-filter.webp
    :alt: System Administrator - Main Menu Audit Operations Filter
    :align: center
    :width: 65%

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~
Audit Logs Timestamp Filter
~~~~~~~~~~~~~~~~~~~~~~~~~~~
``Timestamp`` filters the log based on date range

.. image:: /_static/images/system-admin/main-menu/audit-options-filter.webp
    :alt: System Administrator - Main Menu Audit Timestamp Filter
    :align: center
    :width: 65%

|

.. _main-menu-tool-logging-levels:

~~~~~~~~~~~~~~
Logging Levels
~~~~~~~~~~~~~~
There are 6 log levels defined in CrafterCMS. These levels determine what messages will be logged and displayed in the **Logging Console**.

.. image:: /_static/images/site-admin/logs-logging-levels.webp
    :alt: System Administrator - Navigation Menu Logging Levels
    :align: center
    :width: 85%

|

For more information on logging levels, see :ref:`override-logging-levels`

.. _main-menu-tool-log-console:

~~~~~~~~~~~
Log Console
~~~~~~~~~~~
The ``Log Console`` allows the user to view messages depending on what log levels and what Java packages have been set for tracking.

.. image:: /_static/images/system-admin/main-menu/main-menu-log-console.webp
    :alt: System Administrator - Navigation Menu Log Console
    :align: center
    :width: 85%

|

:ref:`override-logging-levels` contains more information on how to track Java packages with the corresponding log levels desired.

The ``Log Console`` here in the Main Menu is similar to a project ``Log Console`` described :ref:`here <studio-log-console>`. The difference is the ``Log Console`` from the Main Menu can display logs for all the projects inside Studio, not just one project.

.. _nav-menu-global-config:

"""""""""""""
Global Config
"""""""""""""
CrafterCMS allows the user to edit the system settings for Studio without access to the physical server through ``Global Config`` under the ``Navigation Menu`` in Studio.
This global configuration file overrides the core configuration of Crafter Studio, ``studio-config.yaml``,  found in your Authoring installation, under ``CRAFTER_HOME/bin/apache-tomcat/webapps/studio/WEB-INF/classes/crafter/studio``, and the Studio configuration override file ``studio-config-override.yaml`` under ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension`` in your Authoring installation (for more information on this file, see :ref:`studio-config`.

Changes made to this file will spread to all nodes in a Studio cluster automatically. Please note that not all changes to this file can/will take effect without a restart, so expect to have to **restart Studio for most changes to take effect**. If in a cluster, you'll need a rolling restart for all nodes to pick up the changes.

To access the Global Config, click on the ``Navigation Menu`` icon at the top right corner, then click on ``Global Config`` in the Global panel

.. image:: /_static/images/system-admin/main-menu/main-menu-global-config.webp
    :alt: System Administrator - Navigation Menu Global Config
    :align: center
    :width: 100%

|

To find out more on what you can configure from the Global Config, see :ref:`studio-config`.

|

.. _main-menu-tool-encryption-tool:

"""""""""""""""
Encryption Tool
"""""""""""""""
The ``Encryption Tool`` allows the user to encrypt sensitive data such as access keys and passwords, that shouldn't be publicly available to anyone but developers and administrators

.. image:: /_static/images/system-admin/main-menu/main-menu-encryption-tool.webp
    :alt: System Administrator - Navigation Menu Encryption Tool
    :align: center
    :width: 100%

|

For more information on how to use the encryption tool, see :ref:`studio-encryption-tool`.

|

.. _nav-menu-token-management:

""""""""""""""""
Token Management
""""""""""""""""
The ``Token Management Tool`` allows the user to manage access tokens used to make API requests on behalf of the user and
create tokens for accessing a project/site in Preview.

.. image:: /_static/images/system-admin/main-menu/main-menu-token-management.webp
    :alt: System Administrator - Navigation Menu Token Management Tool
    :align: center
    :width: 70%

|

Note that only users with the required permission ``manage_access_token``, such as users with the ``system_admin`` role,
have access to the ``Token Management Tool``.

For more information on configuring settings for the Studio access tokens, see :ref:`studio-access-tokens`

.. _api-token:

~~~~~~~~~
API Token
~~~~~~~~~
JWT authentication using API access tokens allows the user to access Studio APIs as a particular user with a particular role.

To create a new API access token, click on ``Token Management`` from the Main Menu, then click on the ``API Token`` button.
The only required field for the access token is the label to identify it, however, it is also recommended to set
an expiration date to minimize the risk of lost or stolen tokens being used without being noticed.

.. figure:: /_static/images/jwt/create-token.webp
    :width: 70%
    :alt: Crafter Studio - Create API Access Token
    :align: center

|

Once the expiration date is reached the access token will stop working automatically. Click on the ``Submit`` button to
create the token.

.. figure:: /_static/images/jwt/create-token-2.webp
    :width: 70%
    :alt: Crafter Studio - Access Token Expiration
    :align: center

|

The next step is to copy the value of the access token. The value of the access token will not be stored on the server,
so it needs to be stored by the user in a safe place as it is impossible to recover it after it is created.

.. figure:: /_static/images/system-admin/main-menu/access-token-created.webp
    :width: 70%
    :alt: Crafter Studio - Access Token Created
    :align: center

|

The access token created from the tool is used for JWT authentication to be able to interact with Crafter Studio APIs.
For an example of how to use the generated access token, see :ref:`crafter-cli`.

If an access token is lost or exposed in any way it should be disabled or completely deleted to avoid any
possible use. To delete a token, simply click on the trash can icon to the right of the token you want to delete.

.. figure:: /_static/images/system-admin/main-menu/delete-token-1.webp
    :width: 70%
    :alt: Crafter Studio - Delete a Token
    :align: center

|

You can also delete multiple tokens at once by placing a checkmark on the tokens you want to delete, then clicking on
``Delete Selected``.

.. figure:: /_static/images/system-admin/main-menu/delete-token-2.webp
    :width: 70%
    :alt: Crafter Studio - Delete Multiple Tokens
    :align: center

|

To disable/enable a token, simply click on the slider on the right side of the token next to the trash can icon.

.. figure:: /_static/images/system-admin/main-menu/token-disable.webp
    :width: 70%
    :alt: Crafter Studio - Disable/Enable Token
    :align: center

|

.. note:: Users needs the ``manage_access_token`` permission to create access tokens

|

.. _preview-token:

~~~~~~~~~~~~~
Preview Token
~~~~~~~~~~~~~
.. version_tag::
	:label: Since
	:version: 4.2.0

Preview tokens allow preview applications to access delivery APIs in preview mode from within the authoring environment.
This extra layer of security is required in authoring because the environment contains unpublished projects and content.

To create a Preview Token, click on ``Token Management`` from the Main Menu, then click on the ``Preview Token`` button.

.. figure:: /_static/images/system-admin/main-menu/create-preview-token.webp
    :width: 70%
    :alt: Crafter Studio - Create Preview Access Token
    :align: center

|

The only required fields for the preview token is the dropdown for selecting projects to grant preview access, and the
date/time fields to set an expiration date for the token, which is pre-populated to a date in the near future. The expiration
date is set to minimize the risk of lost or stolen tokens being used without being noticed. Click on the ``Generate``
button to create the token.

.. figure:: /_static/images/system-admin/main-menu/create-preview-token-2.webp
    :width: 70%
    :alt: Crafter Studio - Create Preview Access Token
    :align: center

|

The next step is to copy the value of the preview token. The value of the preview token is not stored on the server,
so it needs to be stored by the user in a safe place as it is impossible to recover it after it is created.

.. figure:: /_static/images/system-admin/main-menu/preview-token-created.webp
    :width: 70%
    :alt: Crafter Studio - Create Preview Access Token
    :align: center

|

You can now use the preview token in one of the following ways:

- Set a header with the name X-Crafter-Preview, or
- Add a query string argument with the name crafterPreview, or
- Set a cookie with the name crafterPreview

Here's an example of using the token with Curl, where ``{Generated-Preview-Token}`` is the token just created:

.. code-block:: bash

    curl --header "cookie: crafterPreview={Generated-Preview-Token};" "http://localhost:8080/api/1/site/content_store/item.json?url=/site/website/index.xml&crafterSite=ed"


The dialog above that shows the preview token generated also shows other examples on how to use the preview token.

Sometimes you may need to disable security for accessing delivery APIs in preview mode from within the authoring environment, when working on a feature. To disable the preview API security, you'll need to set the Engine ``crafter.security.preview.enabled`` property to ``false``. See :ref:`engine-security-preview-mode` for more information.

|

.. _settings:

"""""""""""""""""""""""""
Settings |enterpriseOnly|
"""""""""""""""""""""""""
.. version_tag::
    :label: Since
    :version: 4.5.0

The ``Settings Tool``  allows the user to configure system settings, such as enabling/disabling maintenance mode.

.. image:: /_static/images/system-admin/main-menu/main-menu-settings.webp
    :alt: System Administrator - Navigation Menu Settings Tool
    :align: center
    :width: 70%

|

~~~~~~~~~~~~~~~~
Maintenance Mode
~~~~~~~~~~~~~~~~
Maintenance mode is a status that tells users that your CrafterCMS install is under maintenance and temporarily unavailable.
Turning on maintenance mode when making updates, functionality modifications or design changes to your CrafterCMS install
helps prevent errors and issues and allows for a smooth user experience.

To enable/disable maintenance mode, simply toggle the switch to on or off.

.. image:: /_static/images/system-admin/main-menu/maintenance-mode-on.webp
    :alt: System Administrator - Maintenance mode toggle switch
    :align: center
    :width: 70%

|

When turning on maintenance mode, a dialog will appear to confirm you want to turn on maintenance mode.

.. image:: /_static/images/system-admin/main-menu/maintenance-mode-on-confirmation.webp
    :alt: System Administrator - Maintenance mode on confirmation dialog
    :align: center
    :width: 70%

|

Once maintenance mode is enabled, the UI will show a maintenance mode screen informing users that the system is
temporarily unavailable, as seen here: :ref:`author-maintenance-mode`.
For users with the ``allowed_in_maintenance`` permission, access to Crafter Studio/system APIs is still available
during maintenance mode.

|

"""""""
Account
"""""""
The ``Account Tool`` allows the user to change the user's personal Crafter Studio settings like language or to change the user's password or to clear your Studio UI preferences from the browser cache.

.. image:: /_static/images/system-admin/main-menu/main-menu-account.webp
    :alt: System Administrator - Navigation Menu Account Tool
    :align: center
    :width: 100%

|

For more information on how to use the Account tool, see :ref:`account-management`.

|hr|

.. _user-group-management:

^^^^^^^^^^^^^^^^^^^^^
User/Group Management
^^^^^^^^^^^^^^^^^^^^^
This section describes managing user accounts and groups.

A user is anybody who uses CrafterCMS. A user account holds a user name and password. A group consists of a collection of users. Users can be assigned to a group for a project/site. Through the groups, roles are assigned to users to certain areas of the site (access rights/ permissions). Each role represents a set of activities allowed. Groups are  used to simplify management as changes made to the rights of the group applies to all the users belonging to that group.

When you work in Crafter Studio, you need to login as a user. Your CrafterCMS administrator sets up user accounts, group memberships, roles and permissions. The sections below goes into more detail on how users, groups, permissions and roles are administered/setup.


.. _roles-and-permissions:

"""""""""""""""""""""
Roles and Permissions
"""""""""""""""""""""
To access CrafterCMS, a user must be allowed access rights to certain areas of the project (access rights/ permissions). For example, if a user wants to create, edit or submit content, the user needs to have those specific permissions. Here, we see that the user requires multiple permissions. For simplicity, permissions are grouped together into **roles**. A role is a set of allowed actions/activities. An **author** role, for example, has access to create, edit and submit content.

To define permissions for users, they need to be a member of a group. A group is a collection of users with a role assigned. Groups are used to simplify management as changes made to the rights of the group applies to all the users belonging to that group. For our example above of a user that wants to create, edit or submit content, the user should be assigned to a group with the **author** role.

Out of the box, CrafterCMS supports the following roles/groups:

============== ================= =========================================================
Role           Group             Description
============== ================= =========================================================
system_admin   system_admin      Has access to everything in the CMS, such as all the projects, users, groups, etc. in addition to the admin role
admin          site_admin        Has access to everything in the project such as project configurations, creating/editing layouts, templates, taxonomies, content types, scripts, etc. in addition to the publisher role
author         site_author       Has access to create, edit or submit content in a project
developer      site_developer    Has access to access to creating/editing layouts, templates, taxonomies, content types, scripts, etc., project configurations in addition to the publisher role in a project
reviewer       site_reviewer     Has the ability to approve and reject workflow, but don't have access to the author role in a project
publisher      site_publisher    Has the ability to approve and reject workflow, in addition to the author role in a project
============== ================= =========================================================

Permissions and roles can be setup for each project, and for the entire application itself. Note that the ``system_admin`` role applies to the entire application and the rest of the default roles applies to a project.

See :ref:`groups-management` for more information on administrating groups.

~~~~~~~~
Projects
~~~~~~~~
To edit permissions for a project role, in Studio, from the *Sidebar*, click on |projectTools| -> *Configuration* -> *Permission Mapping*. See :ref:`permission-mappings` for more information on permissions and the default permissions assigned to roles.

To add/edit a role for a project, in Studio, from the *Sidebar*, click on |projectTools| -> *Configuration* -> *Role Mapping*. See :ref:`project-role-mappings` for more information.

The items for interaction/tools available from the **Sidebar** depending on the user role can be configured in Studio, from the *Sidebar*, click on |projectTools| -> *Configuration* -> *User Interface Configuration*. See :ref:`user-interface-configuration` for more information.

~~~~~~
Global
~~~~~~
To add/edit a global role/group, see :ref:`global-role-mappings-config` for more information.

To add/edit global permissions for a role, see :ref:`global-permission-mappings-config` for more information.

The items for interaction/tools available from the |mainMenu| *Main Menu* depending on the user role can be configured by opening the :ref:`global-menu-config.xml <global-menu-config>` file under ``CRAFTER_HOME/data/repos/global/configuration`` using your favorite editor.

.. _putting-it-all-together:

""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
Putting it all together - Users, Groups, Roles and Permissions
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
In this section, we'll see how users, groups, roles and permissions work together in giving users access to
certain folders in a project.

We'll create a new role, group and user, add permissions for the new role and finally assign the newly
created user to the new group setup.

In preparation for our example, we will be using the Website editorial blueprint. We'll add a **news** folder
under **Home**, by navigating to **Pages** -> **Home**, then right click on **Home** and select **New Folder**.
Enter *news* in the **Folder Name** field. We will be using the **news** folder for our example in setting up
permissions to folders based on roles. Users assigned to the **newseditor** role will then have access to
publish and add/edit content in the **news** folder.

~~~~~~~~~~~~~~~~~~
Create a new group
~~~~~~~~~~~~~~~~~~
Let's begin by creating a new group.

#. To create a new group, click on |mainMenu| **Navigation Menu** from the top right, then click on **Groups**.
#. Click on the **Create Group** button.
#. Enter a name for the new group being created in the **Display Name** field.
#. Enter a description of the new group being created in the **Description** field.
#. Click on the **Save** button. A notification will appear that your new group has been created.

Below are the information used to create a new group:

.. image:: /_static/images/site-admin/new-group.webp
     :alt: Group - Create a New Group
     :width: 65%
     :align: center

|

For more information on adding a new group to a project, please see :ref:`create-a-new-group`

~~~~~~~~~~~~~~~~~
Create a new role
~~~~~~~~~~~~~~~~~
We'll now create a new role for the new group we just created.

#. To create a new role, click on |projectTools| from the **Sidebar**, then click on **Configuration**.
#. From the list, select **Role Mappings**
#. Add your new group and role in the editor

   .. code-block:: xml
       :linenos:
       :emphasize-lines: 18,19,20

       <role-mappings>
         <groups>
           <group name="Admin">
               <role>admin</role>
           </group>
           <group name="Developer">
               <role>developer</role>
           </group>
           <group name="Author">
               <role>author</role>
           </group>
           <group name="Publisher">
               <role>publisher</role>
           </group>
           <group name="Reviewer">
               <role>reviewer</role>
           </group>
           <group name="NewsEditor">
               <role>newseditor</role>
           </group>
         </groups>
       </role-mappings>

#. Click on the **Save** button.

For more information about role mappings, please see: :ref:`project-role-mappings`

~~~~~~~~~~~~~~~~~~
Adding permissions
~~~~~~~~~~~~~~~~~~
#. To add permissions to the new role we just created, click on |projectTools| from the **Sidebar**, then click on **Configuration**.
#. From the dropdown box, select **Permissions Mappings**
#. Add in the permissions that you would like to give to the new role that we just created. For our example below, we are giving the role **newseditor** permission to publish from the dashboard and the following permissions for the **news** folder and **assets** folder:

   - read
   - write
   - create content
   - create folder
   - publish

   .. code-block:: xml
      :linenos:

      <role name="newseditor">
         <rule regex="/site/website/news/.*">
           <allowed-permissions>
             <permission>content_read</permission>
             <permission>content_write</permission>
             <permission>content_create</permission>
             <permission>folder_create</permission>
             <permission>publish</permission>
           </allowed-permissions>
         </rule>
         <rule regex="/static-assets/.*">
           <allowed-permissions>
             <permission>content_read</permission>
             <permission>content_write</permission>
             <permission>content_create</permission>
             <permission>folder_create</permission>
             <permission>publish</permission>
           </allowed-permissions>
         </rule>
       </role>

#. Click on the **Save** button to save your changes.

For more information about permission mappings, please see: :ref:`permission-mappings`

~~~~~~~~~~~~~~~~~~~~~~~~
Adding users to the role
~~~~~~~~~~~~~~~~~~~~~~~~
We can now add users to the role by adding the users to the group mapped to the role. In the role mappings configuration file, we mapped the role **newseditor** to the group NewsEditor. To add users to the group NewsEditor,

#. Click on |mainMenu| from the top right of Studio, then select **Groups** on the left hand side
#. Click on the pencil (edit icon) next to the group name you want to edit. In our example, the group **NewsEditor**
#. Click on the box for the field **Add new members**, enter the users you'd like to add, then click on the **Add members** button.

For more information about adding users to a group, please see: :ref:`adding-users-to-a-group`

Your new role with users and permissions assigned are now ready!

.. _user-passwords:

""""""""""""""
User passwords
""""""""""""""
~~~~~~~~~~~~~~~~~~~~~~
Changing Your Password
~~~~~~~~~~~~~~~~~~~~~~
Every user logged in to CrafterCMS can change their own password.

#. To change your own password, click on the **Navigation Menu** |mainMenu| option at the top right of Studio,
   then select **Account**

   .. image:: /_static/images/users/your-passwd-open.webp
       :alt: Users - Open Dialog with User Name
       :width: 65%
       :align: center

   |

#. In the **Change Password** section of the dialog, enter your current password in the **Current Password** field.

   .. image:: /_static/images/users/your-passwd-change.webp
       :alt: Users - User Settings Dialog to Change Password
       :width: 50%
       :align: center

   |

#. Next, enter the new password into the **New Password** field.
#. Re-enter the new password into the **Confirm Password** field.
#. Click on the **Save** button. A notification will appear that the profile has been updated.

   .. image:: /_static/images/users/change-passwd-notification.webp
       :alt: Users - Password Change Notification
       :width: 30%
       :align: center

   |

After changing your password, you will be logged out of the system and will have to log back in using the new password you set before continuing your work in Studio.

~~~~~~~~~~~~~~~~~~~~~~~~
Changing a User Password
~~~~~~~~~~~~~~~~~~~~~~~~
The Crafter admin can change passwords for other users.

#. To change a user's password, login as crafter admin in Studio.
#. Click on **Users** at the top right of Studio
#. Click on the pencil (edit icon) next to the user you would like to change/reset the password.
#. Enter a new password in the **Reset Password** field.
#. Click on the **Save** button. A notification will appear that the user has been edited.

For more information on editing a user, see :ref:`editing-a-user`

'''''''''''''''''''''''''''''''''
Setting a User's Initial Password
'''''''''''''''''''''''''''''''''
The Crafter admin must set an initial password when creating a new user. To create a new user, please see :ref:`creating-a-user`

|hr|

.. _create-project-with-link-to-remote-repo:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Project Creation with Remote Repositories
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Crafter Studio supports project creation with remote repositories and provides two options:

- Create project based on remote Git repository
- Create project based on a blueprint then add a remote Git repository

To start creating a project with a remote repository, from the **Projects** screen, click on the **Create Project** button.
A **Create Project** dialog will be launched. For both options, there will be a screen where the **Remote Git Repository Name** and **Remote Git Repository URL** needs to be filled out and the rest is optional and only needs to be filled out if required by the remote git repository being used.

Let's take a look at the fields where the remote repository details needs to be filled in:

.. image:: /_static/images/system-admin/remote-repo-info.webp
   :alt: System Administrator - Remote Repository Details
   :width: 55 %
   :align: center

|

#. In the **Git Repo URL** field you must provide the link to the Git repository you would like to use
#. In the **Authentication** field you must select the authentication method to be used to access the Git repository in the previous field.

   CrafterCMS supports the following authentication types to use to access remote repository:

   - **Authentication not required (Public URL)** - no credentials needed to access remote repository
   - **Username & Password** - for this method, you will be asked for a **Remote Git Repository Username** and a **Remote Git Repository Password**. Supply your username and password
   - **Token** - for this method, you will be asked for a **Remote Git Repository Username** (if required) and a **Remote Git Repository Token**. This method is usually used when two-factor authentication is configured on the remote repository to be accessed. Supply your username if required and token.
   - **Private Key** - for this method, you will be asked for a **Remote Git Repository Private Key**. This method is a key-based authentication. Supply your private key.

#. In the **Git Branch** field, you can supply a branch name, but can be left blank, which in turn would default to the ``master`` branch.
#. In the **Git Remote Name** field you want to provide a repository name that makes sense. It’s common to use “origin” or “upstream.”

.. _create-project-based-on-a-blueprint-then-add-a-remote-bare-git-repository:

"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
Create project based on a blueprint then add a remote bare Git repository
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
To create a project based on a blueprint then add a remote bare git repository, click on **Create Project** from
**Projects**, then select the blueprint you would like to use

.. image:: /_static/images/developer/dev-cloud-platforms/create-project-then-push-1.webp
    :alt: Create Project Dialog in Crafter Studio, select a blueprint
    :width: 65 %
    :align: center

|

The next step is to fill in the **Project ID** and **Project Name**, then click on the **Review** button, then finally  click on the **Create Project** button to create your project. Your project should be created in a short while.

.. image:: /_static/images/developer/dev-cloud-platforms/create-project-then-push-2.webp
    :alt: Create Project Dialog in Crafter Studio, fill in Site ID
    :width: 65 %
    :align: center

|

Once your project is created, the next step is to add a remote repository to your newly created project. Open the
**Sidebar** then click on **Project Tools** -> **Git**, then click on the **New Remote** on the top.
This will open up a dialog where we can fill in all the information for our remote repository as described above.
Click on the **Create** button after filling in the required information.

.. image:: /_static/images/developer/dev-cloud-platforms/create-project-then-push-3.webp
    :alt: Create Repository dialog to fill in information of remote repository being added to the project
    :width: 65 %
    :align: center

|

Your project should now have a remote repository listed in the **Remote Repositories** tab

.. image:: /_static/images/developer/dev-cloud-platforms/create-project-then-push-4.webp
    :alt: Remotes screen displaying newly added remote repository to project
    :width: 65 %
    :align: center

|

Remember that the remote repository needs to be a bare git repository, since we are pushing our newly created project to the remote repository. To push our newly create project to the remote repository, click on the ``Push`` button (button with the up arrow) next to the remote repository

"""""""""""""""""""""""""""""""""""""""""""""""
Create project based on a remote Git repository
"""""""""""""""""""""""""""""""""""""""""""""""
Creating a project based on a remote Git repository is basically exporting a project from one Studio and importing it into another one.

To create a project based on remote Git repository, after clicking on **Create Project**, Click on **Remote Git Repository** in the create project screen

.. figure:: /_static/images/first-project/create-project-choose-bp.webp
    :alt: Developer How Tos - Setting up to work locally against the upstream
    :width: 65 %
    :align: center

|

Click on the **Site ID** field where you'll need to give your project an ID. Scroll down to see where you can fill in all the information for the remote repository we are importing. The ``Git Repo URL`` is the import project's sandbox repository git url (the project you want to bring over to your Studio). Below are sample urls for the project being imported:

Here is a sample Git url from GitHub:
`https://github.com/username/hello-test.git`
Here is a sample Git url using ssh:
`ssh://[user@]host.xz[:port]/path/to/repo/`
or alternatively for ssh:
`[user@]host.xz:path/to/repo/`

.. figure:: /_static/images/developer/dev-cloud-platforms/craftercms-github-clone-1.webp
   :alt: Developer How Tos - Setting up to work locally against the upstream
   :width: 65 %
   :align: center

|

Click on the **Review** button, then finally, the **Create Project** button.


.. figure:: /_static/images/developer/dev-cloud-platforms/craftercms-github-clone-2.webp
   :alt: Developer How Tos - Setting up to work locally against the upstream review entries
   :width: 65 %
   :align: center

|

After a short while, your project will be imported.

**In case you want to publish the entire project**, follow these optional steps:

#. In the project you just imported, click on **Project Tools**, then click on **Publishing**

   .. image:: /_static/images/system-admin/publishing.webp
      :alt: System Administrator - Bulk Publishing"
      :width: 20 %
      :align: center

   |

#. In the **Publishing** screen, scroll down to ``Publish on Demand`` then click on the **Publish Entire Project**
   button to publish the whole project.

   .. image:: /_static/images/system-admin/bulk-publish-project.webp
      :alt: System Administrator - Bulk Publish the whole project filled in"
      :width: 65 %
      :align: center

   |

|hr|

.. _duplicate-project:

^^^^^^^^^^^^^^^^^^^^^
Duplicating a Project
^^^^^^^^^^^^^^^^^^^^^
Crafter Studio supports creating a new project by duplicating an existing project.
To duplicate a project, from ``Projects``, click on the ``Create Project`` button.

.. image:: /_static/images/first-project/create-project-choose-bp.webp
   :width: 65 %
   :align: center
   :alt: Studio Administration - Create Project

|

Next, click on ``Duplicate Project``. It will then prompt you to select the project to be duplicated by clicking
on the dropdown arrow in the ``Project`` field.  Give it a good ``Project Name`` and ``Project ID``, then click on the
``Review`` button

.. image:: /_static/images/system-admin/duplicate-project-screen.webp
   :width: 65 %
   :align: center
   :alt: Studio Administration - Duplicate Project Screen

|

When duplicating a project that uses S3 buckets (blob stores), the S3 buckets may be copied over to the new project and the
configuration updated if separate S3 buckets from the source project are required.

|hr|

----------
Clustering
----------
Learn about clustering Crafter Studio in the :ref:`Crafter Studio Clustering Guide <studio-clustering>`.

|hr|

.. _crafter-studio-api:

--------
REST API
--------
To view the Crafter Studio REST APIs:

.. open_iframe_modal_button::
   :label: Open here
   :url: ../../_static/api/studio.html
   :title: Studio API

.. raw:: html

    or <a href="../../_static/api/studio.html" target="_blank">in a new tab</a>


|hr|

-----------
Source Code
-----------
Crafter Studio's source code is managed in GitHub: https://github.com/craftercms/studio