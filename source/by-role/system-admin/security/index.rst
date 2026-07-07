:is-up-to-date: True
:last-updated: 4.1.2

.. index:: Security, SAML, LDAP, Header-based, Headers-based, Headers Based, Authentication, Password, SSL, TLS, HTTPS, Secrets, Managing Secrets, External Secrets, Internal Secrets, Encryption, Encrypting Text in a Configuration File

.. meta::
   :description: Secure CrafterCMS with SAML2, LDAP, header-based authentication, HTTPS/TLS, managing secrets, changing default passwords, and encryption configuration.
   :keywords: security, SAML2, LDAP, HTTPS, TLS, encryption, authentication, secrets, CrafterCMS, system admin

.. _system-admin-security:

========
Security
========
.. _change-the-defaults:

-------------------
Change the Defaults
-------------------
CrafterCMS installations are pre-configured with default passwords, tokens, keys, etc. These default values are intended
for initial testing, installation and configuration. Failure to change these default values creates a critical security
vulnerability. To secure your CrafterCMS installation, **YOU MUST CHANGE THE PRE-CONFIGURED DEFAULT VALUES**.

Here are the parameters for which you must change the default values:

* Replace default values for configuration files encryption key and salt

     .. code-block:: sh
        :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*

        # -------------------- Encryption variables --------------------
        # These variables are used to encrypt and decrypt values inside the configuration files.
        export CRAFTER_ENCRYPTION_KEY=${CRAFTER_ENCRYPTION_KEY:="default_encryption_key"}
        export CRAFTER_ENCRYPTION_SALT=${CRAFTER_ENCRYPTION_SALT:="default_encryption_salt"}

     |

* Replace the default values for database values encryption key and salt. Remember that these values should **not** be changed after CrafterCMS has been started if the installation has one or more of the following:

  * Remote repository passwords and keys
  * Cluster passwords and keys

     .. code-block:: sh
        :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*

        # These variables are used by Studio to encrypt and decrypt values in the database.
        export CRAFTER_SYSTEM_ENCRYPTION_KEY=${CRAFTER_SYSTEM_ENCRYPTION_KEY:="s0meDefaultKey"}
        export CRAFTER_SYSTEM_ENCRYPTION_SALT=${CRAFTER_SYSTEM_ENCRYPTION_SALT:="s0meDefaultSalt"}

     |

* Replace default values for the management tokens used by Studio, Engine, Deployer, Search, Profile and Social

     .. code-block:: sh
        :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*

        # -------------------- Management tokens ----------------
        # Please update this per installation and provide these tokens to the status monitors.
        export STUDIO_MANAGEMENT_TOKEN=${STUDIO_MANAGEMENT_TOKEN:="defaultManagementToken"}
        export ENGINE_MANAGEMENT_TOKEN=${ENGINE_MANAGEMENT_TOKEN:="defaultManagementToken"}
        export DEPLOYER_MANAGEMENT_TOKEN=${DEPLOYER_MANAGEMENT_TOKEN:="defaultManagementToken"}
        export SEARCH_MANAGEMENT_TOKEN=${SEARCH_MANAGEMENT_TOKEN:="defaultManagementToken"}
        export PROFILE_MANAGEMENT_TOKEN=${PROFILE_MANAGEMENT_TOKEN:="defaultManagementToken"}
        export SOCIAL_MANAGEMENT_TOKEN=${SOCIAL_MANAGEMENT_TOKEN:="defaultManagementToken"}

     |

* Replace default values for the DB root password, the DB ``crafter`` user password and the DB replication user password (if running in a primary/replica cluster) before starting CrafterCMS for the very first time.

     .. code-block:: sh
        :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*

        # -------------------- MariaDB variables --------------------
        ...
        export MARIADB_ROOT_PASSWD=${MARIADB_ROOT_PASSWD:="root"}
        ...
        export MARIADB_PASSWD=${MARIADB_PASSWD:="crafter"}
        ...
        export MARIADB_REPLICATION_PASSWD=${MARIADB_REPLICATION_PASSWD:="crafter_replication"}

     |

  To change the values after the initial start of CrafterCMS, do the following:

  #. Manually change the DB passwords

     First, login to the database as root. From the command line in the server, go to ``CRAFTER_HOME/bin/dbms/bin`` and run the following command:

       .. code-block:: bash

          ./mariadb -u root -p --socket=/tmp/MariaDB4j.33306.sock

       |

     To change the ``root`` password, run the following command:

       .. code-block:: bash

          ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';

       |

     Remember to replace ``MyNewPass`` with the actual password you want to set, and if you are connecting to the DB from another host, change ``localhost`` with the remote hostname or IP address.

     To change the ``crafter`` user password, run the following command, similar to changing the root password.

       .. code-block:: bash

          ALTER USER 'crafter'@'localhost' IDENTIFIED BY 'MyNewCrafterPass';

       |

     Again, remember to replace ``MyNewCrafterPass`` with the actual password you want to set, and if you are connecting to the DB from another host, change ``localhost`` with the remote hostname or IP address.

     To change the ``crafter_replication`` replication user password, run the following command, similar to changing the root password.

       .. code-block:: bash

          ALTER USER 'crafter_replication'@'localhost' IDENTIFIED BY 'MyNewReplicationPass';

       |

     Again, remember to replace ``MyNewReplicationPass`` with the actual password you want to set, and if you are connecting to the DB from another host, change ``localhost`` with the remote hostname or IP address.

  #. Stop Studio
  #. Update the values in the configuration file ``crafter-setenv.sh`` with the new password used in the previous step

       .. code-block:: sh
        :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*

        # -------------------- MariaDB variables --------------------
        ...
        export MARIADB_ROOT_PASSWD=${MARIADB_ROOT_PASSWD:="MyNewPass"}
        ...
        export MARIADB_PASSWD=${MARIADB_PASSWD:="MyNewCrafterPass"}

     |

  #. Restart Studio

* Change the default Studio ``admin`` user password either by randomizing the ``admin`` password for a fresh install of Crafter Studio or by changing the password after logging in as user ``admin``. For more information on randomizing the admin password for a fresh install, see :ref:`randomize-admin-password`. For more information on changing user passwords, see :ref:`user-passwords`

* Set session cookies as ``HTTP Only`` and ``Secure`` by setting the flags to ``true`` in your tomcat ``web.xml`` file

  .. code-block:: xml
     :caption: *CRAFTER_HOME/bin/apache-tomcat/conf/web.xml*
     :emphasize-lines: 3-6
     :linenos:

     <session-config>
       <session-timeout>1</session-timeout>
       <cookie-config>
         <http-only>true</http-only>
         <secure>true</secure>
       </cookie-config>
     </session-config>

* Replace default values for all the variables used to control the access tokens used for Studio's API

  .. code-block:: sh
     :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*
     :linenos:
     :emphasize-lines: 16-19

     # -------------------- Studio's access tokens ---------------------
     # *************************************************************************************
     # ************************* IMPORTANT *************************************************
     # The following variables are used to control the access tokens used for Studio's API,
     # please replace all default values to properly secure your installation
     # *************************************************************************************

     # Issuer for the generated access tokens
     export STUDIO_TOKEN_ISSUER=${STUDIO_TOKEN_ISSUER:="Crafter Studio"}
     # List of accepted issuers for validation of access tokens (separated by commas)
     export STUDIO_TOKEN_VALID_ISSUERS=${STUDIO_TOKEN_VALID_ISSUERS:="Crafter Studio"}
     # The audience for generation and validation of access tokens (if empty the instance id will be used)
     export STUDIO_TOKEN_AUDIENCE=${STUDIO_TOKEN_AUDIENCE:=""}
     # Time in minutes for the expiration of the access tokens
     export STUDIO_TOKEN_TIMEOUT=${STUDIO_TOKEN_TIMEOUT:=5}
     # Password for signing the access tokens (needs to be equal or greater than 512 bits in length)
     export STUDIO_TOKEN_SIGN_PASSWORD=${STUDIO_TOKEN_SIGN_PASSWORD:="s0meDefaultTokenSignPasswd"}
     # Password for encrypting the access tokens
     export STUDIO_TOKEN_ENCRYPT_PASSWORD=${STUDIO_TOKEN_ENCRYPT_PASSWORD:="s0meDefaultTokenEncryptPasswd"}
     # Name of the cookie to store the refresh token
     export STUDIO_REFRESH_TOKEN_NAME=${STUDIO_REFRESH_TOKEN_NAME:="refresh_token"}
     # Time in seconds for the expiration of the refresh token cookie
     export STUDIO_REFRESH_TOKEN_MAX_AGE=${STUDIO_REFRESH_TOKEN_MAX_AGE:=300}
     # Indicates if the refresh token cookie should be secure (should be true for production environments behind HTTPS)
     export STUDIO_REFRESH_TOKEN_SECURE=${STUDIO_REFRESH_TOKEN_SECURE:="false"}

.. _configure-ssl-tls:

-----------------
Configure SSL/TLS
-----------------
To configure SSL/TLS for CrafterCMS authoring and delivery, do the following:

* Step 1: Create a keystore file

  .. code-block:: bash

     $JAVA_HOME/bin/keytool -genkey -alias tomcat -keyalg RSA

  |

* Step 2: Edit the tomcat file to use the keystore file by uncommenting or adding the "SSL HTTP/1.1 Connector" entry

* Step 3: Test your setup

From ``tomcat.apache.org`` :

   Transport Layer Security (TLS) and its predecessor, Secure Sockets Layer (SSL), are technologies which allow web browsers and web servers to communicate over a secured connection. This means that the data being sent is encrypted by one side, transmitted, then decrypted by the other side before processing. This is a two-way process, meaning that both the server AND the browser encrypt all traffic before sending out data.*

CrafterCMS employs two deployment methods, traditional deployment and serverless deployment. Let's take a look at an example of how to configure SSL/TLS in a traditional deployment and serverless (docker container) deployment:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Configuring SSL/TLS for CrafterCMS Authoring and Delivery in a Traditional Deployment with Tomcat
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _create-keystore-file:

""""""""""""""""""""""""""""""
Step 1: Create a Keystore file
""""""""""""""""""""""""""""""
Java's ``keytool`` file allows the user to create self signed certificates. For this example, we will be using a self signed certificate. Please remember that self signed certificates are not suitable for production use.

.. code-block:: bash

   $JAVA_HOME/bin/keytool -genkey -alias tomcat -keyalg RSA

   Enter keystore password:  password (it will be invisible)
   Re-enter new password: password
   What is your first and last name?
     [Unknown]:  {FIRST_LAST_NAME}
   What is the name of your organizational unit?
     [Unknown]:  {ORGANIZATIONAL_UNIT}
   What is the name of your organization?
     [Unknown]:  {ORGANIZATION_NAME}
   What is the name of your City or Locality?
     [Unknown]:  {CITY}
   What is the name of your State or Province?
     [Unknown]:  {STATE_PROVINCE}
   What is the two-letter country code for this unit?
     [Unknown]:  {COUNTRY_CODE}
   Is CN={FIRST_LAST_NAME}, OU={ORGANIZATIONAL_UNIT}, O={ORGANIZATION_NAME}, L={CITY}, ST={STATE_PROVINCE}, C={COUNTRY_CODE} correct?
     [no]:  yes

   Enter key password for
       (RETURN if same as keystore password):  password
   Re-enter new password: password

|

The command above will generate a file named ``.keystore`` in the users home directory. Take note of the location as it will be used in the next step.

.. _use-keystore-file-in-tomcat:

"""""""""""""""""""""""""""""""""""""""""""""""""
Step 2: Configure Tomcat to use the Keystore file
"""""""""""""""""""""""""""""""""""""""""""""""""
The next step is to configure SSL/TLS Connector in the authoring/delivery tomcat file by uncommenting/adding the following:

.. code-block:: xml
    :caption: CRAFTER_HOME/bin/apache-tomcat/conf/server.xml
    :linenos:

    <Connector
        protocol="org.apache.coyote.http11.Http11NioProtocol"
        port="8443"
        maxThreads="150"
        SSLEnabled="true">
        <SSLHostConfig>
            <Certificate
                certificateKeystoreFile="/path/to/your/keystore"
                certificateKeystorePassword="yourKeystorePassword"
                type="RSA"
            />
        </SSLHostConfig>
    </Connector>

where:

* **port** : port number to be secured by your new keystore file
* **certificateKeystoreFile** : path to your keystore file created from the previous step
* **certificateKeystorePassword** : password used when keystore file was created from the previous step

We'll use the above values for our CrafterCMS authoring example. Save the changes and restart CrafterCMS authoring/delivery.

For more information on configuring SSL/TLS on Tomcat, see https://tomcat.apache.org/tomcat-11.0-doc/ssl-howto.html

.. _ssl-test-your-setup:

"""""""""""""""""""""""
Step 3: Test your setup
"""""""""""""""""""""""
To test your CrafterCMS authoring, open your browser and type in:

   ``https://localhost:8443/studio``

Since we are using a self-signed certificate, you'll get a message similar to this depending on your browser:

.. figure:: /_static/images/system-admin/ssl-connection-not-private.webp
    :alt: Connection not private message using a self signed certificate
    :width: 80 %
    :align: center

From the above screen, just click on ``Advanced``, then allow it to proceed to ``localhost::8443``, and you will then be taken to the login screen of Crafter Studio

.. figure:: /_static/images/system-admin/ssl-login-not-secure.webp
    :alt: Connection not private message using a self signed certificate
    :width: 90 %
    :align: center

|

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Configuring SSL/TLS for CrafterCMS Authoring and Delivery in a Traditional Deployment with Apache HTTPd
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To configure SSL/TLS for CrafterCMS authoring and delivery in a traditional deployment with Apache HTTPd, follow the steps detailed at https://httpd.apache.org/docs/2.4/ssl/ssl_howto.html.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Configuring SSL/TLS for CrafterCMS Authoring and Delivery via a CDN
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SSL/TLS can also be configured at the CDN, here is an example of doing that using AWS CloudFront https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https.html.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Configuring SSL/TLS for CrafterCMS Authoring and Delivery in a Containerized Deployment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Setting up SSL/TLS for CrafterCMS authoring and delivery in a Docker Container is similar to the steps done for CrafterCMS Authoring and Delivery installed in a server, with just a few differences.

* Step 1: Create a keystore file

  .. code-block:: bash

     $JAVA_HOME/bin/keytool -genkey -alias tomcat -keyalg RSA

  |

* Step 2: Edit the tomcat file to use the keystore file by uncommenting or adding the "SSL HTTP/1.1 Connector" entry

* Step 3: Edit your ``docker-compose.yml`` file to setup connector port and certificate

* Step 4: Test your setup

Let's take a look at an example of a CrafterCMS authoring running in a docker container. For reference, here's the instruction for  :ref:`running-craftercms-in-docker`

""""""""""""""""""""""""""""""""
Step 1: Create the Keystore file
""""""""""""""""""""""""""""""""

Follow the step above :ref:`create-keystore-file` to create your keystore file. For convenience, copy the ``.keystore`` file where your ``docker-compose.yml`` files is, so your directory structure looks like:

.. code-block:: text

   CrafterCMS authoring
     |-- docker-compose.yml
     |-- .keystore

|

"""""""""""""""""""""""""""""""""""""""""""""""""
Step 2: Configure Tomcat to use the Keystore file
"""""""""""""""""""""""""""""""""""""""""""""""""
Get a copy of the ``server.xml`` file from your container by running the following command:

.. code-block:: bash

   docker cp tomcat_1:/opt/crafter/bin/apache-tomcat/conf/server.xml .

|

The command above will copy the ``server.xml`` file from your docker container to your current directory.
Follow the step above :ref:`use-keystore-file-in-tomcat` to configure SSL/TLS Connector in the ``server.xml`` file you just copied from the docker container.

Your directory should now contain the following:

.. code-block:: text

   CrafterCMS authoring
     |-- docker-compose.yml
     |-- .keystore
     |-- server.xml

|

See https://docs.docker.com/engine/reference/commandline/cp/ for more information on ``docker cp``

""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
Step 3: Setup connector port and Keystore file in docker-compose.yml
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
In your ``docker-compose.yml`` file, under ``tomcat``:

* Add port ``8443``
* Add the keystore file and the edited ``server.xml`` file to volumes

Your ``docker-compose.yml`` should look like below:

.. code-block:: yaml
    :linenos:
    :emphasize-lines: 25, 33-35

    version: '3.7'
    services:
      search:
      image: opensearchproject/opensearch:2.8.0
      ports:
        - 9201:9200
      environment:
        - discovery.type=single-node
        - bootstrap.memory_lock=true
        - plugins.security.disabled=true
        - "ES_JAVA_OPTS=-Xss1024K -Xmx1G"
      ulimits:
        memlock:
          soft: -1
          hard: -1
      volumes:
        - search_data:/usr/share/opensearch/data
        - search_logs:/usr/share/opensearch/logs

      tomcat:
        image: craftercms/authoring_tomcat:4.1.1 # craftercms version flag
        depends_on:
          - search
          - deployer
        ports:
          - 8080:8080
          - 8443:8443
        volumes:
          - crafter_data:/opt/crafter/data
          - crafter_logs:/opt/crafter/logs
          - crafter_temp:/opt/crafter/temp
          # Search dirs needed for backup/restore
          - search_data:/opt/crafter/data/indexes
          # SSL/TLS certificate
          - ./.keystore:/etc/ssl/certs/.keystore
          - ./server.xml:/opt/crafter/bin/apache-tomcat/conf/server.xml
        environment:
          - DEPLOYER_HOST=deployer
          - DEPLOYER_PORT=9191
          - ES_HOST=search
          - ES_PORT=9200

|

Restart your Docker container.

"""""""""""""""""""""""
Step 4: Test your setup
"""""""""""""""""""""""
To test your CrafterCMS authoring, open your browser and type in:

   ``https://localhost:8443/studio``

You should see similar screens as shown :ref:`above<ssl-test-your-setup>` when we setup SSL/TLS for a traditional deployment.

.. _managing-secrets:

----------------
Managing Secrets
----------------
There are a number of ways to manage secrets with CrafterCMS. The following sections describe the different options.

.. note::
        A note on avoiding secrets.
        Whenever possible, storing secrets should be avoided as a best practice. For example, if you're deploying to AWS or similar, it's possible to have role-based authentication and authorization at the service level and avoid having to store secrets related to said service in configuration files.

^^^^^^^^^^^^^^^^
External Secrets
^^^^^^^^^^^^^^^^
CrafterCMS supports the use of external secrets. This means that you can store your secrets in a separate location
and use these secrets in your configuration files. This is the recommended approach whenever possible.

For example, you can use AWS Secrets and solicit the secrets. You can also use a Vault server to store your secrets.
Secrets can then be injected into CrafterCMS via environment variables or system properties.

.. TODO: Show an example where secrets get injected via `crafter-setenv.sh`

^^^^^^^^^^^^^^^^
Internal Secrets
^^^^^^^^^^^^^^^^
When external secrets are not possible, CrafterCMS supports the use of encrypted internal secrets. This means that
you can store your secrets in the configuration files themselves, and these secrets will be encrypted.

.. note::
    The encryption keys are configurable and can be different per environment, having different keys for development,
    staging, and production environments.

CrafterCMS has a number of ways to encrypt secrets:

- Automatic encryption of secrets in configuration files
- Studio UI encryption tool

    - Project-level encryption tool
    - Admin UI encryption tool
- CLI encryption tool

""""""""""
Encryption
""""""""""
Encrypting secrets can be done automatically within Studio-managed configuration files, via the UI, or using a CLI.

The encryption algorithm used is PBE (Password Based Encryption) with AES, in which a key and a salt are
specified to generate the key used on encryption/decryption.

For encryption via Studio-managed configuration files and via the UI, Crafter Studio uses a default key and salt
for the encryption tool. To set the key and salt to desired values, in your Authoring installation directory,
open ``CRAFTER_HOME/bin/crafter-setenv.sh`` and modify the following values

.. code-block:: bash
   :caption: *CRAFTER_HOME/bin/crafter-setenv.sh*

   # -------------------- Encryption variables --------------------
   export CRAFTER_ENCRYPTION_KEY=${CRAFTER_ENCRYPTION_KEY:="default_encryption_key"}
   export CRAFTER_ENCRYPTION_SALT=${CRAFTER_ENCRYPTION_SALT:="default_encryption_salt"}

|


.. _encrypting-text-in-a-configuration-file:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Automatic Encryption of Secrets in Configuration Files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
This section details how to encrypt passwords, access keys or other sensitive information in a configuration file
managed through Crafter Studio.

Examples of project configuration files where encryption would make sense include:

 - Engine Project Configuration (``/config/engine/site-config.xml``)
 - Studio AWS Profiles (``/config/studio/aws/aws.xml``)
 - Studio Box Profiles (``/config/studio/box/box.xml``)

   .. version_tag::
       :label: Until
       :version: 4.2

 - Studio WebDAV Profiles (``/config/studio/webdav/webdav.xml``)

'''''''''''''''''''''''''''''''''''''''''
How to Encrypt Text in Configuration File
'''''''''''''''''''''''''''''''''''''''''
To encrypt passwords, access keys or other sensitive information in a configuration file managed through Crafter Studio:

* Open the configuration file that has the text/information that you would like to encrypt
* Find the entry you would like to encrypt and add the attribute ``encrypted=""``
* Click on the ``Encrypt Marked`` button to encrypt text
* Your sensitive text should now be encrypted and displayed with the attribute ``encrypted="true"`` and you may now save your file

'''''''
Example
'''''''
Let's take a look at an example of encrypting the ``accessKey`` and ``secretKey`` for the AWS Profiles configuration.

* Open the ``AWS Profiles`` configuration file by clicking on |projectTools| -> ``Configuration``, then select ``AWS Profiles`` from the dropdown box
* We will add an ``AWS S3 profile``. Notice that the ``accessKey`` and ``secretKey`` are in the clear.

  .. code-block:: xml
     :caption: *{REPOSITORY_ROOT}/sites/SITENAME/config/studio/aws/aws.xml*
     :linenos:
     :emphasize-lines: 20,21

     <?xml version="1.0" encoding="UTF-8"?>
     <aws>
       <s3>
       <!--

       AWS S3 Profile

       Additional properties:

       <bucketName/>
       <pathStyleAccess/>

       bucketName: name of the bucket where files will be uploaded
       pathStyleAccess: indicates if path style access should be used for all requests (defaults to false)

       -->
         <profile>
           <id>s3-default</id>
             <credentials>
             <accessKey>YOUR_ACCESS_KEY</accessKey>
             <secretKey>YOUR_SECRET_KEY</secretKey>
           </credentials>
           <region>us-west-1</region>
           <bucketName>sample-input-bucket</bucketName>
           <pathStyleAccess>true</pathStyleAccess>
         </profile>
       </s3>
     </aws>

* We will now mark items to be encrypted by adding the attribute ``encrypted=""``. For our example, we will mark ``accessKey`` and ``secretKey`` for encryption.

  .. code-block:: xml
     :caption: *{REPOSITORY_ROOT}/sites/SITENAME/config/studio/aws/aws.xml*

     <accessKey encrypted="">YOUR_ACCESS_KEY</accessKey>
     <secretKey encrypted="">YOUR_SECRET_KEY</secretKey>

  |

  .. image:: /_static/images/site-admin/config-encrypt-text-1.webp
     :align: center
     :alt: Add "encrypted=""" attribute to "accessKey" and "secureKey"

  |

* Click on the ``Encrypt Marked`` button to encrypt the marked items, the attribute for the marked items will change to ``encrypted="true"``:

  .. code-block:: xml
     :caption: *{REPOSITORY_ROOT}/sites/SITENAME/config/studio/aws/aws.xml*

     <accessKey encrypted="true">${enc:ENCRYPTED_ACCESS_KEY}</accessKey>
     <secretKey encrypted="true">${enc:ENCRYPTED_SECRET_KEY}</secretKey>

  |

  .. image:: /_static/images/site-admin/config-encrypt-text-2.webp
     :align: center
     :alt: "accessKey" and "secureKey" now encrypted

  |

* The ``accessKey`` and ``secretKey`` are now encrypted and will be decrypted by Crafter Studio as needed

|hr|

.. _studio-encryption-tool:

~~~~~~~~~~~~~~~~~~~~~~~~~
Studio UI Encryption Tool
~~~~~~~~~~~~~~~~~~~~~~~~~
Crafter Studio provides an encryption tool for encrypting configuration properties like access keys or password, to keep these sensitive data, available only to developers and administrators.

There are two ways to access the encryption tools in Studio. Via Studio's ``Navigation Menu`` and a project's ``Project Tools``

To access the encryption tool via Studio's ``Navigation Menu``, from the top right of your browser, click on the ``Navigation Menu`` icon |mainMenu|, then click on ``Encryption Tool`` under *Global*.

.. image:: /_static/images/system-admin/main-menu/main-menu-encryption-tool.webp
    :alt: System Administrator - Main Menu Encryption Tool
    :align: center
    :width: 100%

|

To access the encryption tool from a project's ``Project Tools``, open the Sidebar and click on |projectTools|,
then click on ``Encryption Tool``.

.. image:: /_static/images/system-admin/project-tools-encryption-tool.webp
    :alt: Project Tools - Encryption Tool
    :align: center
    :width: 80%

|

To encrypt a password, access key, etc., simply enter the password, access key, etc. in the ``Raw Text`` field, then click on ``Encrypt Text``. This will generate the encrypted password, access key, etc. that you then simply need to paste in the configuration file.

Crafter Studio also provides the encryption tool to encrypt passwords, access keys or other sensitive information in a configuration file managed through Crafter Studio in |projectTools| -> ``Configuration``. See :ref:`encrypting-text-in-a-configuration-file` for more information.

'''''''
Example
'''''''
Let's take a look at an example of using the Studio encryption tool to encrypt a password. From the ``Navigation Menu``, click on ``Global Config`` and scroll down to the ``SMTP Configuration (Email)`` section. We're going to encrypt the value for ``studio.mail.password:``

.. code-block:: yaml
   :caption: *CRAFTER_HOME/data/repos/global/configuration/studio-config-override.yaml*
   :emphasize-lines: 13

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
   # Turn on/off (value true/false) SMTP authenticated access protocol.
   # studio.mail.smtp.auth: false
   # Enable/disable (value true/false) SMTP TLS protocol when sending emails.
   # studio.mail.smtp.starttls.enable: false
   # Enable/disable (value true/false) SMTP EHLO protocol when sending emails.
   # studio.mail.smtp.ehlo: true
   # Enable/disable (value true/false) debug mode for email service. Enabling debug mode allows tracking/debugging communication between email service and SMTP server.
   # studio.mail.debug: false

|

Let's begin:

#. Encrypt the password

   * To encrypt the ``studio.mail.password``, click on |mainMenu|, then click on ``Encryption Tool``
   * Enter the password in the ``Raw Text`` field

     .. image:: /_static/images/system-admin/main-menu/main-menu-encryption-tool-raw-text.webp
        :alt: System Administrator - Main Menu Encryption Tool Enter Raw Text
        :align: center
        :width: 70%

   * Click on the ``Encrypt Text`` button. The encrypted text will be displayed below the ``Raw Text`` field and copied onto the clipboard

     .. image:: /_static/images/system-admin/main-menu/main-menu-encryption-text-encrypted.webp
        :alt: System Administrator - Main Menu Encryption Tool Text Encrypted
        :align: center
        :width: 70%

#. Use the encrypted password

   * Click on |mainMenu|, then click on ``Global Config`` and scroll down to the ``SMTP Configuration (Email)`` section.
   * Paste the encrypted password

     .. code-block:: yaml
        :caption: *CRAFTER_HOME/data/repos/global/configuration/studio-config-override.yaml*

        # SMTP password for authenticated access when sending emails.
        studio.mail.password: ${enc:q2gqrm8R6Z0Xg77J6wzHH4i4qqMSlrcFcSkshS+RZ9s=}

     |

#. Your password is now encrypted and will be decrypted by Crafter Studio as needed.

|hr|

.. _crafter-commons-encryption-tool:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Command Line Interface (CLI) Encryption Tool
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Crafter Commons provides a command line tool that can be used to encrypt/decrypt text. It's especially useful for encrypting
values that will be used in configuration files. The encryption algorithm used is PBE (Password Based Encryption) with AES,
in which a password and a salt are specified to generate the key used on encryption/decryption.

Using the tool is very simple. First build the Crafter Commons source code and go to the ``target`` folder of the ``utilities``
sub-module, where you should find the JAR with the ``-enctool`` suffix. Then you can run any of the following commands:

- **Encode text in Base 64:** ``java -jar {JARNAME} -e64 CLEAR_TEXT``

	.. code-block:: bash

		java -jar crafter-commons-utilities-3.0.1-enctool.jar -e64 KniOddyi
		Encoded text in Base 64: S25pT2RkeWk=

- **Encrypt text:** ``java -jar {JARNAME} -e CLEAR_TEXT -p PASSWORD -s BASE64_SALT``

	.. code-block:: bash

		java -jar crafter-commons-utilities-3.0.1-enctool.jar -e c852cb30cda311e488300800200c9a66 -p klanFogyetkonjo -s S25pT2RkeWk=
		Cipher text (in Base 64): VkHxBsaSZ9DXrXk52uK9And+CEZlqiy7Wb23GxBFOSXD6KBOCh1ojp8fUw7w11IxpxBipiI4HsSg3cdl9TgTQg==

""""""""""
Decryption
""""""""""
CrafterCMS will automatically decrypt secrets as it needs them. If, however, you wanted to decrypt a secret manually you can use the CLI encryption/decryption tool:

- **Decode Base 64 text:** ``java -jar {JARNAME} -d64 BASE64_TEXT``

	.. code-block:: bash

		java -jar crafter-commons-utilities-3.0.1-enctool.jar -d64 S25pT2RkeWk=
		Decoded Base 64 text: KniOddyi

- **Decrypt text:** ``java -jar {JARNAME} -d CIPHER_TEXT -p PASSWORD -s BASE64_SALT``

  .. code-block:: bash

	 java -jar crafter-commons-utilities-3.0.1-enctool.jar -d VkHxBsaSZ9DXrXk52uK9And+CEZlqiy7Wb23GxBFOSXD6KBOCh1ojp8fUw7w11IxpxBipiI4HsSg3cdl9TgTQg== -p klanFogyetkonjo -s S25pT2RkeWk=
	 Clear text: c852cb30cda311e488300800200c9a66

-------------------------
Configure Studio Security
-------------------------
Crafter Studio supports a number of authentication methods to ensure secure access. Learn more about securing Crafter Studio in the article :ref:`studio-security`.

|hr|

-------------------------
Configure Engine Security
-------------------------
Crafter Engine supports a number of authentication methods to ensure secure access. Learn more about securing Crafter Engine in the article :ref:`engine-security`.

|hr|

