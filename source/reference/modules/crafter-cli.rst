:is-up-to-date: True
:last-updated: 4.2.0
:orphan:

.. index:: Crafter CLI, CLI

.. meta::
   :description: Crafter CLI is the CrafterCMS DevContentOps toolkit for authenticating and exercising CrafterCMS APIs from Unix, Mac, or Windows terminals.
   :keywords: Crafter CLI, CrafterCMS, DevContentOps, command line interface, CLI

.. _crafter-cli:

===========
Crafter CLI
===========
CrafterCMS provides Crafter CLI (Command Line Interface), a DevContentOps Toolkit, to support :ref:`DevContentOps <devcontentops>` processes in CrafterCMS. The CLI allows you to authenticate with CrafterCMS and exercise its APIs via a Unix, Mac, or Windows command line interface.

Run the CLI in your terminal program, navigate to the ``bin`` folder  ``CRAFTER_HOME/bin/cli/bin/``. There you will find two versions of the tool:

* **crafter-cli:** - for users on a Linux/macOS operating system
* **crafter-cli.bat** - for users on a Windows operating system

.. _crafter-cli-add-environment:

When using the **crafter-cli**, we first need to setup the connection to CrafterCMS before we can use the other available commands. To setup the connection, run the ``add-environment`` command,  provide a name, the url for a CrafterCMS authoring server and the authentication information.

For the example below, we'll use ``local`` for the name, ``http://localhost:8080`` for the url, and your access token for the authentication. See :ref:`here <api-token>` for the steps on how to create a token. Leave the token blank, you will be prompted for the token after issuing the ``add-environment`` command:

   .. code-block:: bash

      ➜  ./crafter-cli add-environment -e local -u http://localhost:8080 --token
      Enter value for --token (The access token for authentication):
      Environment added

   |

After setting up the connection to CrafterCMS, you may now create sites or sync a remote repository from the command line, etc:

Here's an example  of creating a site:

   .. code-block:: bash

      ➜  ./crafter-cli create-site -e local -s myeditorial --blueprint org.craftercms.blueprint.editorial
      OK

   |

Here's an example of adding a remote to a site:

   .. code-block:: bash

      ➜  ./crafter-cli add-remote -e local -s editorial -n origin -u http://github.com/john.doe/editorial.git
      Created

   |

Here's an example of syncing to a remote repository:

   .. code-block:: bash

       ➜  ./crafter-cli sync-to -e local -s editorial -n origin -b site-updates
       OK

   |


To view the available commands, type in ``./crafter-cli -h``

   .. code-block:: bash

      ➜  ./crafter-cli -help
      Usage: crafter-cli [-hV] [COMMAND]
        -h, --help      Show this help message and exit.
        -V, --version   Print version information and exit.
      Commands:
        help                 Displays help information about the specified command
        add-environment      Adds the configuration to connect to CrafterCMS
        add-remote           Adds a remote repository to a site
        create-site          Creates a site from a blueprint or a remote repository
        list-remotes         List the remote repositories of a site
        sync-from            Sync the content of a site from a remote repository
        sync-to              Sync the content of a site to a remote repository
        list-sites           List the sites that the current user can access
        copy-plugin          Copies a plugin from a Studio local folder into a site
        create-user          Creates a user from command parameters or bulk create users from a CSV file
        list-users           Get all Studio users
        create-access-token  Creates an access token for the user
        publish-content      Publish content from a project or site.
        create-group         Creates a group from command parameters or bulk create groups from a CSV file
        list-groups          List all the groups or search for groups by keyword or sort by a field.

   |

.. _crafter-cli-command-help:

To view more information about each command, just enter ``./crafter-cli <command>``, for example:

   .. code-block:: bash

      ➜  ./crafter-cli add-environment
      Usage: crafter-cli add-environment --password [--config=path] -e=<environment>
                                      [-p=<profile>] -u=<url> --username=<username>
      Adds the configuration to connect to CrafterCMS
            --config=path         The folder to store configurations
        -e, --environment=<environment>
                                  The name of the environment
        -p, --profile=<profile>   The name of the profile
            --password            The password for authentication
        -u, --url=<url>           The URL of the server
            --username=<username> The username for authentication

   |

Crafter CLI for DevContentOps processes source code is managed in GitHub: https://github.com/craftersoftware/craftercms/tree/support/4.x/cli

----------
Using cURL
----------
In this section, we'll show the basics of interacting with Crafter Studio APIs by performing the following:

#. Authentication
#. Get a list of projects under management
#. Write content to a project

We’ll use cURL, a ubiquitous Linux command tool as our client.

You can find the full Crafter Studio API for CrafterCMS :ref:`here<crafter-studio-api>`

^^^^^^^^^^^^^^
Authentication
^^^^^^^^^^^^^^

There are two authentication methods that can be used: JWT and HTTP Basic

.. _jwt-authentication:

""""""""""""""""""
JWT Authentication
""""""""""""""""""

.. version_tag::
    :label: Since
    :version: 4.0.0

JWT is a widely adopted standard for secure authentication and is the preferred way.
HTTP Basic is simple but not as secure as JWT and for that reason it is disabled by default. However, it can be very
useful for local development environments.

In both authentication methods you need to know the credentials of a user with the right permissions depending on
the operations that you need to execute. For this example we will generate an API access token for the ``admin`` user
but in a real scenario you should create a user with the least amount of permissions possible.

.. note::

    Not all users have the required permission ``manage_access_token`` to create access tokens.
    If the ``Token Management`` tool is not shown in the global menu you will need to request the permission or an
    access token from a system administrator.

|

The first thing that you need is an API access token. If you already have an API access token you can skip to step 2,
otherwise, start from step 1.

1. Login to Crafter Studio then open the ``Token Management`` tool

   .. figure:: /_static/images/jwt/global-menu.webp
       :width: 90%
       :alt: Crafter Studio - Global Menu
       :align: center

   |

2. Follow the instructions :ref:`here <api-token>` on how to create a new API access token.

3. Include the API access token with all requests

   Most HTTP clients provide built-in support for access token authentication, in the case of cURL you will need
   to use the header option ``--header`` or ``-H``

   ``curl -H 'Authentication: Bearer <access token>' ...``

   |

.. _http-basic-authentication:

"""""""""""""""""""""""""
HTTP Basic Authentication
"""""""""""""""""""""""""

If HTTP Basic authentication is already enabled you can skip step 1 and start using the credentials:

1. Enable HTTP Basic auth and restart Crafter Studio

   Update the Crafter Studio configuration override: from Crafter Studio ``Global Menu > Global Config`` or directly
   from the global repository ``$CRAFTER_HOME/data/repos/global/configuration/studio-config-override.yaml``

   Adding the following property:

   .. code-block:: yaml

       # Indicates if HTTP Basic auth should be enabled for the API
       studio.security.http.basic.enabled: true

   |


2. Include the credentials with all requests

   Most HTTP clients provide an easy way to use HTTP Basic, in the case of cURL you can use the user options
   ``--user`` or ``-u``

   ``curl -u <username>:<password> ...``

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Get a List of Projects Under Management
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
We'll get a list of sites the user is authorized to work with using the API
:base_url:`getCurrentUserSites <_static/api/studio.html#tag/users/operation/getCurrentUserSites>`

.. code-block:: bash

    curl <authentication option> -X GET http://localhost:8080/studio/api/2/users/admin/sites

|

After issuing the cURL command you will get a response that contains sites your user has access to:

.. code-block:: json

    {
      "response": {
        "code": 0,
        "message": "OK",
        "remedialAction": "",
        "documentationUrl": ""
    },
    "total": 2,
    "offset": 0,
    "limit": 10,
    "sites": [
      {
        "siteId": "hello",
        "uuid": "e7153d93-3879-41d5-8e91-408a1bd79d74",
        "name": "hello",
        "desc": null,
        "state": "READY"
      },
      {
        "siteId": "my-editorial",
        "uuid": "a82278d6-535c-4e0c-9013-95284e10a993",
        "name": "My Editorial",
        "desc": null,
        "state": "READY"
      }
      ]
    }

|

The response above contains a number of projects. In the next call we will write a content object to one of the projects (editorialcom.) To do this we need the site ID. We get this from the response above: **editorialcom**

^^^^^^^^^^^^^^^^^^^^^^^^^^
Write Content to a Project
^^^^^^^^^^^^^^^^^^^^^^^^^^

We'll now write content to the Editorial com Project using the API
:base_url:`writeContent <_static/api/studio.html#tag/content/operation/writeContent>`

.. code-block:: bash

    curl <authentication option> -d "<page><content-type>/page/category-landing</content-type><display-template>/templates/web/pages/category-landing.ftl</display-template><merge-strategy>inherit-levels</merge-strategy><file-name>index.xml</file-name><folder-name>test3</folder-name><internal-name>test3</internal-name><disabled >false</disabled></page>" -X POST "http://localhost:8080/studio/api/1/services/api/1/content/write-content.json?site=editorialcom&phase=onSave&path=/site/website/test3/index.xml&fileName=index.xml&user=admin&contentType=/page/category-landing&unlock=true"

|

In the call above note:

We are passing in content as the POST body. The content is in XML format. In CrafterCMS, content objects are stored as simple XML documents.
We are passing a number of parameters that tell CrafterCMS where and how to store the content in the repository

Using the above examples as a guide, we can now interact with any Crafter Studio API found :ref:`here <crafter-studio-api>`
