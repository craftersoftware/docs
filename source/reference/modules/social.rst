:is-up-to-date: True
:last-updated: 4.1.2

.. index:: Modules; Crafter Social

.. meta::
   :description: Crafter Social is CrafterCMS's enterprise user-generated content module — enabling comments, ratings, and community features in your projects.
   :keywords: Crafter Social, CrafterCMS, user-generated content, comments, ratings, enterprise

.. _crafter-social:

===============================
Crafter Social |enterpriseOnly|
===============================
.. figure:: /_static/images/architecture/crafter-social.webp
    :alt: Crafter Social
    :width: 75%
    :align: center

|

Crafter Social is a multi-tenant, platform independent user-generated content management system for handling all actions related to user-generated content (UGC), including the creation, updating and moderation of the content. It is built on MongoDB and uses :ref:`crafter-profile` for profile, tenant, roles management, and authentication. Crafter Social is highly scalable in terms of both the users & data, and secures the generated content using Crafter Profile and the Crafter Profile Security library. As a headless, RESTful application, Crafter Social allows for loosely coupled integration with the vertical applications using it. Some examples of these vertical applications include:

    - a products site, for example a books site with reviews & ratings,
    - a ratings site and
    - a blogging application with threaded comments.

.. _crafter-social-admin:

--------------------------------
Configuration and Administration
--------------------------------
This guide covers the basic configuration for Crafter Social, if you need to manage contexts and
user generated content you can follow the :ref:`social-admin-console` guides.

.. NOTE::
  This guide assumes that you have already installed and configured Crafter Profile. For details
  see :ref:`crafter-profile-admin`


All configuration for Crafter Social is managed using a properties file:

  ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/social/extension/server-config.properties``

^^^^^^^^^^^^^^^^^^^^^
MongoDB Configuration
^^^^^^^^^^^^^^^^^^^^^

Using the following properties you can change the server and database used by Crafter Social.
This will allow you to use advanced MongoDB features and also host multiple Crafter Social
databases in the same MongoDB server.

Properties prefix: ``crafter.social.mongodb.``

+------------------------------------+----------------------+------------------------------------+
| Property                           | Default Value        | Description                        |
+====================================+======================+====================================+
| connection.newConnectionStr        | [#]_                 || MongoDB URL used for all          |
|                                    |                      || connections                       |
+------------------------------------+----------------------+------------------------------------+
| connection.dbName                  | craftersocial        || Name of the MongoDB database      |
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
|                                    |                      || creating the default context,     |
|                                    |                      || will run each time the            |
|                                    |                      || Crafter Social war is loaded      |
+------------------------------------+----------------------+------------------------------------+
| scripts.paths                      | [#]_                 || List of all MongoDB scripts to    |
|                                    |                      || run                               |
+------------------------------------+----------------------+------------------------------------+

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Crafter Profile Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Using these properties you can control how Crafter Social interacts with Crafter Profile.

Properties prefix: ``crafter.profile.rest.client.``

+----------------+----------------------------------------+------------------------------------+
| Property       | Default Value                          | Description                        |
+================+========================================+====================================+
| url.base       | \http://localhost:8080/profile         || URL where Crafter Profile is      |
|                |                                        || deployed                          |
+----------------+----------------------------------------+------------------------------------+
| accessToken.id | 2ba3ac10-c43e-11e3-9c1a-0800200c9a66   || Access Token included in all      |
|                |                                        || requests made to the Crafter      |
|                |                                        || Profile API                       |
+----------------+----------------------------------------+------------------------------------+

^^^^^^^^^^^^^^^^^
Web Configuration
^^^^^^^^^^^^^^^^^

Properties prefix: ``crafter.social.web.``

+--------------------+-----------------------+---------------------------------------------------+
| Property           | Default Value         | Description                                       |
+====================+=======================+===================================================+
| mapping.dateFormat | yyyy-MM-dd'T'HH:mm'Z' || Pattern used to format all dates                 |
+--------------------+-----------------------+---------------------------------------------------+
| maxUpload          | 10485760              || Maximum file size in bytes for attachments       |
+--------------------+-----------------------+---------------------------------------------------+
| defaultSortOrder   | ASC                   || Default sort order used for queries              |
+--------------------+-----------------------+---------------------------------------------------+

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
User Generated Content Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Properties prefix: ``crafter.social.ugc.``

+------------------+---------------------------+-------------------------------------------------+
| Property         | Default Value             | Description                                     |
+==================+===========================+=================================================+
| invalidQueryKeys || ``contextId:|$where:``   || MongoDB keywords not allowed in the            |
|                  ||                          || user provided queries                          |
+------------------+---------------------------+-------------------------------------------------+
| arraySortFields  || `flags`, `votesDown`,    || MongoDB fields allowd to sort query            |
|                  || `votesUp`, `attachments`,|| results                                        |
|                  || `ancestors`              ||                                                |
+------------------+---------------------------+-------------------------------------------------+
| virusScanner     || [#]_                     || Class implementing the ``VirusScanner``        |
|                  ||                          || interface, see :ref:`crafter-social-av`        |
+------------------+---------------------------+-------------------------------------------------+

^^^^^^^^^^^^^^^^^^^^^^^^^^^
Notifications Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Using these configurations you can control how Crafter Social handles email notifications.

Properties prefix: ``studio.social.notification.``

+-----------------+-------------------+----------------------------------------------------------+
| Property        | Default Value     | Description                                              |
+=================+===================+==========================================================+
| dailyCron       | ``0 0 0 * * ?``   || CRON Expressions used to control how often each type    |
+-----------------+-------------------+| of notification is sent.                                |
| weeklyCron      | ``0 0 0 ? * SUN`` |                                                          |
+-----------------+-------------------+                                                          |
| instantCron     | ``0 */2 * * * ?`` |                                                          |
+-----------------+-------------------+----------------------------------------------------------+
| startDelay      | 60                || Time in seconds to wait after the application is        |
|                 |                   || loaded to start sending notifications                   |
+-----------------+-------------------+----------------------------------------------------------+
| instantInterval | 120000            || Time in milliseconds to wait between each instant       |
|                 |                   || notification is sent                                    |
+-----------------+-------------------+----------------------------------------------------------+
| disable         | false             || If set to ``true`` notifications will not be sent       |
+-----------------+-------------------+----------------------------------------------------------+

^^^^^^^^^^^^^^^^^^^
Other Configuration
^^^^^^^^^^^^^^^^^^^

Properties prefix: ``studio.social.system.``

+--------------------------+---------------------+-----------------------------------------------+
| Property                 | Default Value       | Description                                   |
+==========================+=====================+===============================================+
| ugcClass                 || [#]_               || Class implementing the                       |
|                          |                     || ``UgcFactory`` interface, will               |
|                          |                     || be used to create all new user               |
|                          |                     || generated contents                           |
+--------------------------+---------------------+-----------------------------------------------+
| defaultLocale            || EN                 || Locale used for notification                 |
|                          |                     || templates                                    |
+--------------------------+---------------------+-----------------------------------------------+
| ignoreAnonymousFlagRoles || SOCIAL_ADMIN,      || Roles not taken into account when            |
|                          || SOCIAL_SUPERADMIN, || adding flags to user generated               |
|                          || SOCIAL_MODERATOR   || content                                      |
+--------------------------+---------------------+-----------------------------------------------+

Properties prefix: ``studio.social.system.profileAgg.``

+---------------------------+----------+---------------------------------------------------------+
| Property                  || Default | Description                                             |
|                           || Value   |                                                         |
+===========================+==========+=========================================================+
| cache.ttl                 | 3600     || Total time in seconds that objects will be kept in     |
|                           |          || the cache                                              |
+---------------------------+----------+---------------------------------------------------------+
| cache.tti                 | 1800     || Time in seconds that objects will be kept in the       |
|                           |          || cache wihout being used                                |
+---------------------------+----------+---------------------------------------------------------+
| cache.maxElementsInMemory | 250      || Maximum number of objects kept in memory               |
|                           |          || by the cache                                           |
+---------------------------+----------+---------------------------------------------------------+
| attributesToReturn        | [#]_     || List of fields used by MongoDB queries                 |
+---------------------------+----------+---------------------------------------------------------+

Properties prefix: ``studio.social.system.emailConfig.``

+---------------------------+----------+---------------------------------------------------------+
| Property                  || Default | Description                                             |
|                           || Value   |                                                         |
+===========================+==========+=========================================================+
| cache.ttl                 | 3600     || Total time in seconds that objects will be kept in     |
|                           |          || the cache                                              |
+---------------------------+----------+---------------------------------------------------------+
| cache.tti                 | 1800     || Time in seconds that objects will be kept in the       |
|                           |          || cache wihout being used                                |
+---------------------------+----------+---------------------------------------------------------+
| cache.maxElementsInMemory | 250      || Maximum number of objects kept in memory               |
|                           |          || by the cache                                           |
+---------------------------+----------+---------------------------------------------------------+

Properties prefix: ``studio.social.system.tentanConfig.``

+---------------------------+----------+---------------------------------------------------------+
| Property                  || Default | Description                                             |
|                           || Value   |                                                         |
+===========================+==========+=========================================================+
| cache.ttl                 | 3600     || Total time in seconds that objects will be kept in     |
|                           |          || the cache                                              |
+---------------------------+----------+---------------------------------------------------------+
| cache.tti                 | 1800     || Time in seconds that objects will be kept in the       |
|                           |          || cache wihout being used                                |
+---------------------------+----------+---------------------------------------------------------+
| cache.maxElementsInMemory | 250      || Maximum number of objects kept in memory               |
|                           |          || by the cache                                           |
+---------------------------+----------+---------------------------------------------------------+

Properties prefix: ``studio.social.system.cors.``

Using these properties you can control the `Cross-Origin Resource Sharing` settings to assure
your sites and applications are able to access the Crafter Social API.

+-------------------------------+---------------------+------------------------------------------+
| Property                      | Default Value       | Description                              |
+===============================+=====================+==========================================+
| disableCORS                   | false               || If set to ``true`` CORS headers will    |
|                               |                     || not be added to any response            |
+-------------------------------+---------------------+------------------------------------------+
| accessControlAllowHeaders     | x-requested-with    || Value for the header                    |
|                               |                     || ``Access-Control-Allow-Headers``        |
+-------------------------------+---------------------+------------------------------------------+
| accessControlAllowMethods     | POST,GET,PUT,DELETE || Value for the header                    |
|                               |                     || ``Access-Control-Allow-Methods``        |
+-------------------------------+---------------------+------------------------------------------+
| accessControlMaxAge           | 3600                || Value for the header                    |
|                               |                     || ``Access-Control-Max-Age``              |
+-------------------------------+---------------------+------------------------------------------+
| accessControlAllowOrigin      | *                   || Value for the header                    |
|                               |                     || ``Access-Control-Allow-Origin``         |
+-------------------------------+---------------------+------------------------------------------+
| accessControlAllowCredentials | false               || Value for the header                    |
|                               |                     || ``Access-Control-Allow-Credentials``    |
+-------------------------------+---------------------+------------------------------------------+

.. _crafter-social-av:

^^^^^^^^^^^^^^^^^^^^^^^^^^^
Virus Scanner Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Since users of your site or application will be able to upload files into the Crafter Social database
it is recommended to setup an antivirus for additional protection of both the server and the
client machines. By default Crafter Social is configured to use an empty implementation of the
``VirusScanner``, in a production environment you should follow these steps to enable it:

"""""""""""""""""""""""""""
Enable ClamAV Virus Scanner
"""""""""""""""""""""""""""

1. Install ClamAV
2. Edit the ClamAV configuration file to include the following properties:

.. code-block:: text
  :caption: clamd.conf
  :linenos:

  # The values provided are just an example, they are fine for a local test but you should
  # use the appropriate values for production

  TCPSocket 3310
  TCPAddr 127.0.0.1

3. Start the ``clamd`` daemon
4. Add the following file:

.. NOTE::
  If you are going to use the default ClamAV values (localhost:3310) the only change needed is the
  property ``studio.social.ugc.virusScanner=org.craftercms.virusscanner.impl.ClamavVirusScannerImpl``.

.. code-block:: xml
  :caption: $TOMCAT/shared/classes/crafter/social/extension/virus-scanner-context.xml
  :linenos:

  <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

      <bean id="social.ugc.virusScanner" class="org.craftercms.virusscanner.impl.ClamavVirusScannerImpl">
        <property name="host" value="localhost"/>
        <property name="port" value="3310"/>
        <property name="timeout" value="60000"/>
      </bean>

    </beans>

5. Reload the Crafter Social WAR file or restart the Tomcat server

""""""""""""""""""""""""""""
Using a Custom Virus Scanner
""""""""""""""""""""""""""""

If you need to use a different antivirus software you can create a class that implements the
``org.craftercms.virusscanner.api.VirusScanner`` interface. Your custom class should:

1. Define all configuration needed by the antivirus
2. Execute the antivirus scan for individual files
3. Throw a ``org.craftercms.virusscanner.impl.VirusScannerException`` if any threat is detected

.. rubric:: Notes

.. [#] - Default Value: ``mongodb://localhost:27017/craftersocial?readPreference=primary&maxPoolSize=150&minPoolSize=50&maxIdleTimeMS=1000&waitQueueMultiple=200&waitQueueTimeoutMS=1000&w=1&journal=true``
       - You can find more information about the format and parameters here: https://docs.mongodb.com/manual/reference/connection-string/

.. [#] - Default Value: ``classpath:/crafter/social/``
       - You can add any custom MongoDB script, more information: https://docs.mongodb.com/manual/tutorial/write-scripts-for-the-mongo-shell/

.. [#] - Default Value: ``org.craftercms.virusscanner.impl.NullVirusScannerImpl``
       - You can extend Crafter Social to use a custom virus scanner or use the provided solution with ClamAV.

.. [#] - Default Value: ``org.craftercms.social.repositories.social.SocialUgcFactory``
       - You can extend Crafter Social to include custom fields or logic when creating User Generated Content.

.. [#] - Default Value: ``displayName,avatarLink,socialContexts,notificationLocale,autoWatch,defaultFrequency,isAlwaysAnonymous``
       - Crafter Social will expect those fields to be available in all profiles, you need to make sure they are included in all
         tenants that are needed by your site or application.

.. _social-admin-console:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Crafter Social Admin Console UI
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Crafter Social Admin Console consists of a single WAR file that depends on access to both Crafter
Social and Crafter Profile. This web application provides a simple way to manage all data related
to permissions and user generated content without the need to call the :ref:`crafter-social-api`
directly.

"""""""""""""""""""
Configuration Guide
"""""""""""""""""""

Similar to other CrafterCMS components you can configure the Social Admin Console using a simple
properties file placed in the following location:

  ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/social/management/extension/server-config.properties``

You can change any of the default configuration, some of the more relevant properties are:

.. code-block:: properties

  crafter.social.app.rootUrl=
  crafter.social.app.name=crafter-social

  crafter.profile.rest.client.url.base=http://localhost:8080/crafter-profile
  crafter.profile.rest.client.accessToken.id=e8f5170c-877b-416f-b70f-4b09772f8e2d

~~~~~~~~~~
Properties
~~~~~~~~~~

+-------------------------------+----------------------------------------------------------------+
| Property                      | Description                                                    |
+===============================+================================================================+
| crafter.social.app.rootUrl    || URL where Crafter Social is deployed, if its empty then it is |
|                               || assumed that both WAR files are deployed in the same server   |
+-------------------------------+----------------------------------------------------------------+
| crafter.social.app.name       || Name of the Crafter Social WAR file                           |
+-------------------------------+----------------------------------------------------------------+
| ..rest.client.url.base        || URL where Crafter Profile is deployed, can be an              |
|                               || external server                                               |
+-------------------------------+----------------------------------------------------------------+
| ...rest.client.accessToken.id || Access Token used by the Admin Console application,           |
|                               || can be changed in the first login                             |
+-------------------------------+----------------------------------------------------------------+

"""""""""""""""""""""""""""
Accessing the Admin Console
"""""""""""""""""""""""""""
Once the WAR file has been deployed, you can access the application in the following URL:
``HOST:PORT/crafter-social-admin``

.. figure:: /_static/images/social-admin/login.webp
  :align: center
  :width: 25%
  :alt: Crafter Social Admin Console Login

  Crafter Social Admin Console login dialog.

By default there is only one user created:

.. code-block:: none

  Username: admin
  Password: admin

""""""""""""""""""""""""""
Social Contexts Management
""""""""""""""""""""""""""
Crafter Social provides the concept of context to allow a flexible management of permissions
for each one of the sites or applications where the users will be creating the content.

~~~~~~~~
List All
~~~~~~~~
To list all existing Social Contexts you can click the ``Contexts`` link in the left sidebar.

.. figure:: /_static/images/social-admin/contexts.webp
  :align: center
  :alt: Crafter Social contexts

~~~~~~~~~~~~~~~~~~
Create New Context
~~~~~~~~~~~~~~~~~~
To create a new Social Context you only need to provide a name in the input and then click the add
button.

.. figure:: /_static/images/social-admin/contexts-new.webp
  :align: center
  :alt: Crafter Social new contexts

An ID will be generated automatically and this is the value that need to be included in the requests
by the Crafter Social clients.

"""""""""""""""""""""""""""
Security Actions Management
"""""""""""""""""""""""""""
All roles are associated with security actions, when a client tries to create or update content
Crafter Social will validate the action against the user roles.

~~~~~~~~
List All
~~~~~~~~
To view all existing Security Actions you can click the ``Security Actions`` link in the left sidebar.

.. figure:: /_static/images/social-admin/security.webp
  :align: center
  :alt: Crafter Social security

All actions starting with the prefix ``system.`` are used by Crafter Social and are not available
for updates.

~~~~~~~~~~~~~~~~~~~~~~~
Update Security Actions
~~~~~~~~~~~~~~~~~~~~~~~
Using this page you can change the actions allowed to the Social Roles or add your own roles too.
You only need to change the roles indicated in the right input and press the update button. Any
change in this configuration will take effect immediately for all Crafter Social clients.

""""""""""""""""""""""
Preferences Management
""""""""""""""""""""""
.. WARNING::
  Most preferences managed from the Crafter Social Admin Console are specific for each Social
  Context, please be sure to select the right context from the dropdown before saving any change.

~~~~~~~~~~~~~~~~~~~~~~~~~
Notifications Preferences
~~~~~~~~~~~~~~~~~~~~~~~~~
You can access the email notifications by clicking the ``Notification Preferences`` link in the
left sidebar.

.. figure:: /_static/images/social-admin/preferences.webp
  :align: center
  :alt: Crafter Social Admin Console Preferences

'''''''''''''''
Email Templates
'''''''''''''''
This section includes Context specific templates for all supported event notifications

+------------------+---------------------------------------------------------------+
| Event            |  Description                                                  |
+==================+===============================================================+
| Instant          || New changes from all subscribed threads (Individual)         |
+------------------+---------------------------------------------------------------+
| Daily            || New changes from all subscribed threads (Aggregated by day)  |
+------------------+---------------------------------------------------------------+
| Weekly           || New changes from all subscribed threads (Aggregated by week) |
+------------------+---------------------------------------------------------------+
| Approver Email   || New changes that need to be moderated                        |
+------------------+---------------------------------------------------------------+
| Approve UGC Page || New changes that have been approved                          |
+------------------+---------------------------------------------------------------+

All email templates need to be valid HTML pages and can use any feature from Freemarker.

.. code-block:: html
  :force:
  :caption: Example Email Template
  :linenos:

  <html>
    <head>
      <title></title>
    </head>
    <body>
      <p>Hi ${profile.username} this are changes that happen on your subscribe Threads</p>
      <#list digest as change>
        <h1>${change["_id"]}</h1>

        <dl>
          <#list change.ugcList as ugc>
            <dt>Subject</dt>
            <dd>${ugc.subject!""}</dd>
            <dt>Body</dt>
            <dd>${ugc.body!""}</dd>
            <dt>Changed by</dt>
            <dt></dt>
            <dd>${ugc.lastModifiedBy.username}</dd>
            <dd></dd>
          </#list>
        </dl>
      </#list>
    </body>
  </html>

'''''''''''''''''''
Email Configuration
'''''''''''''''''''
This section includes the basic configuration that applies to all Social Contexts

+--------------------+---------------------------------------------------------------+
| Property           |  Description                                                  |
+====================+===============================================================+
| Server Host        || SMTP server for sending email notifications                  |
+--------------------+---------------------------------------------------------------+
| Port               || SMTP port using for connection to the server                 |
+--------------------+---------------------------------------------------------------+
| Use Authentication || If enabled the username and passwords will be used           |
+--------------------+---------------------------------------------------------------+
| Username           || Authentication used for connections to the server            |
+--------------------+                                                               +
| Password           ||                                                              |
+--------------------+---------------------------------------------------------------+
| Use TLS            || If enabled the connection will be secured                    |
+--------------------+---------------------------------------------------------------+
| Reply To           || Email address used by users for replies                      |
+--------------------+---------------------------------------------------------------+
| From               || Email address used to send all email notifications           |
+--------------------+---------------------------------------------------------------+
| Email Priority     || Value goes from 1 (highest) to 5 (lowest)                    |
+--------------------+---------------------------------------------------------------+
| Subject            || Subject used for all content change notifications            |
+--------------------+---------------------------------------------------------------+
| Encoding           || Encoding used for sending the email body                     |
+--------------------+---------------------------------------------------------------+

.. _social-admin-tenant-preferences:

~~~~~~~~~~~~~~~~~~
Tenant Preferences
~~~~~~~~~~~~~~~~~~
These preferences allow you change the behavior of Crafter Social depending on the Social Context,
for example one context could send daily notifications and others send them weekly instead. You can
access them by clicking the ``Tenant Preferences`` link in the left sidebar.

.. figure:: /_static/images/social-admin/preferences-tenant.webp
  :align: center
  :alt: Crafter Social Admin tenant preferences

''''''''''
Properties
''''''''''
+-----------------------+----------------------+-------------------------------------------------+
| Property              | Default Value        | Description                                     |
+=======================+======================+=================================================+
| baseUrl               || myDomain.com        || URL for the server used in the                 |
|                       ||                     || email templates                                |
+-----------------------+----------------------+-------------------------------------------------+
| defaultFrequency      || INSTANT             || Frequency for sending the email                |
|                       ||                     || notifications                                  |
+-----------------------+----------------------+-------------------------------------------------+
| hiddenUgcStatus       || SPAM,TRASH          || List of status that should not appear          |
|                       ||                     || in the email notifications                     |
+-----------------------+----------------------+-------------------------------------------------+
| moderateByMailEnable  || false               || If enabled moderation emails will be           |
|                       ||                     || sent when new content is created               |
+-----------------------+----------------------+-------------------------------------------------+
| moderateByMailRole    || SOCIAL_APPROVER     || All users with this role will receive          |
|                       ||                     || the moderation emails                          |
+-----------------------+----------------------+-------------------------------------------------+
| moderateByMailSubject || A new Comment needs || Subject to use for the moderation              |
|                       || to be approved      || emails                                         |
+-----------------------+----------------------+-------------------------------------------------+
| setupAutoWatch        || false               || If enabled users will be automatically         |
|                       ||                     || subscribed to all content they create          |
+-----------------------+----------------------+-------------------------------------------------+
| timezone              || EST                 || Timezone used to format dates in               |
|                       ||                     || notifications                                  |
+-----------------------+----------------------+-------------------------------------------------+

'''''''''''''''''
Custom Properties
'''''''''''''''''
You can also extend Crafter Social to add custom business rules, in which case you can also
include custom configuration from the Admin Console. When you click the ``Add`` button you can
set new properties with any name and value. If you are not using a custom Crafter Social WAR all
custom properties will be ignored.

.. figure:: /_static/images/social-admin/preferences-tenant-new.webp
  :align: center
  :width: 55%
  :alt: Crafter Social new tenant preferences

"""""""""""""""""""
Profiles Management
"""""""""""""""""""
~~~~~~~~~~~~~~~
Search Profiles
~~~~~~~~~~~~~~~
To find a specific profile you can click the ``Search Profiles`` link in the left sidebar.

.. figure:: /_static/images/social-admin/profiles.webp
  :align: center
  :alt: Crafter Social search profiles

In the search page you need to select the right tenant from the dropdown, indicate the username
of the profile and then press the search button.

In this page you can find basic information and manage the social roles for the profiles, if you
need to change anything else in the profile you need to use the :ref:`crafter-profile-admin-console`.

~~~~~~~~~~~~
Update Roles
~~~~~~~~~~~~

From the search page you can click the ID of the profile to see the details:

.. figure:: /_static/images/social-admin/profile.webp
  :align: center
  :alt: Crafter Social profile

To change the roles you need to select the right Context from the dropdown and indicate *all* the
roles you want to set for the user.

''''''''''''
Social Roles
''''''''''''

+-------------------+----------------------------------------------------------------------------+
| Role              | Description                                                                |
+===================+============================================================================+
| SOCIAL_SUPERADMIN || This is the only role allowed to create new Social Contexts               |
+-------------------+----------------------------------------------------------------------------+
| SOCIAL_ADMIN      || Provides complete access to manage data in Crafter Social                 |
+-------------------+----------------------------------------------------------------------------+
| SOCIAL_MODERATOR  || Provides access to view and manage the new content that needs             |
|                   || moderation                                                                |
+-------------------+----------------------------------------------------------------------------+
| SOCIAL_APPROVER   || Users with this role will receive emails when the Email Moderation        |
|                   || feature is enabled                                                        |
+-------------------+----------------------------------------------------------------------------+
| SOCIAL_USER       || Provides access to create and update new content                          |
+-------------------+----------------------------------------------------------------------------+
| ANONYMOUS         || Special role used in request when no user has logged in and there is      |
|                   || no profile available                                                      |
+-------------------+----------------------------------------------------------------------------+

"""""""""""""""""""""""""""""""""
User Generated Content Moderation
"""""""""""""""""""""""""""""""""
In order to assure the quality of the content that users will be able to see in the site or
application, all user generated content should go through the moderation process. In this process
one or more moderators will be able to review the new content and take the appropriate decision if
it should be accepted or not.

You can start the moderation process by clicking the ``Moderation Dashboard`` link in the left
sidebar.

.. figure:: /_static/images/social-admin/moderation.webp
  :align: center
  :alt: Crafter Social moderation

When users submit new content, it will automatically be listed in this page. Remember to select
the right Social Context from the dropdown before making any changes.

~~~~~~~~~~~~~~~~~~
Moderation Process
~~~~~~~~~~~~~~~~~~
Content that goes through the moderation process will change according to a set of status and
depending on the status of the content, the moderators will have different actions available.

'''''''''''
Unmoderated
'''''''''''
New content that no one has reviewed. Items in this status will not be visible for the end users
and is available for updates from the moderators in order to remove inappropriate content.

.. figure:: /_static/images/social-admin/moderation-unmoderated.webp
  :align: center
  :alt: Crafter Social unmoderated

Actions
 - Approve
 - Mark as Spam
 - Mark as Trash
 - Save Changes
 - Reset

''''''''
Approved
''''''''
Content that has already been reviewed and accepted. Items in this status will be visible for the
end users but can still be updated or removed by the moderators.

.. figure:: /_static/images/social-admin/moderation-approved.webp
  :align: center
  :alt: Crafter Social moderation approved

Actions
 - Mark as Spam
 - Mark as Trash
 - Mark as Unmoderated
 - Save Changes
 - Reset

''''
Spam
''''
Content that has already been reviewed but was considered as irrelevant. Items in this state can
be set as ``Unmoderated`` again.

.. figure:: /_static/images/social-admin/moderation-spam.webp
  :align: center
  :alt: Crafter Social spam

Actions
 - Permanently delete
 - Mark as Unmoderated

'''''
Trash
'''''
Content that has already been reviewed but should be discarded. Items in this state can not be
recovered and the only option available is to delete them from the database.

.. figure:: /_static/images/social-admin/moderation-trash.webp
  :align: center
  :alt: Crafter Social trash

Actions
 - Permanently delete

|hr|

.. _crafter-social-api:

--------
REST API
--------
To view the Crafter Social REST APIs:

.. open_iframe_modal_button::
    :label: Open here
    :url: ../../_static/api/social.html
    :title: Social API

.. raw:: html

    or <a href="../../_static/api/social.html" target="_blank">in a new tab</a>

|

|hr|

-----------
Source Code
-----------

Crafter Social's source code is managed in GitHub: https://github.com/craftersoftware/craftercms/tree/support/4.x/social
