:is-up-to-date: True
:last-updated: 4.1.2

.. meta::
   :description: Crafter Profile is CrafterCMS's enterprise user profile and attribute management module — enabling personalization and access control.
   :keywords: Crafter Profile, CrafterCMS, user profile, personalization, enterprise, access control

.. _crafter-profile:

================================
Crafter Profile |enterpriseOnly|
================================
.. figure:: /_static/images/architecture/crafter-profile.webp
    :alt: Crafter Profile
    :width: 75%
    :align: center

|

Crafter Profile is a multi-tenant, platform independent, highly secure and scalable profile and attribute store. It allows web developers to easily add user login and user profile management to website applications. The module enables web developers to create secure login features and gather user profile information for use in targeting and personalization.

Crafter Profile is built on MongoDB for extensibility and extreme scalability and includes a multi-tenant profile attribute store, an admin console for user profile management, chained authentication with any existing authentication services including Active Directory, TAM, Crowd, Open Social  and others. In addition, Crafter Profile can easily extend existing profiles without interfering with core repositories.

Crafter Profile provides a secure and scalable platform for storing, querying and analyzing user data at each interaction in your customer journey.

.. _crafter-profile-admin:

--------------------------------
Configuration and Administration
--------------------------------
This guide covers the basic configuration for Crafter Profile, if you need to manage tenants and
profiles you can follow the :ref:`crafter-profile-admin-console` guides.

.. NOTE::
  This guide assumes that you have already installed and configured MongoDB, or you've used
  CrafterCMS's Gradle build system (which will install MongoDB for you). You can find more
  information in the official documentation: https://docs.mongodb.com/manual/installation/

All configuration for Crafter Profile is managed using a properties file:

  ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/profile/extension/server-config.properties``

^^^^^^^^^^^^^^^^^^^^^
MongoDB Configuration
^^^^^^^^^^^^^^^^^^^^^
Using the following properties you can change the server and database used by Crafter Profile.
This will allow you to use advanced MongoDB features and also host multiple Crafter Profile
databases in the same MongoDB server.

Properties prefix: ``crafter.profile.mongodb.``

+------------------------------------+----------------------+------------------------------------+
| Property                           | Default Value        | Description                        |
+====================================+======================+====================================+
| connection.newConnectionStr        | [#]_                 || MongoDB URL used for all          |
|                                    |                      || connections                       |
+------------------------------------+----------------------+------------------------------------+
| connection.dbName                  | crafterprofile       || Name of the MongoDB database      |
+------------------------------------+----------------------+------------------------------------+
| connection.scriptRunner.withClient | false                || If enabled a native client will   |
|                                    |                      || be used to run all scripts        |
+------------------------------------+----------------------+------------------------------------+
| connection.scriptRunner.clientPath | /usr/local/bin/mongo || Absolute path of the native       |
|                                    |                      || client                            |
+------------------------------------+----------------------+------------------------------------+
| connection.dbUsername              | None                 || Username for MongoDB              |
|                                    |                      || authentication                    |
+------------------------------------+----------------------+------------------------------------+
| connection.dbPassword              | None                 || Password for MongoDB              |
|                                    |                      || authentication                    |
+------------------------------------+----------------------+------------------------------------+
| scripts.runOnInit                  | true                 || If enabled all scripts for        |
|                                    |                      || creating the default tenant,      |
|                                    |                      || profile and access tokens in the  |
|                                    |                      || db will run each time the         |
|                                    |                      || Crafter Profile war is loaded     |
+------------------------------------+----------------------+------------------------------------+
| scripts.paths                      | [#]_                 || List of all MongoDB scripts to    |
|                                    |                      || run                               |
+------------------------------------+----------------------+------------------------------------+

^^^^^^^^^^^^^^^^^^^
Email Configuration
^^^^^^^^^^^^^^^^^^^
Using the following properties you can change the server used by Crafter Profile to send emails.

Properties prefix: ``crafter.profile.mail.``

+----------------------+---------------+--------------------------------------------------+
| Property             | Default Value | Description                                      |
+======================+===============+==================================================+
| host                 | localhost     || Email server host name                          |
+----------------------+---------------+--------------------------------------------------+
| port                 | 25            || Email server port                               |
+----------------------+---------------+--------------------------------------------------+
| protocol             | smtp          || Email server protocol                           |
+----------------------+---------------+--------------------------------------------------+
| username             | None          || Email server username                           |
+----------------------+---------------+--------------------------------------------------+
| password             | None          || Email server password                           |
+----------------------+---------------+--------------------------------------------------+
| encoding             | UTF-8         || Character encoding used for the                 |
|                      |               || emails                                          |
+----------------------+---------------+--------------------------------------------------+
| smtp.auth            | false         || If enabled the connection will try to use the   |
|                      |               || username and password for authentication        |
+----------------------+---------------+--------------------------------------------------+
| smtp.starttls.enable | false         || If enabled the connection will be secured       |
+----------------------+---------------+--------------------------------------------------+
| templates.path       | [#]_          || Location for resolving email templates          |
+----------------------+---------------+--------------------------------------------------+

^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Authentication Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Using the following properties you can change how Crafter Profile handles the authentication
process.

Properties prefix: ``crafter.profile.auth.``

+--------------------------------+---------------+-----------------------------------------------+
| Property                       | Default Value | Description                                   |
+================================+===============+===============================================+
| ticket.maxAge                  | 900           || Maximum time in seconds to keep the          |
|                                |               || cookie                                       |
+--------------------------------+---------------+-----------------------------------------------+
| ticket.cleaner.startDelay      | 0             || Time in milliseconds to wait before          |
|                                |               || starting                                     |
+--------------------------------+---------------+-----------------------------------------------+
| ticket.cleaner.repeatInterval  | 30000         || Time interval in milliseconds to run the     |
|                                |               ||  cleaner task                                |
+--------------------------------+---------------+-----------------------------------------------+
| lockTime                       | 10            || Time in minutes to lock a profile after      |
|                                |               || the specified failed attempts to login       |
+--------------------------------+---------------+-----------------------------------------------+
| failedLoginAttemptsBeforeLock  | 8             || Limit of failed attempts to login before     |
|                                |               || locking the profile                          |
+--------------------------------+---------------+-----------------------------------------------+
| failedLoginAttemptsBeforeDelay | 2             || Number of consecutive login attempts before  |
|                                |               || a delay is added                             |
+--------------------------------+---------------+-----------------------------------------------+

^^^^^^^^^^^^^^^^^^^^^^^^^^
Verification Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^
Using the following properties you can change how Crafter Profile handles the profile verification
process.

Properties prefix: ``crafter.profile.verification.``

+---------------------------------+------------------------------+-------------------------------+
| Property                        | Default Value                | Description                   |
+=================================+==============================+===============================+
| token.maxAge                    | 86400                        || Maximum time in seconds      |
|                                 |                              || to keep the verification     |
|                                 |                              || token                        |
+---------------------------------+------------------------------+-------------------------------+
| token.cleaner.startDelay        | 0                            || Time in milliseconds to      |
|                                 |                              || wait before starting the     |
|                                 |                              || task that deletes expired    |
|                                 |                              || tokens                       |
+---------------------------------+------------------------------+-------------------------------+
| ticket.cleaner.repeatInterval   | 30000                        || Time interval in milliseconds|
|                                 |                              || to run the cleaner task      |
+---------------------------------+------------------------------+-------------------------------+
| newProfile.mail.from            | noreply\@example.com         || Email address to send        |
|                                 |                              || the verification mail        |
+---------------------------------+------------------------------+-------------------------------+
| newProfile.mail.subject         | Verify Account               || Subject for the              |
|                                 |                              || verification mail            |
+---------------------------------+------------------------------+-------------------------------+
| newProfile.mail.templateName    | verify-new-profile-email.ftl || Name of the template for     |
|                                 |                              || the verification mail        |
+---------------------------------+------------------------------+-------------------------------+
| resetPassword.mail.from         | noreply\@example.com         || Email address to send        |
|                                 |                              || the reset password mail      |
+---------------------------------+------------------------------+-------------------------------+
| resetPassword.mail.subject      | Reset Password               || Subject for the              |
|                                 |                              || reset password mail          |
+---------------------------------+------------------------------+-------------------------------+
| resetPassword.mail.templateName | reset-password-email.ftl     || Name of the template for     |
|                                 |                              || the reset password mail      |
+---------------------------------+------------------------------+-------------------------------+

^^^^^^^^^^^^^^^^^^^^^^^^^
Remember Me Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^
Using the following properties you can change how Crafter Profile validates the `Remember Me`
cookies.

Properties prefix: ``crafter.profile.auth.``

+-------------------------------+---------------+------------------------------------------------+
| Property                      | Default Value | Description                                    |
+===============================+===============+================================================+
| ticket.maxAge                 | 900           || Maximum time in seconds to keep the           |
|                               |               || ticket                                        |
+-------------------------------+---------------+------------------------------------------------+
| ticket.cleaner.startDelay     | 0             || Time in milliseconds to wait before           |
|                               |               || starting the task that deletes expired        |
|                               |               || tickets                                       |
+-------------------------------+---------------+------------------------------------------------+
| ticket.cleaner.repeatInterval | 30000         || Time interval in milliseconds to run the      |
|                               |               ||  cleaner task                                 |
+-------------------------------+---------------+------------------------------------------------+

^^^^^^^^^^^^^^^^^^^
Tasks Configuration
^^^^^^^^^^^^^^^^^^^
Using the following properties you can change how Crafter Profile handles the concurrent tasks.

Properties prefix: ``crafter.profile.task.``

+------------------------+---------------+------------------------------------------------+
| Property               | Default Value | Description                                    |
+========================+===============+================================================+
| executor.poolSize      | 5-50          || Range of allowed concurrent tasks             |
+------------------------+---------------+------------------------------------------------+
| executor.queueCapacity | 100           || Total maximum of tasks                        |
+------------------------+---------------+------------------------------------------------+

^^^^^^^^^^^^^^^^^^^^^^^^^
Attachments Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^
Using the following properties you can change how Crafter Profile handles file attachments for
profiles.

Properties prefix: ``crafter.profile.attachments.``

+----------------+----------------------------+------------------------------------------------+
| Property       | Default Value              | Description                                    |
+================+============================+================================================+
| validMimeTypes || image/bmp, image/gif,     || List of file types that will be accepted as   |
|                || image/jpeg, image/png,    || attachments in the profiles                   |
|                || image/tiff, image/svg+xml ||                                               |
+----------------+----------------------------+------------------------------------------------+
| maxSizeUpload  || 5242880                   || Maximum file size in bytes to be uploaded     |
+----------------+----------------------------+------------------------------------------------+
| maxSizeMem     || 1048576                   || Maximum file size in bytes to store in        |
|                ||                           || memory                                        |
+----------------+----------------------------+------------------------------------------------+

^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Using Custom Email Templates
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
By default Crafter Profile only uses two email templates, but in the future more could be added.

Email Templates
 - verify-new-profile-email.ftl
 - reset-password-email.ftl

If you want the emails to match the styles from your site or application you can create your own
templates and configure Crafter Profile to use them following this steps:

1. Change the property ``crafter.profile.mail.templates.path`` to something like ``classpath:crafter/profile/extension/templates``
2. Place the new templates in the folder ``TOMCAT/shared/classes/crafter/profile/extension/templates``
3. Reload the ``crafter-profile.war`` file

The templates will have available the ``verificationLink`` variable.

.. code-block:: html
  :caption: Example Email Template

  Click on the link below to verify your Crafter Profile account.
  <br/><br/>
  <a id="verificationLink" href="${verificationLink}">${verificationLink}</a>
  <br/><br/>
  If it does not work copy and paste the URL to your browser.
  <br/><br/>
  Best regards,
  <br/>
  Crafter Team

.. rubric:: Notes

.. [#] - Default Value: ``mongodb://localhost:27017/crafterprofile?readPreference=primary&maxPoolSize=150&minPoolSize=50&maxIdleTimeMS=1000&waitQueueMultiple=200&waitQueueTimeoutMS=1000&w=1&journal=true``
       - You can find more information about the format and parameters here: https://docs.mongodb.com/manual/reference/connection-string/

.. [#] - Default Value: ``classpath:crafter/profile/init-data.js,classpath:crafter/profile/upgrade-db.js``
       - You can add any custom MongoDB script, more information: https://docs.mongodb.com/manual/tutorial/write-scripts-for-the-mongo-shell/

.. [#] - Default Value: ``classpath:crafter/profile/mail/templates``
       - If you change this property be sure to include all templates needed by Crafter Profile in the new location

.. _crafter-profile-admin-console:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Crafter Profile Admin Console UI
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Crafter Profile Admin Console consists of a single WAR file, with a dependency on access to
Crafter Profile. This web application provides a simple way to manage all data related to tenants
and profiles without the need to call the :ref:`crafter-profile-api` directly.

"""""""""""""""""""
Configuration Guide
"""""""""""""""""""
Similar to other CrafterCMS components you can configure the Profile Admin Console using a simple
properties file placed in the following location:

  ``$CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/profile/management/extension/server-config.properties``

You can change any of the default configuration, some of the more relevant properties are:

.. code-block:: properties

  crafter.profile.rest.client.url.base=http://localhost:8080/crafter-profile
  crafter.profile.rest.client.accessToken.id=e8f5170c-877b-416f-b70f-4b09772f8e2d

~~~~~~~~~~
Properties
~~~~~~~~~~
+---------------------------------------+--------------------------------------------------------+
| Property                              | Description                                            |
+=======================================+========================================================+
| ...rest.client.url.base               || URL where Crafter Profile is deployed, can be an      |
|                                       || external server                                       |
+---------------------------------------+--------------------------------------------------------+
| ...rest.client.accessToken.id         || Access Token used by the Admin Console application,   |
|                                       || can be changed in the first login                     |
+---------------------------------------+--------------------------------------------------------+

"""""
Login
"""""
you can access the application in the following URL:
``HOST:PORT/crafter-profile-admin``

.. figure:: /_static/images/profile-admin/login.webp
  :align: center
  :width: 25%
  :alt: Crafter Profile Admin Console Login

  Crafter Profile Admin Console login dialog.

By default there is only one user created:

.. code-block:: none

  Username: admin
  Password: admin

.. _profile-admin-access-tokens:

""""""""""""""""""""""""
Access Tokens Management
""""""""""""""""""""""""
All applications need an Access Token in order to make requests for the :ref:`crafter-profile-api`.
By default three tokens will be created:

+----------------+-----------------------------------------------------------------------------+
| Application    | Description                                                                 |
+================+=============================================================================+
| profile-admin  || Used by the Profile Admin Console web application for all operations       |
|                || on tenants and profiles.                                                   |
+----------------+-----------------------------------------------------------------------------+
| engine         || Used by Crafter Engine for authenticating and getting profiles.            |
+----------------+-----------------------------------------------------------------------------+
| social         || Used by Crafter Social for getting profiles.                               |
+----------------+-----------------------------------------------------------------------------+

~~~~~~~~
List All
~~~~~~~~
To view all existing Access Tokens you can click the ``List Access Tokens`` link in the left sidebar.

.. figure:: /_static/images/profile-admin/access-tokens-list.webp
  :align: center
  :alt: Crafter Profile access tokens list

From this page you can view the general information about the tokens and delete them if needed.

.. WARNING::
  When you delete an Access Token all applications that are configured to use it will be unable to
  access the API immediately.

~~~~~~~~~~~~
View Details
~~~~~~~~~~~~
From the List Access Tokens page you can click the ID of an existing record to display the details:

.. figure:: /_static/images/profile-admin/access-tokens-view.webp
  :align: center
  :alt: Crafter Profile access tokens view

.. NOTE::
  Access Tokens are immutable, if you need to change the permissions for an existing token you need
  to delete it and create a new one using the same value for the ID.

~~~~~~~~~~~~~~~~~~~~~~~~~
Create a New Access Token
~~~~~~~~~~~~~~~~~~~~~~~~~
To create a new Access Token you can click the ``New Access Token`` link in the left sidebar.

.. figure:: /_static/images/profile-admin/access-tokens-new.webp
   :align: center
   :alt: Crafter Profile access tokens new

''''''
Fields
''''''
+--------------------+-------------+---------+---------------------------------------------------+
| Field              | Required    | Type    |  Description                                      |
+====================+=============+=========+===================================================+
| ID                 | |checkmark| | String  || This field needs to be unique, applications need |
|                    |             |         || to be configured to include it in the requests   |
+--------------------+-------------+---------+---------------------------------------------------+
| Application        | |checkmark| | String  || Arbitrary name used to describe the application  |
|                    |             |         || that will use this token                         |
+--------------------+-------------+---------+---------------------------------------------------+
| Master             |             | Boolean || If set to `true` the application using the token |
|                    |             |         || will be allowed to perform operations on other   |
|                    |             |         || Access Tokens                                    |
+--------------------+-------------+---------+---------------------------------------------------+
| Expires On         | |checkmark| | Date    || All request using a token after the `Expired On` |
|                    |             |         || date will fail                                   |
+--------------------+-------------+---------+---------------------------------------------------+
| Tenant Permissions |             | List    || List of tenants with allowed permissions for     |
|                    |             |         || each one                                         |
+--------------------+-------------+---------+---------------------------------------------------+

''''''''''''''''''''''''''
Example Tenant Permissions
''''''''''''''''''''''''''
.. figure:: /_static/images/profile-admin/access-tokens-permissions.webp
  :align: center
  :alt: Crafter Profile access tokens permissions

An application using an Access Token with these permissions will be able to:

  - Query data from both tenants ``site1`` and ``site2``
  - Update only tenant ``site2``
  - Query, create, update and delete profiles for both ``site1`` and ``site2``


.. _profile-admin-tenants:

""""""""""""""""""
Tenants Management
""""""""""""""""""
Tenants can be used to organize profiles separating them by company or department or site. This
allows better control over data access by the applications.

After the installation there is only one tenant named ``default``, it will include the roles and
attributes used by the Crafter Profile Admin Console and Crafter Profile. You are free to change
it or create a new one to replace it.

~~~~~~~~
List All
~~~~~~~~
To view all existing tenants you can click the ``List Tenants`` link in the left sidebar.

.. figure:: /_static/images/profile-admin/tenants-list.webp
  :align: center
  :alt: Crafter Profile admin tenants list

In this page you can see the names of the tenants and delete them if needed.

.. WARNING::
  When you delete a tenant, all profiles created under it will be also deleted and there is no
  way to recover the data.

~~~~~~~~~~~~~~~~~
Create New Tenant
~~~~~~~~~~~~~~~~~
You can create a new tenant by clicking the ``New Tenant`` link in the left sidebar.

.. figure:: /_static/images/profile-admin/tenants-new.webp
  :align: center
  :alt: Crafter Profile admin new tenant

''''''
Fields
''''''
+-----------------------+-------------+---------+------------------------------------------------+
| Field                 | Required    | Type    |  Description                                   |
+=======================+=============+=========+================================================+
| Name                  | |checkmark| | String  || Unique name for the tenant                    |
+-----------------------+-------------+---------+------------------------------------------------+
| Verify Profiles       |             | Boolean || If set to ``true`` new profiles created for   |
|                       |             |         || this tenant will not be available until the   |
|                       |             |         || verification process is completed             |
+-----------------------+-------------+---------+------------------------------------------------+
| Enable Single Sign-On |             | Boolean || If set to ``true`` Crafter Profile will enable|
|                       |             |         || SSO security by looking for two properties in |
|                       |             |         || HTTP headers:                                 |
|                       |             |         ||    CRAFTER_username and CRAFTER_email         |
|                       |             |         || these names can be changed by changing Crafter|
|                       |             |         || Profile security provider configuration. SSO  |
|                       |             |         || is typically implemented using SAML2 and      |
|                       |             |         || Apache mod_auth_mellon.                       |
+-----------------------+-------------+---------+------------------------------------------------+
| Cleanse Attributes    |             | Boolean || If set to ``true``, escape HTML tags in       |
|                       |             |         || values for attributes of type ``TEXT``,       |
|                       |             |         || ``LARGE TEXT`` and ``STRING LIST``            |
+-----------------------+-------------+---------+------------------------------------------------+
| Available Roles       |             | List    || List of roles that profiles can have          |
+-----------------------+-------------+---------+------------------------------------------------+
| Attribute Definitions |             | List    || List of attributes that profiles can have     |
+-----------------------+-------------+---------+------------------------------------------------+

''''''''''''''''''''
Profile Verification
''''''''''''''''''''
If your site or application will be open to the general public, it is a good idea to avoid spam by
enabling the profile verification feature. When a tenant has this feature enabled and you
include the ``crafter.profile.management.profile.verificationUrl`` property in the configuration,
all new users will receive a verification email and the profile will be enabled only when the
process is completed.

.. figure:: /_static/images/profile-admin/verification-mail.webp
  :align: center
  :width: 60%
  :alt: Crafter Profile admin verification mail

  Example verification mail using ``http://www.example.com`` as the `verificationUrl`

.. NOTE::
  You can change the email sender, subject and body template in the Crafter Profile configuration.

'''''
Roles
'''''
Roles are simple strings used to differentiate users for business logic. In your site or
application you can check if a profile has certain roles to choose what content they can see
or change.

Crafter Profile Admin Console uses the following roles:

- PROFILE_SUPERADMIN
- PROFILE_TENANT_ADMIN
- PROFILE_ADMIN

If you are going to have multiple users using the Profile Admin Console you can change the roles to
make sure only the appropriate users are able to change sensitive content.

'''''''''''''''''''''
Attribute Definitions
'''''''''''''''''''''
A tenant can have any number of custom attributes according to the needs of each site or
application. The attributes are used to store meta data used in the business logic.
When a new profile is created you can set a value for each one of the attributes defined in the
tenant.

.. figure:: /_static/images/profile-admin/tenants-update-attr.webp
  :align: center
  :alt: Crafter Profile update tenants attributes

|

+-----------------------+-------------+---------+------------------------------------------------+
| Field                 | Required    | Type    |  Description                                   |
+=======================+=============+=========+================================================+
| Name                  | |checkmark| | String  || Unique name for the attribute                 |
+-----------------------+-------------+---------+------------------------------------------------+
| Label                 | |checkmark| | String  || Label shown in the Admin Console only         |
+-----------------------+-------------+---------+------------------------------------------------+
| Type                  | |checkmark| | String  || Type of value for the attribute               |
|                       |             |         || - Text                                        |
|                       |             |         || - Large Text                                  |
|                       |             |         || - Number                                      |
|                       |             |         || - Boolean                                     |
|                       |             |         || - String List                                 |
|                       |             |         || - Complex                                     |
+-----------------------+-------------+---------+------------------------------------------------+
| Default Value         |             |         || Initial value if none is provided, the type   |
|                       |             |         || will change depending on each attribute       |
+-----------------------+-------------+---------+------------------------------------------------+
| Display Order         | |checkmark| | Integer || Used by the Admin Console to sort the         |
|                       |             |         || attributes in the view/update form            |
+-----------------------+-------------+---------+------------------------------------------------+
| Attribute Permissions | |checkmark| | List    || List of applications with the permissions for |
|                       |             |         || each one                                      |
+-----------------------+-------------+---------+------------------------------------------------+

~~~~~~~~~~~~~
Update Tenant
~~~~~~~~~~~~~
From the list page you can click the name of a tenant to open the update page:

.. figure:: /_static/images/profile-admin/tenants-update.webp
  :align: center
  :alt: Crafter Profile update tenants

|

All fields can be changed except for the name.

.. WARNING::
  Changes for the roles and attribute definitions will only affect new profiles, existing ones
  will not be updated automatically.

.. _profile-admin-profiles:

"""""""""""""""""""
Profiles Management
"""""""""""""""""""
Profiles hold data for each user of the site or application, each profile needs to be related to
a tenant because it will inherit the attribute definitions and available roles.

~~~~~~~~~~~~~
List & Search
~~~~~~~~~~~~~
You can list all existing profiles for a given tenant by clicking the ``List Profiles`` link in the
left sidebar.

.. figure:: /_static/images/profile-admin/profiles-list.webp
  :align: center
  :width: 80%
  :alt: Crafter Profile admin profiles list

Once the page is loaded you can select the desired tenant by using the dropdown below
the page title.

You can also search for a specific username by using the filter next to the tenant dropdown. The
filter can be removed using the ``Reset`` button.

From this page you can also delete profiles.

~~~~~~~~~~~~~~~~~~
Create New Profile
~~~~~~~~~~~~~~~~~~
To create a new profile you can click the ``New Profile`` link in the left sidebar.

.. figure:: /_static/images/profile-admin/profiles-new.webp
  :align: center
  :width: 80%
  :alt: Crafter Profile admin new profiles

''''''
Fields
''''''
+-----------------------+-------------+---------+------------------------------------------------+
| Field                 | Required    | Type    |  Description                                   |
+=======================+=============+=========+================================================+
| Username              | |checkmark| | String  || Unique username for the new profile           |
+-----------------------+-------------+---------+------------------------------------------------+
| Tenant                | |checkmark| | String  || Tenant to which the new profile will belong   |
+-----------------------+-------------+---------+------------------------------------------------+
| Email                 | |checkmark| | String  || Email for the new profile                     |
+-----------------------+-------------+---------+------------------------------------------------+
| Password              | |checkmark| | String  || Password for the new profile                  |
+-----------------------+-------------+---------+------------------------------------------------+
| Enabled               |             | Boolean || If set to `false` the authentication for the  |
|                       |             |         || new profile will always fail                  |
+-----------------------+-------------+---------+------------------------------------------------+
| Roles                 |             | List    || List of roles that will be assigned to the    |
|                       |             |         || new profile                                   |
+-----------------------+-------------+---------+------------------------------------------------+

'''''''''''''
Custom Fields
'''''''''''''
Crafter Profile Admin Console will display an additional field for each one of the attribute
definition in the selected tenant. The input will change depending on the attribute type.

.. NOTE::
  Custom Fields are always optional, if no default value is set in the tenant they will remain empty.

  Some attribute types such as ``Complex`` can only be changed using the API, those fields will
  appear to be disabled in the Admin Console.

~~~~~~~~~~~~~~
Update Profile
~~~~~~~~~~~~~~
From the list page you can click the ID of a profile to open the update page:

.. figure:: /_static/images/profile-admin/profiles-update.webp
  :align: center
  :width: 80%
  :alt: Crafter Profile admin update profiles

When updating an existing profile the username and tenant are not editable.

'''''''''''''''''
Additional Fields
'''''''''''''''''
These fields are not shown in the new profile form because the values are always assigned
automatically by Crafter Profile.

+------------------+---------+-------------------------------------------------------------------+
| Field            | Type    |  Description                                                      |
+==================+=========+===================================================================+
| Verified         | Boolean || Indicates if the verification process has been completed by the  |
|                  |         || user                                                             |
+------------------+---------+-------------------------------------------------------------------+
| Created On       | Date    || Date when the profile was created                                |
+------------------+---------+-------------------------------------------------------------------+
| Last Modified On | Date    || Date when the profile was last updated                           |
+------------------+---------+-------------------------------------------------------------------+

To configure Crafter Profile, please see :ref:`crafter-profile-admin`

|hr|

.. _crafter-profile-api:

--------
REST API
--------
To view the Crafter Profile REST APIs:

.. open_iframe_modal_button::
   :label: Open here
   :url: ../../_static/api/profile.html
   :title: Profile API

.. raw:: html

   or <a href="../../_static/api/profile.html" target="_blank">in a new tab</a>

|

|hr|

-----------
Source Code
-----------
Crafter Profile's source code is managed in GitHub: https://github.com/craftersoftware/craftercms/tree/support/4.x/profile
