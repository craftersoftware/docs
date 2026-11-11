:is-up-to-date: True
:last-updated: 4.7.0

.. index:: Configuration, Ports, Hostnames, Apache HTTPd, Configure Reverse Proxy

.. meta::
   :description: CrafterCMS system configuration guide covering security, project delivery setup, reverse proxy, ports, hostnames, and serverless deployment options.
   :keywords: system configuration, CrafterCMS, security, delivery setup, serverless, Apache HTTPd, reverse proxy, system admin

.. _system-admin-configuration:

=============
Configuration
=============
This section describes how to configure CrafterCMS.

---------------------
General Configuration
---------------------
^^^^^^^^
Security
^^^^^^^^
To secure your CrafterCMS install, please see the article :ref:`system-admin-security`.

|hr|

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Setup Project in a Delivery Environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Setting up a project or site for delivery can be done for traditional deployments or serverless deployments. Furthermore, a project or site can be set up as ``staging`` or ``live``.

For traditional delivery, follow the article :ref:`setup-project-for-delivery`, and for serverless delivery, follow the article :ref:`setup-serverless-delivery`.

|hr|

^^^^^^^^^^^^^^^^^^^
Ports and Hostnames
^^^^^^^^^^^^^^^^^^^
"""""""""""""""""""""""""""
Default Ports and Hostnames
"""""""""""""""""""""""""""
CrafterCMS uses the following default ports and hostnames:

.. list-table::
    :header-rows: 1

    * - Module
      - Port
      - Hostname
    * - Studio
      - 8080 |br| 33306 |br| 5701
      - localhost
    * - Engine
      - 8080 |br| 9080
      - localhost
    * - Deployer
      - 9191 |br| 9192
      - localhost
    * - Search
      - 9201 |br| 9202
      - localhost
    * - Profile |enterpriseOnly|
      - 8080 |br| 9080
      - localhost
    * - Social |enterpriseOnly|
      - 8080 |br| 9080
      - localhost

.. _change-ports-and-hostnames:

""""""""""""""""""""""""""
Change Ports and Hostnames
""""""""""""""""""""""""""

There are times when you need to change the ports and/or hostnames in your CrafterCMS installation.
Changes to the ports and/or hostnames may be required in the following cases:

- If you need to change the port that an application in the current installation binds to.
- If you need the applications in the current installation to communicate with another application that's in a different host and/or port.

All the hostnames and ports used for communication by CrafterCMS applications are defined in
``CRAFTER_HOME/bin/crafter-setenv.sh``. You can either modify the file directly or set the environment variables (which is much cleaner). The environment variables are noted in the file, for example, ``MAIL_HOST`` and ``MAIL_PORT`` are used to set the hostname and port for the mail server and CrafterCMS will use those environment variables if present before using the defaults.

.. _authoring-environment-ports-and-hostnames:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Authoring Environment Ports and Hostnames
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The following is an example of the default values for the hostnames and ports used by the CrafterCMS authoring applications ``AUTHORING_INSTALL_DIR/bin/crafter-setenv.sh``

.. code-block:: bash
    :caption: *AUTHORING_INSTALL_DIR/bin/crafter-setenv.sh hostnames and ports with defaults*
    :linenos:

    export MAIL_HOST=${MAIL_HOST:="localhost"}
    export MAIL_PORT=${MAIL_PORT:="25"}
    export SEARCH_HOST=${SEARCH_HOST:="localhost"}
    export SEARCH_PORT=${SEARCH_PORT:="9201"}
    export DEPLOYER_HOST=${DEPLOYER_HOST:="localhost"}
    export DEPLOYER_PORT=${DEPLOYER_PORT:="9191"}
    # MongoDB is used only by Crafter Profile / Crafter Social (enterprise)
    export MONGODB_HOST=${MONGODB_HOST:="localhost"}
    export MONGODB_PORT=${MONGODB_PORT:="27020"}
    export MARIADB_HOST=${MARIADB_HOST:="127.0.0.1"}
    export MARIADB_PORT=${MARIADB_PORT:="33306"}
    export TOMCAT_HOST=${TOMCAT_HOST:="localhost"}
    export TOMCAT_HTTP_PORT=${TOMCAT_HTTP_PORT:="8080"}
    export TOMCAT_HTTPS_PORT=${TOMCAT_HTTPS_PORT:="8443"}
    export TOMCAT_AJP_PORT=${TOMCAT_AJP_PORT:="8009"}
    export TOMCAT_SHUTDOWN_PORT=${TOMCAT_SHUTDOWN_PORT:="8005"}
    export TOMCAT_DEBUG_PORT=${TOMCAT_DEBUG_PORT:="8000"}

.. _delivery-environment-ports-and-hostnames:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Delivery Environment Ports and Hostnames
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The following is an example of the default values for the hostnames and ports used by the CrafterCMS delivery applications ``DELIVERY_INSTALL_DIR/bin/crafter-setenv.sh``:

.. code-block:: bash
    :caption: *DELIVERY_INSTALL_DIR/bin/crafter-setenv.sh hostnames and ports with defaults*
    :linenos:

    # -------------------- hostnames and ports --------------------
    export MAIL_HOST=${MAIL_HOST:="localhost"}
    export MAIL_PORT=${MAIL_PORT:="25"}
    export SEARCH_HOST=${SEARCH_HOST:="localhost"}
    export SEARCH_PORT=${SEARCH_PORT:="9202"}
    export DEPLOYER_HOST=${DEPLOYER_HOST:="localhost"}
    export DEPLOYER_PORT=${DEPLOYER_PORT:="9192"}
    # MongoDB is used only by Crafter Profile / Crafter Social (enterprise)
    export MONGODB_HOST=${MONGODB_HOST:="localhost"}
    export MONGODB_PORT=${MONGODB_PORT:="28020"}
    export TOMCAT_HOST=${TOMCAT_HOST:="localhost"}
    export TOMCAT_HTTP_PORT=${TOMCAT_HTTP_PORT:="9080"}
    export TOMCAT_HTTPS_PORT=${TOMCAT_HTTPS_PORT:="9443"}
    export TOMCAT_AJP_PORT=${TOMCAT_AJP_PORT:="9009"}
    export TOMCAT_SHUTDOWN_PORT=${TOMCAT_SHUTDOWN_PORT:="9005"}
    export TOMCAT_DEBUG_PORT=${TOMCAT_DEBUG_PORT:="9000"}

|hr|

^^^^^^^
Logging
^^^^^^^
Learn more about how to configure CrafterCMS :ref:`logging`.

|hr|

.. _reverse-proxy-configuration:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Reverse Proxy (Apache HTTPd / NGINX)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
It's often times desirable to use a reverse proxy like Apache HTTPd, NGINX, or a CDN to front the CrafterCMS Studio and Engine web applications. This can be helpful for faster serving of static assets, caching, and SSL termination among other benefits. In this section, we discuss how to configure a reverse proxy using Apache 2 HTTPd vhost configuration
for authoring and delivery. A similar approach can be used for other HTTPd servers.

"""""""""""""""""""""""""""""""
Reverse Proxy with Apache HTTPd
"""""""""""""""""""""""""""""""
Below are the directives used for setting up a reverse proxy with Apache.
The directive on line 7 below is used for setting up the websocket proxy for Studio (authoring) events.

.. _configure-reverse-proxy-for-authoring:

.. code-block:: apache
   :caption: *Authoring Configuration*
   :emphasize-lines: 4,7-9
   :linenos:

   <VirtualHost *:80>
        ServerName authoring.example.com

        ProxyPreserveHost On

        # Proxy Authoring and Preview (Crafter Studio and Engine Preview)
        ProxyPassMatch ^/(studio/events)$  ws://localhost:8080/$1
        ProxyPass / http://localhost:8080/
        ProxyPassReverse / http://localhost:8080/

        # Configure the log files
        ErrorLog ${APACHE_LOG_DIR}/crafter-studio-error.log
        CustomLog ${APACHE_LOG_DIR}/crafter-studio-access.log combined
   </VirtualHost>

|

A delivery Engine can host multiple projects. Typical production setup is one virtual host per project,
each setting the ``X-Crafter-Site`` header so Engine knows which tenant to render. The vhost overwrites
that header with the project configured for the vhost and removes any ``crafterSite`` query parameter
sent by the client, so a visitor can't make one virtual host serve another project.
See :ref:`setup-project-for-delivery`.

.. _configure-reverse-proxy-for-delivery:

.. code-block:: apache
   :caption: *Delivery Configuration*
   :emphasize-lines: 14,19-22,40,46,47
   :linenos:

   <VirtualHost *:80>
        ServerName example.com

        # Remember to change {path_to_craftercms_home} to CrafterCMS installation home
        # Remember to change {myproject} to your actual project name

        # Path to your CrafterCMS project
        DocumentRoot /{path_to_craftercms_home}/data/repos/sites/{myproject}

        RewriteEngine On

        # Assign the CrafterCMS project for this vhost. "set" replaces any
        # X-Crafter-Site header sent by the client. Requires mod_headers.
        RequestHeader set X-Crafter-Site "{myproject}"

        # Remove any crafterSite query parameter sent by the client, keeping all
        # other query parameters. The N flag re-runs the rules, so repeated
        # crafterSite parameters are all removed.
        RewriteCond %{QUERY_STRING} ^(.*&)?crafterSite=[^&]*&(.*)$ [NC]
        RewriteRule ^/?(.*)$ /$1?%1%2 [N]
        RewriteCond %{QUERY_STRING} ^(.*?)&?crafterSite=[^&]*$ [NC]
        RewriteRule ^/?(.*)$ /$1?%1 [N]

        # Block outside access to management services
        RewriteRule ^/api/1/cache / [NC,PT,L]
        RewriteRule ^/api/1/site/mappings / [NC,PT,L]
        RewriteRule ^/api/1/site/cache / [NC,PT,L]
        RewriteRule ^/api/1/site/context/destroy / [NC,PT,L]
        RewriteRule ^/api/1/site/context/rebuild / [NC,PT,L]

        # Take all inbound URLs and lower case them before proxying to Crafter Engine
        # Crafter Studio enforces lower-case URLs.
        # Using the rewrite rule below, the URL the user sees can be mixed-case,
        # however, what's sent to CrafterCMS is always lower-case.
        RewriteCond %{REQUEST_URI} !^/static-assets/.*$ [NC]
        RewriteCond %{REQUEST_URI} !^/api/.*$ [NC]
        RewriteMap lc int:tolower
        RewriteRule ^/(.*)$ /${lc:$1}

        ProxyPreserveHost On

        # Don't proxy static-asset, instead, serve directly from HTTPd
        ProxyPass /static-assets !

        # Proxy the rest to Crafter Engine
        ProxyPass / http://localhost:9080/
        ProxyPassReverse / http://localhost:9080/

        # Configure the log files
        ErrorLog ${APACHE_LOG_DIR}/crafter-engine-error.log
        CustomLog ${APACHE_LOG_DIR}/crafter-engine-access.log combined
    </VirtualHost>

The ``ProxyPreserveHost`` directive indicates whether it uses incoming Host HTTP request header for proxy request.

The ``ProxyPass`` and ``ProxyPassReverse`` directives in the above example specify that traffic to the server name
specified in your config should be proxied to ``http://localhost:8080/`` for your authoring install and
``http://localhost:9080/`` for your delivery install. The ``ProxyPassReverse`` distinguishes your configuration
as a reverse proxy setup.

The ``ProxyPassMatch`` directive is equivalent to ``ProxyPass`` but makes use of regular expressions instead of simple
prefix matching. In the above authoring example, the ``ProxyPassMatch`` line configures the websocket for Studio events.

""""""""""""""""""""""""
Reverse Proxy with NGINX
""""""""""""""""""""""""
Below are the directives used for setting up a reverse proxy with NGINX:

.. _configure-reverse-proxy-for-authoring-nginx:

.. code-block:: nginx
    :caption: *NGINX Authoring Configuration*
    :linenos:

    server {
        listen 80;
        server_name authoring.example.com;

        # Proxy Authoring and Preview (Crafter Studio and Engine Preview)
        location ~ ^/(studio/events)$ {
            proxy_pass http://localhost:8080;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }

        location / {
            proxy_pass http://localhost:8080;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Configure the log files
        error_log ${NGINX_LOG_DIR}/crafter-studio-error.log;
        access_log ${NGINX_LOG_DIR}/crafter-studio-access.log combined;
    }

|

.. _configure-reverse-proxy-for-delivery-nginx:

.. code-block:: nginx
    :caption: *NGINX Delivery Configuration*
    :linenos:

    server {
        listen 80;
        server_name example.com;

        # Remember to change {path_to_craftercms_home} to CrafterCMS installation home
        # Remember to change {myproject} to your actual project name

        # Path to your CrafterCMS project
        root /{path_to_craftercms_home}/data/repos/sites/{myproject};

        location /static-assets/ {
            # Serve static assets directly from NGINX
            # Adjust the path as needed based on your setup
            alias /{path_to_craftercms_home}/data/repos/sites/{myproject}/static-assets/;
        }

        location / {
            # Remove any crafterSite query parameter sent by the client, keeping all
            # other query parameters
            if ($args ~* "^(?<qs_head>.*?)(?:^|&)crafterSite=[^&]*(?<qs_tail>(?:&.*)?)$") {
                set $args "$qs_head$qs_tail";
            }
            if ($args ~ "^&(?<qs_rest>.*)$") {
                set $args $qs_rest;
            }

            # Block outside access to management services
            rewrite ^/api/1/cache / break;
            rewrite ^/api/1/site/mappings / break;
            rewrite ^/api/1/site/cache / break;
            rewrite ^/api/1/site/context/destroy / break;
            rewrite ^/api/1/site/context/rebuild / break;

            # Take all inbound URLs and lower case them before proxying to Crafter Engine
            # Crafter Studio enforces lower-case URLs.
            # Using the rewrite rule below, the URL the user sees can be mixed-case,
            # however, what's sent to CrafterCMS is always lower-case.
            if ($request_uri !~ ^/static-assets/.*$ ) {
                if ($request_uri !~ ^/api/.*$ ) {
                    rewrite ^/(.*)$ /${lc:$1} break;
                }
            }

            proxy_pass http://localhost:9080/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # Assign the CrafterCMS project for this vhost, replacing any
            # X-Crafter-Site header sent by the client
            proxy_set_header X-Crafter-Site "{myproject}";
        }

        # Configure the log files
        error_log ${NGINX_LOG_DIR}/crafter-engine-error.log;
        access_log ${NGINX_LOG_DIR}/crafter-engine-access.log combined;
    }

Depending on your setup, the following CrafterCMS properties may need to be setup:

- ``crafter.engine.forwarded.headers.enabled`` property under :ref:`engine-forwarded-headers` in the ``server-config.properties`` file
- ``studio-config-forwarded-headers`` property under :ref:`studio-forwarded-headers` in the ``studio-config-override.yaml`` file

.. note::
    The delivery examples above set the ``X-Crafter-Site`` header to ``{myproject}`` instead of injecting a
    ``crafterSite`` query parameter. The header takes precedence over the query parameter and the cookie, so
    content retrieval API requests through these virtual hosts no longer need to pass ``crafterSite``.
    The Apache example requires ``mod_headers`` and ``mod_rewrite`` to be enabled.

.. _environment-variables:

^^^^^^^^^^^^^^^^^^^^^
Environment Variables
^^^^^^^^^^^^^^^^^^^^^
Using environment variables allows you to inject properties into a vanilla installation without modifying any actual
files, which is especially useful when using Docker or Kubernetes.

""""""""""""""""""""""""""""""""""""""""
CrafterCMS Default Environment Variables
""""""""""""""""""""""""""""""""""""""""
The following environment variables are configured in the ``CRAFTER_HOME/bin/crafter-setenv.sh``  file. Remember to
restart your installation so your changes to the file will take effect.

.. _env-var-location:

~~~~~~~~
Location
~~~~~~~~
The following environment variables are used to change location of data, logs, etc.

.. list-table:: Location Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - CRAFTER_HOME
      - CrafterCMS *Authoring/Delivery* path
      - {CrafterCMS-install-directory}/crafter-{env}/
    * - CRAFTER_LOGS_DIR
      - CrafterCMS logs file path
      - $CRAFTER_HOME/logs
    * - CRAFTER_DATA_DIR
      - CrafterCMS data file path
      - $CRAFTER_HOME/data
    * - CRAFTER_TEMP_DIR
      - CrafterCMS temporary directory path
      - $CRAFTER_HOME/temp
    * - CRAFTER_BACKUPS_DIR
      - CrafterCMS backup directory path
      - $CRAFTER_HOME/backups

See the following for more information on using the above location environment variables:

- :ref:`logging`
- :ref:`running-the-backup-script`
- :ref:`changing-the-data-and-logs-folder-location`

.. _env-var-ports-and-hosts:

~~~~~~~~~~~~~~~
Ports and Hosts
~~~~~~~~~~~~~~~
Here are the environment variables used for configuring hosts and ports:

.. list-table:: Location Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - MAIL_HOST
      - CrafterCMS mail host
      - localhost
    * - MAIL_PORT
      - CrafterCMS mail port
      - 25
    * - SEARCH_HOST
      - Search host
      - localhost
    * - SEARCH_PORT
      - Search port
      - 9201
    * - DEPLOYER_HOST
      - Deployer host
      - localhost
    * - DEPLOYER_PORT
      - Deployer port
      - 9201
    * - MONGODB_HOST
      - MongoDB host (Crafter Profile / Crafter Social |enterpriseOnly|)
      - localhost
    * - MONGODB_PORT
      - MongoDB port (Crafter Profile / Crafter Social |enterpriseOnly|)
      - 27020
    * - MARIADB_HOST
      - MariaDB host (authoring only)
      - 127.0.0.1
    * - MARIADB_PORT
      - MariaDB port  (authoring only)
      - 33306
    * - TOMCAT_HOST
      - Tomcat host
      - localhost
    * - TOMCAT_HTTP_PORT
      - Tomcat Http port
      - 8080
    * - TOMCAT_HTTPS_PORT
      - Tomcat SSL (https) port
      - 8443
    * - TOMCAT_AJP_PORT
      - Tomcat AJP port
      - 8009
    * - TOMCAT_SHUTDOWN_PORT
      - Tomcat shutdown port
      - 8005
    * - TOMCAT_DEBUG_PORT
      - Tomcat debug port
      - 8000

See :ref:`change-ports-and-hostnames` for more information.

.. _env-var-urls:

~~~~
URLs
~~~~
Here are the environment variables used for setting URLs for various CrafterCMS modules:

.. list-table:: Location Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - SEARCH_URL
      - Search URL
      - ``http://$SEARCH_HOST:$SEARCH_PORT``
    * - DEPLOYER_URL
      - Crafter Deployer URL
      - ``http://$DEPLOYER_HOST:$DEPLOYER_PORT``
    * - STUDIO_URL
      - Crafter Studio URL
      - ``http://$TOMCAT_HOST:$TOMCAT_HTTP_PORT/studio``
    * - ENGINE_URL
      - Crafter Engine URL
      - ``http://$TOMCAT_HOST:$TOMCAT_HTTP_PORT/``
    * - PROFILE_URL
      - Crafter Profile |enterpriseOnly| URL
      - ``http://$TOMCAT_HOST:$TOMCAT_HTTP_PORT/crafter-profile``
    * - SOCIAL_URL
      - Crafter Social |enterpriseOnly| URL
      - ``http://$TOMCAT_HOST:$TOMCAT_HTTP_PORT/crafter-social``

See the following for examples on where the above URL environment variables are used:

- :ref:`studio-preview-deployer-config`
- :ref:`crafter-deployer-administration`


.. _env-var-java-options:

~~~~~~~~~~~~
Java Options
~~~~~~~~~~~~
Here are the environment variables used for setting Java JVM options:

.. list-table:: Java Options Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - OPENSEARCH_JAVA_OPTS
      - OpenSearch Java options
      - "-server -Xss1024K -Xmx1G"
    * - DEPLOYER_JAVA_OPTS
      - Deployer Java options
      - "-server -Xss1024K -Xmx1G"
    * - CATALINA_OPTS
      - Tomcat options
      - "-server -Xss1024K -Xms1G -Xmx4G"

See the following for examples using the Java options environment variable:

- :ref:`studio-performance-tuning`
- :ref:`engine-performance-tuning`

.. _env-var-tomcat:

~~~~~~
Tomcat
~~~~~~
Here are the environment variables used for configuring Tomcat:

.. list-table:: Tomcat Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - CATALINA_HOME
      - Apache Tomcat files path
      - $CRAFTER_HOME/bin/apache-tomcat
    * - CATALINA_PID
      - Tomcat process id file save path
      - $CATALINA_HOME/bin/tomcat.pid
    * - CATALINA_LOGS_DIR
      - Tomcat file logs path
      - $CRAFTER_LOGS_DIR/tomcat
    * - CATALINA_OUT
      - Tomcat main log file
      - $CATALINA_LOGS_DIR/catalina.out
    * - CATALINA_TMPDIR
      - Tomcat temporary directory
      - $CRAFTER_TEMP_DIR/tomcat

.. _env-var-opensearch:

~~~~~~~~~~
OpenSearch
~~~~~~~~~~
Here are the environment variables used for configuring OpenSearch:

.. list-table:: OpenSearch Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - OPENSEARCH_JAVA_HOME
      - OpenSearch Java home directory
      - $JAVA_HOME
    * - OPENSEARCH_HOME
      - OpenSearch home directory
      - $CRAFTER_BIN_DIR/opensearch
    * - OPENSEARCH_INDEXES_DIR
      - OpenSearch indexes directory
      - $CRAFTER_DATA_DIR/indexes-es
    * - OPENSEARCH_LOGS_DIR
      - OpenSearch log files directory
      - $CRAFTER_LOGS_DIR/logs/search
    * - OPENSEARCH_PID
      - OpenSearch process Id
      - $OPENSEARCH_HOME/opensearch.pid
    * - OPENSEARCH_USERNAME
      - OpenSearch username
      -
    * - OPENSEARCH_PASSWORD
      - OpenSearch password
      -
    * - SEARCH_DOCKER_NAME
      - OpenSearch Docker name
      - {env}-search

.. _env-var-deployer:

~~~~~~~~
Deployer
~~~~~~~~
Here are the environment variables used for configuring the Deployer:

.. list-table:: Deployer Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - DEPLOYER_HOME
      - Crafter Deployer jar files path
      - $CRAFTER_HOME/bin/crafter-deployer
    * - DEPLOYER_DATA_DIR
      - Deployer data files directory
      - $CRAFTER_DATA_DIR/deployer
    * - DEPLOYER_LOGS_DIR
      - Deployer log files directory
      - $CRAFTER_LOGS_DIR/deployer
    * - DEPLOYER_DEPLOYMENTS_DIR
      - Deployer deployments files directory
      - $CRAFTER_DATA_DIR/repos/sites
    * - DEPLOYER_SDOUT
      - Deployer SDOUT path
      - $DEPLOYER_LOGS_DIR/crafter-deployer.out
    * - DEPLOYER_PID
      - Deployer process id file
      - $DEPLOYER_HOME/crafter-deployer.pid

.. _env-var-mongodb:

~~~~~~~~~~~~~~~~~~~~~~~~
MongoDB |enterpriseOnly|
~~~~~~~~~~~~~~~~~~~~~~~~
These variables apply only when using Crafter Profile or Crafter Social |enterpriseOnly|. MongoDB is not required for the core CMS.

.. list-table:: MongoDB Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - MONGODB_HOME
      - MongoDB files path
      - $CRAFTER_BIN_DIR/mongodb
    * - MONGODB_PID
      - MongoDB process id file save path
      - $MONGODB_DATA_DIR/mongod.lock
    * - MONGODB_DATA_DIR
      - MongoDB data directory
      - $CRAFTER_DATA_DIR/mongodb
    * - MONGODB_LOGS_DIR
      - MongoDB log files directory
      - $CRAFTER_LOGS_DIR/mongodb

.. _env-var-mariadb:

~~~~~~~
MariaDB
~~~~~~~
Here are the environment variables used for configuring MariaDB:

.. list-table:: MariaDB Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - MARIADB_SCHEMA
      - MariaDB schema
      - crafter
    * - MARIADB_HOME
      - MariaDB files path
      - $CRAFTER_BIN_DIR/dbms
    * - MARIADB_DATA_DIR
      - MariaDB data directory
      - $CRAFTER_DATA_DIR/db
    * - MARIADB_ROOT_USER
      - MariaDB root username
      -
    * - MARIADB_ROOT_PASSWD
      - MariaDB root password
      -
    * - MARIADB_USER
      - MariaDB username
      - crafter
    * - MARIADB_PASSWD
      - MariaDB user password
      - crafter
    * - MARIADB_SOCKET_TIMEOUT
      - MariaDB socket timeout
      - 60000
    * - MARIADB_TCP_TIMEOUT
      - MariaDB TCP timeout
      - 120
    * - MARIADB_PID
      - MariaDB process id file
      - \$MARIADB_HOME/\$HOSTNAME.pid

You must change the pre-configured default values for environment variables for passwords such as ``MARIADB_PASSWD``
to secure your installation. See :ref:`system-admin-security` for more information on changing the values.

.. _env-var-git:

~~~
Git
~~~
Here are the environment variables used for configuring Git:

.. list-table:: Git Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - GIT_CONFIG_NOSYSTEM
      - Ignore Git system wide configuration file
      - true

.. _env-var-management-tokens:

~~~~~~~~~~~~~~~~~
Management Tokens
~~~~~~~~~~~~~~~~~
Here are the environment variables used for configuring Management Tokens.
You must update these per installation and provide these tokens to the status monitors:

.. list-table:: Management Tokens Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - STUDIO_MANAGEMENT_TOKEN
      - Authorization token for Studio
      - defaultManagementToken
    * - ENGINE_MANAGEMENT_TOKEN
      - Authorization token for Engine
      - defaultManagementToken
    * - DEPLOYER_MANAGEMENT_TOKEN
      - Authorization token for Deployer
      - defaultManagementToken
    * - PROFILE_MANAGEMENT_TOKEN
      - Authorization token for Profile
      - defaultManagementToken
    * - SOCIAL_MANAGEMENT_TOKEN
      - Authorization token for Social
      - defaultManagementToken

.. _env-var-profile-access-tokens:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Profile Access Tokens |enterpriseOnly|
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Here are the environment variables used for configuring the Crafter Profile |enterpriseOnly| REST API access tokens used
by Profile, Engine, and Social. When ``access-tokens.env`` does not exist, set all three, or leave all three unset.
Startup stops when Profile is deployed, ``$CRAFTER_DATA_DIR/profile/access-tokens.env`` is missing, and either:

* only some of ``PROFILE_ADMIN_ACCESS_TOKEN_ID``, ``PROFILE_ENGINE_ACCESS_TOKEN_ID``, and
  ``PROFILE_SOCIAL_ACCESS_TOKEN_ID`` are set, or
* none of them is set and local MongoDB data already exists, or ``MONGODB_HOST`` is not ``localhost`` or ``127.0.0.1``.

.. list-table:: Profile Access Token Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - PROFILE_ADMIN_ACCESS_TOKEN_ID
      - Access token ID for the Profile Admin client. Profile also seeds this ID as the ``profile-admin`` token.
      -
    * - PROFILE_ENGINE_ACCESS_TOKEN_ID
      - Access token ID Engine uses to call Profile. Profile also seeds this ID as the ``engine`` token.
      -
    * - PROFILE_SOCIAL_ACCESS_TOKEN_ID
      - Access token ID Social uses to call Profile. Profile also seeds this ID as the ``social`` token.
      -
    * - PROFILE_ACCESS_TOKENS_FILE
      - File that stores the three access token IDs
      - $CRAFTER_DATA_DIR/profile/access-tokens.env

On a new local Authoring installation, CrafterCMS generates the three IDs and writes them to ``PROFILE_ACCESS_TOKENS_FILE``
with mode ``600``. ``PROFILE_ACCESS_TOKENS_FILE`` by default is set to ``$CRAFTER_DATA_DIR/profile/access-tokens.env``. If all three ID variables
are already set, those values are used and the file is not created. A value already set in the environment takes
precedence over the same value in the file.

Delivery does not generate a token file. Copy Authoring's ``PROFILE_ACCESS_TOKENS_FILE`` to each Delivery node that uses
Profile, or set the same three ID variables. An installation that does not deploy Profile skips this setup.

See :ref:`upgrade-notes-for-craftercms-4.7.0` before upgrading an existing installation.

.. _env-var-configuration-files-encryption:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Configuration Files Encryption
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Here are the environment variables used to encrypt and decrypt values inside configuration files:

.. list-table:: Configuration Files Encryption Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - CRAFTER_ENCRYPTION_KEY
      - Key used for encrypting configuration properties
      - default_encryption_key
    * - CRAFTER_ENCRYPTION_SALT
      - Salt used for encrypting configuration properties
      - default_encryption_salt

You must change the pre-configured default values for these environment variables to secure your installation.

See the following for more information on using the configuration files encryption environment variables:

- :ref:`change-the-defaults`
- :ref:`crafter-deployer-administration`
- :ref:`studio-cipher-configuration`

.. _env-var-database-encryption:

~~~~~~~~~~~~~~~~~~~
Database Encryption
~~~~~~~~~~~~~~~~~~~
Here are the environment variables used to encrypt and decrypt values in the database:

.. list-table:: Database Encryption Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - CRAFTER_SYSTEM_ENCRYPTION_KEY
      - Key used for encrypting database values
      - \<someDefaultKeyValue\>
    * - CRAFTER_SYSTEM_ENCRYPTION_SALT
      - Salt used for encrypting database values
      - \<someDefaultSaltValue\>

You must change the pre-configured default values for these environment variables to secure your installation.
See :ref:`studio-cipher-configuration` for more information on using the above environment variables.

.. _env-var-preview-token-encryption:

~~~~~~~~~~~~~~~~~~~~~~~~
Preview Token Encryption
~~~~~~~~~~~~~~~~~~~~~~~~
.. version_tag::
    :label: Since
    :version: 4.7.0

Here are the environment variables used to encrypt and decrypt preview tokens shared by Studio and Engine:

.. list-table:: Preview Token Encryption Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - CRAFTER_PREVIEW_TOKEN_KEY
      - Key used for encrypting preview tokens
      - default_preview_token_key
    * - CRAFTER_PREVIEW_TOKEN_SALT
      - Salt used for encrypting preview tokens
      - default_preview_token_salt

You must change the pre-configured default values for these environment variables to secure your installation.
Preview tokens are prefixed with ``CPT-V1#`` by default. If you need a different prefix, set
``studio.security.preview.token.prefix`` in Studio and ``crafter.security.preview.token.prefix`` in Engine to the
same value.

See the following for more information on using the preview token encryption environment variables:

- :ref:`change-the-defaults`
- :ref:`preview-token`
- :ref:`engine-security-preview-mode`


.. _env-var-serverless-deployments:

~~~~~~~~~~~~~~~~~~~~~~
Serverless Deployments
~~~~~~~~~~~~~~~~~~~~~~
Here are the environment variables used for serverless deployments:

.. list-table:: Serverless Deployments Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - AWS_S3_ENDPOINT
      - Endpoint used for accessing S3 buckets
      - ""
    * - AWS_S3_PATH_STYLE_ACCESS
      - Use path style URLs for accessing S3 buckets
      - false
    * - SERVERLESS_NAMESPACE
      - Namespace used for deployment
      - cloud-sites
    * - PREVIEW_BUCKET_NAME_PATTERN
      - Name pattern for S3 Preview bucket
      - ${SERVERLESS_NAMESPACE}-blobs-\${siteName}
    * - PREVIEW_BUCKET_PREFIX_PATTERN
      - Prefix pattern for S3 Preview bucket
      - ""
    * - STAGING_BUCKET_NAME_PATTERN
      - Name pattern for S3 Staging bucket
      - ${SERVERLESS_NAMESPACE}-site-\${siteName}-staging
    * - STAGING_BUCKET_PREFIX_PATTERN
      - Prefix pattern for S3 Staging bucket
      - ""
    * - LIVE_BUCKET_NAME_PATTERN
      - Name pattern for S3 Live bucket
      - ${SERVERLESS_NAMESPACE}-site-\${siteName}
    * - LIVE_BUCKET_PREFIX_PATTERN
      - Prefix pattern for S3 Live bucket
      - ""

See :ref:`blob-stores` for more information on using the above environment variables.

.. _env-var-configuration:

~~~~~~~~~~~~~
Configuration
~~~~~~~~~~~~~
Here are the configuration variables used in CrafterCMS:

.. list-table:: Configuration Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - CRAFTER_ENVIRONMENT
      - Name used for environment specific configurations in Studio, Engine and Deployer
      - default

See the following for examples on using the configuration environment variable:

- :ref:`engine-multi-environment-support`
- :ref:`studio-multi-environment-support`

.. _env-var-ssh:

~~~
SSH
~~~
Here are the SSH variables used in CrafterCMS:

.. list-table:: SSH Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - CRAFTER_SSH_CONFIG
      - CrafterCMS folder path for the SSH configuration
      - $CRAFTER_DATA_DIR/ssh |

.. _env-var-studio-token:

~~~~~~~~~~~~
Studio Token
~~~~~~~~~~~~
Here are the environment variables used for configuring Studio's access tokens for API's:

.. list-table:: Studio Configuration Environment Variables
    :header-rows: 1

    * - Variable Name
      - Description
      - Default Value
    * - STUDIO_TOKEN_ISSUER
      - Issuer for generated tokens
      - Crafter Studio
    * - STUDIO_TOKEN_VALID_ISSUERS
      - Issuer for generated tokens
      - Crafter Studio
    * - STUDIO_TOKEN_AUDIENCE
      - Audience for generation and validation of access tokens
      -
    * - STUDIO_TOKEN_TIMEOUT
      - Expiration time of access tokens in minutes
      - 5
    * - STUDIO_TOKEN_SIGN_PASSWORD
      - Password for signing the access tokens
      -
    * - STUDIO_TOKEN_ENCRYPT_PASSWORD
      - Password for encrypting the access tokens
      -
    * - STUDIO_REFRESH_TOKEN_NAME
      - Name of the cookie to store the refresh token
      - refresh_token
    * - STUDIO_REFRESH_TOKEN_MAX
      - Expiration time of the refresh token cookie in seconds
      - 300
    * - STUDIO_REFRESH_TOKEN_SECURE
      - Indicates if refresh token cookie should be secure
      - false

See :ref:`studio-access-tokens` for more information.

"""""""""""""""""""""""""""""""
Accessing Environment Variables
"""""""""""""""""""""""""""""""
.. include:: /includes/environment-variables-whitelist.rst

|hr|

------
Studio
------
Crafter Studio helps create and manage content and code in a project/site. Learn more about Crafter Studio configuration and administration in the articles :ref:`Studio Configuration <studio-config>` and :ref:`Studio Administration <studio-admin>`.

|hr|

------
Engine
------
Crafter Engine delivers the content to consumers/users. Learn more about Crafter Engine configuration and administration in the article :ref:`Engine Configuration <engine-config>`.

|hr|

--------
Deployer
--------
Crafter Deployer ties Studio and Engine together and is responsible for publishing content from Studio to Engine. Learn more about Crafter Deployer configuration and administration in :ref:`Deployer Administration and Configuration <crafter-deployer-administration>`.

|hr|

------------------------
Profile |enterpriseOnly|
------------------------
Crafter Profile |enterpriseOnly| provides a user identity augmentation capability. It allows the project/site developers to add metadata to existing identity (managed in LDAP for example) and add arbitrary metadata to it, or, it can manage the identity entirely if desired. Learn more about Crafter Profile configuration and administration in :ref:`Profile Configuration and Administration <crafter-profile-admin>`.

|hr|

-----------------------
Social |enterpriseOnly|
-----------------------
Crafter Social |enterpriseOnly| provides a user generated content management system. It allows the project/site developers to handle all actions related to user-generated content (UGC), including the creation, updating and moderation of content. Learn more about Crafter Social configuration and administration in :ref:`Social Configuration and Administration <crafter-social-admin>`.