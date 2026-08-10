:is-up-to-date: True
:last-updated: 4.2.2
:orphan:

.. index:: Plugins, Create a Plugin

.. meta::
   :description: Build CrafterCMS plugins to extend Crafter Studio authoring UI and Crafter Engine delivery with custom widgets, REST APIs, content types, and integrations.
   :keywords: plugins, CrafterCMS, Crafter Studio, Crafter Engine, authoring plugins, delivery plugins, composable, extensions

.. _plugins:

=======
Plugins
=======
-----------------
What are plugins?
-----------------

A plugin can contain one or more extensions for CrafterCMS in a single package. These extensions can:

* **Extend Crafter Studio (authoring)**

  * Add Studio authoring widgets that drive the Sidebar and other UI elements
  * Add embedded applications that render in their own page within Studio (see :ref:`Plugin Host <plugin-host>`)
  * Add new Form Engine extensions including data sources and components
  * Add server-side code and services that drive the Studio UI extensions

* **Extend Crafter Engine and the project/web application (delivery)**

  * Add new content types along with their Groovy controllers and FreeMarker templates
  * Add REST APIs and/or server-side code
  * Add 3rd party integrations to your web app

Plugins allows the user to easily add/extend functionality and features of a Web experience
(site, mobile app) or the content authoring experience (authoring tools) or both.
Examples of features/functionalities a user may want to add to their Web app may be a contact form,
a chat bot or Website analytics.

.. _plugins-authoring:

^^^^^^^^^^^^^^^^^
Authoring Plugins
^^^^^^^^^^^^^^^^^

Authoring plugins are those that allow extending Crafter Studio through UI widgets, applications,
and backend services.

""""""""""""""""""""""""""""""
UI Widgets and Standalone Apps
""""""""""""""""""""""""""""""

There are two types of UI plugins

- UI widgets to be used around the different plugable sections of Studio UI
- Standalone apps that run in the :ref:`Plugin Host page <plugin-host>`

Plugins can access Studio client-side components (React), services and utilities through two main mechanisms:

- When using ``npm``, you can ``yarn add`` or ``npm i`` the ``@craftercms/studio-ui`` package
- In the Studio browser runtime, the ``window.craftercms`` global

  - The following namespaces are available through ``window.craftercms``

      - ``libs`` allows access to the published third party techs that power Studio UI

        - React
        - ReactDOM/Client
        - MaterialUI
        - ReactRedux
        - ReactIntl
        - createEmotion
        - ReduxToolkit (createAction)
        - RxJS
        - Emotion (createEmotion)

      - ``components`` contains Studio UI components
      - ``icons`` contains custom Studio UI icons
      - ``utils`` contains utilities for issuing AJAX requests, manipulation objects, arrays, strings, paths, content types, and much more
      - ``services`` contains functions to invoke Studio APIs categorized by module (e.g. users, groups, sites, auth, content types, etc.)
      - ``getStore`` returns the Redux store that powers the UI

.. include:: /includes/plugin-dev-recommendations.rst

Examples of UI Plugins are

* `Component library <https://github.com/craftercms/authoring-ui-plugin-examples/tree/master/examples/component-library>`_: illustrates the creation of a library of widgets that can be rendered throughout Studio UI.
* `Vite example <https://github.com/craftercms/authoring-ui-plugin-examples/tree/master/examples/app-external-sources>`_: illustrates a plugin host app using a dev toolchain with dev server and how to optionally keep the app resources out of the project repo while still using the plugin host.
* `Vanilla Standalone <https://github.com/craftercms/authoring-ui-plugin-examples/tree/master/examples/app-vanilla>`_: illustrates a simple standalone app with single JS entry point without transpilation.

""""""""
Services
""""""""

Through authoring plugins you can add your own service API rest scripts.

For example, you may want to create an API to connect and/or monitor AWS services and create a
`UI extension <UI Widgets and Standalone Apps_>`_ to consume your APIs.

.. _plugins-delivery:

^^^^^^^^^^^^^^^^
Delivery Plugins
^^^^^^^^^^^^^^^^

Delivery plugins allow you to extend the CrafterCMS project by adding content types (and all
content types involve) and REST APIs.

"""""""""""""
Content Types
"""""""""""""

You can add Content Types including their definition, Groovy controller, Freemarker templates and accompanying
assets.

For example, a delivery plugin may be a YouTube video component. The plugin would have the
content type definition, its freemarker template and some JavaScript and CSS to render the content type
when used. When the extension containing the content type is installed, authors would be able to make
use of the YouTube video component adding videos to their content.

"""""""
Scripts
"""""""

Scripts allow you to add APIs to you CrafterCMS project application.

"""""""""
Templates
"""""""""

Through delivery plugins, you can add Freemarker templates to your project. Templates could be
the rendering template of a content type or a Freemarker template hook (explained below). So, in summary,
you can add templates to render your extension content types or to add functionality to pages
via the Freemarker template hooks.

.. _plugins-using-freemarker-templates:

^^^^^^^^^^^^^^^^^^^^^^^^^
Freemarker Template Hooks
^^^^^^^^^^^^^^^^^^^^^^^^^
CrafterCMS provides a mechanism (a "hook") for adding markup and defining macros for plugins via
Freemarker templates. These templates, when the plugin has one of them will be automatically
included in the project.

Here are the supported templates:

* **definitions.ftl**: can be used to define macros for the plugin
* **head.ftl**: can be used to add markup in the HTML <head> element
* **body_top.ftl**: can be used to add markup at the beginning of the HTML <body> element
* **body_bottom.ftl**: can be used to add markup at the end of the HTML <body> element

Place the template/s in the ``{your_plugin_folder}/delivery/templates`` directory location in your plugin
like below:

.. code-block:: text
       :linenos:
       :emphasize-lines: 8-11

       {your_plugin_folder}/
         craftercms-plugin.yaml
         .crafter/
           screenshots/
             default.png
         delivery/
           templates/
             definitions.ftl
             head.ftl
             body_top.ftl
             body_bottom.ftl


The Google Analytics plugin for CrafterCMS available from the `Marketplace <https://craftercms.com/marketplace>`__
uses a Freemarker template (*google-analytics-plugin/delivery/templates/plugins/org/craftercms/plugin/google/analytics/head.ftl*) to add markup in the HTML <head> element.

See https://github.com/craftercms/google-analytics-plugin/blob/master/delivery/templates/plugins/org/craftercms/plugin/google/analytics/head.ftl for an example on what can be in included in the template.



|hr|

.. _how-do-i-make-my-own-plugin:

----------------------------
How Do I Make My Own Plugin?
----------------------------

You'll need the following for creating your plugin:

* Your plugin files
* A plugin descriptor file, ``craftercms-plugin.yaml``

Your plugin files/folders could be JavaScript files, XML files, Groovy scripts, images, CSS files,
and more depending on the plugin type you're creating.

The ``craftercms-plugin.yaml`` file contains information about your plugin, such as the license,
the versions of CrafterCMS supported, and other configurations and metadata. See below for more information.

.. _plugin-descriptor-file:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^
CrafterCMS Plugin Descriptor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``craftercms-plugin.yaml`` file contains information for use in CrafterCMS. This descriptor file contains
information about your extension, such as the license, the versions of CrafterCMS supported, and other
configurations and metadata. In this section, we'll take a look at a plugin descriptor file.

.. code-block:: yaml
     :linenos:
     :caption: *Sample plugin descriptor file*

     # This file describes a plugin for use in CrafterCMS

     # The version of the format for this file
     descriptorVersion: 2

     # Describe the plugin
     plugin:
       type: site
       id: org.craftercms.plugin.test
       name: Project Plugin Example
       tags:
         - test
       version:
         major: 3
         minor: 0
         patch: 1
       description: A simple example for project plugins
       documentation: "https://raw.githubusercontent.com/craftercms/site-plugin-example/master/readme.md"
       website:
         name: Plugin Example
         url: https://github.com/craftercms/site-plugins-example
       media:
         screenshots:
           - title: CrafterCMS
             description: CrafterCMS Example Plugin
             url: "https://raw.githubusercontent.com/craftercms/site-plugin-example/master/.crafter/screenshots/default.png"
       developer:
         company:
           name: CrafterCMS
           email: info@craftercms.com
           url: https://craftercms.com
       license:
         name: MIT
         url: https://opensource.org/licenses/MIT
       crafterCmsVersions:
         - major: 4
           minor: 2
       crafterCmsEditions:
         - community
         - enterprise
       # Option auto-wiring section
       # installation:

Here are some things to note in the descriptor file:

.. list-table:: Descriptor file fields
   :widths: 25 25 50
   :header-rows: 1

   * - Field
     - Required
     - Description
   * - descriptorVersion
     - |checkmark|
     - The version of the format for this file which is currently 2
   * - plugin.type
     - |checkmark|
     - Set the value to ``site``
   * - plugin.id
     - |checkmark|
     - A unique Id that is meaningful/recognizable to people who will be using the plugin
   * - plugin.name
     - |checkmark|
     - The name displayed in the Crafter Marketplace. |br|
       Pick a unique name for your plugin. You can check in the Crafter Marketplace if |br|
       the name you picked does not exist yet. It's also a best practice to provide a name |br|
       for your plugin that is meaningful or recognizable to users. |br|
       The name can be multiple words such as ``Contact Form``
   * - plugin.version
     - |checkmark|
     - The version number for the plugin
   * - plugin.description
     -
     - Contains a short description of the plugin and is displayed underneath the plugin name in |br|
       the Crafter Marketplace
   * - plugin.documentation
     -
     - Serves as the help block for the plugin. It contains a URL to the plugin's documentation file |br|
       (must be in Markdown) containing information on how to use/configure the plugin. The documentation |br|
       will appear alongside the plugin in Crafter Studio and the Crafter Marketplace
   * - plugin.website.url
     -
     - Can be a page for more information on your plugin or for announcing updates, reporting bugs, etc. |br|
       from your user community.
   * - plugin.media.url
     -
     - The path to look for a representative image of the plugin. |br|
       CrafterCMS uses a default path for CrafterCMS to look for a default representative image of a plugin,|br|
       the url ``../.crafter/screenshots/``.
   * - plugin.license
     -
     - The license supported by the plugin
   * - plugin.crafterCmsVersions
     - |checkmark|
     - Contains the CrafterCMS version/s (major and minor numbers) that the plugin is compatible with
       (look in the :ref:`release-notes` section for the versions available), and remember to keep this up to date. |br| |br|

       Here's an example:

       .. code-block:: yaml

           crafterCmsVersions:
             - major: 4
               minor: 1
             - major: 4
               minor: 2

       Note that use of the full CrafterCMS version with the major, minor, and patch numbers is still supported for
       backward compatibility. Remember to maintain the same version format used (either the short version or the full
       version) and not mix them in a plugin version in the descriptor file.

.. note::

  For the images to be used for the ``screenshots`` in the ``craftercms-plugin.yaml`` file, we recommend
  using images with approximately a ``4:3`` aspect ratio (width to height), such as an image sized at 1200x800


.. _plugin-descriptor-auto-wiring:

"""""""""""
Auto-wiring
"""""""""""
CrafterCMS supports automatically wiring your plugin to the corresponding configuration
file in Studio during your plugin installation.

To setup a plugin to be automatically wired in the corresponding configuration file in
Studio (for example, a form control, will be wired to the Content Type Editor Configuration file)
during the installation, add the following to your ``craftercms-plugin.yaml`` descriptor file

.. code-block:: yaml
   :linenos:
   :caption: *Setup auto-wiring to Studio in descriptor file*

   installation:
    - type: preview-app
      parentXpath: //widget[@id='craftercms.components.ToolsPanel']
      elementXpath: //plugin[@id='org.craftercms.sampleComponentLibraryPlugin.components.reactComponent']
      element:
        name: configuration
        children:
        - name: widgets
          children:
          - name: widget
            attributes:
            - name: id
              value: org.craftercms.sampleComponentLibraryPlugin.components.reactComponent
            children:
            - name: plugin
              attributes:
              - name: id
                value: org.craftercms.plugin.sidebar
              - name: type
                value: sidebar
              - name: name
                value: react-sample
              - name: file
                value: index.modern.js

where:

- ``installation.type`` is the type of plugin for auto-wiring in Studio.
  Available values are **form-control**, **form-datasource**, **preview-app**, **site-filter** and **site-context**
- ``installation.parentXpath`` is an XPath selector for the element where the plugin will be added,
  required when installation-type is *preview-app*
- ``installation.elementXpath`` is an XPath selector to check if the plugin is already present in the configuration and used to remove the config when the plugin is uninstalled
- ``installation.element.name`` is the element name to be wired in your project configuration file so the plugin will
  show up in Studio
  Available values are **control** (for *form-control* installation type), **datasource** (for *form-datasource* installation type) and for *preview-app* installation type, the start of the section the plugin needs to be inserted in, e.g. *configuration*, etc.
- ``installation.element.children`` contains any number of **name** and **children** describing your plugin, such
  as the icon to be used by your plugin if applicable, or the plugin location, where:

  - ``name`` is the name of what's being described, e.g. **plugin** or **icon**
  - ``children`` contains any number of **name** and **value** and can contain the class (icon), plugin id, plugin
    type, plugin name and plugin files/folders (plugin location) and its corresponding  values

|

.. _plugin-descriptor-servlet-filter:

Below are examples on how to setup auto-wiring in Studio for various plugin types:

.. tabs::
   .. tab:: form-control

      Below is a sample auto-wiring setup for a form control.

      .. code-block:: yaml
         :caption: *Example installation for a form-control*
         :linenos:

         installation:
           - type: form-control
             elementXpath: //control/plugin[pluginId='org.craftercms.plugin.control']
             element:
               name: control
               children:
                 - name: plugin
                   children:
                     - name: pluginId
                       value: org.craftercms.plugin.control
                     - name: type
                       value: control
                     - name: name
                       value: text-input
                     - name: filename
                       value: main.js
                 - name: icon
                   children:
                     - name: class
                       value: fa-pencil-square-o

   .. tab:: datasource

      Below is a sample auto-wiring setup for a data source.

      .. code-block:: yaml
         :caption: *Example installation for a data source*
         :linenos:

         installation:
           - type: form-datasource
             elementXpath: //datasource/plugin[pluginId='org.craftercms.plugin.datasource']
             element:
               name: datasource
               children:
                 - name: plugin
                   children:
                     - name: pluginId
                       value: org.craftercms.plugin.datasource
                     - name: type
                       value: datasource
                     - name: name
                       value: text-input
                     - name: filename
                       value: main.js
                     - name: icon
                       children:
                     - name: class
                       value: fa-pencil-square-o

   .. tab:: preview-app

      Below is a sample auto-wiring setup for a preview-app.

      .. code-block:: yaml
         :caption: *Example installation for a preview-app*
         :linenos:

         installation:
           - type: preview-app
             parentXpath: //widget[@id='craftercms.components.ToolsPanel']
             elementXpath: //plugin[@id='org.craftercms.sampleComponentLibraryPlugin.components.reactComponent']
             element:
               name: configuration
               children:
               - name: widgets
                 children:
                 - name: widget
                   attributes:
                   - name: id
                     value: org.craftercms.sampleComponentLibraryPlugin.components.reactComponent
                   children:
                   - name: plugin
                     attributes:
                     - name: id
                       value: org.craftercms.plugin
                     - name: type
                       value: sidebar
                     - name: name
                       value: react-sample
                     - name: file
                       value: index.modern.js

   .. tab:: servlet-filter

      Below is a sample auto-wiring setup for a site filter.

      .. code-block:: yaml
         :caption: *Example installation for a site-filter*
         :linenos:

         installation:
           - type: site-filter
             elementXpath: //filter/script[text()='/scripts/filters/plugins/org/craftercms/plugin/filter/myFilter.groovy']
             element:
               name: filter
               children:
                 - name: script
                   value: '/scripts/filters/plugins/org/craftercms/plugin/filter/myFilter.groovy'
                 - name: mapping
                   children:
                     - name: include
                       value: '/**'

   .. tab:: site-context

      Below is a sample auto-wiring setup for the site context.

      .. code-block:: yaml
         :caption: *Example installation for the site-context*
         :linenos:

         installation:
           - type: site-context
             elementXpath: //bean[@id='myBean']
             element:
               name: bean
               attributes:
                 - name: id
                   value: myBean
                 - name: class
                   value: plugins.org.craftercms.plugin/context/MyClass
               children:
                 - name: property
                   attributes:
                     - name: name
                       value: siteItemService
                     - name: ref
                       value: crafter.siteItemService


See :ref:`here <plugins-authoring-guides>` for examples of plugins auto-wired in Studio.

.. _passing-parameters-via-plugins:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Passing Parameters to Project via Plugins
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Some parameters may need to be passed to the project instead of left in the plugin, say, AWS credentials,
CommerceTools credentials, etc. CrafterCMS supports passing parameters to projects from plugins.

To add parameters to be passed to projects via a plugin, simply add the following to the ``craftercms-plugin.yaml`` file

.. code-block:: yaml

   parameters:
    - label: My Parameter Label
      name: myParam
      type: STRING
      description: My parameter
      required: true

where:

- ``label``: Label to display for parameter on Create Project dialog
- ``name``: Name of the parameter in *camelCase* notation
- ``type``: Type of the parameter, possible values are ``STRING`` and ``PASSWORD``. The default is ``STRING``
- ``description``: Description of the parameter
- ``required``: Indicates whether the parameter is required. The default is ``true``


To use the parameters in scripts and template files, simply use ``pluginConfig``, e.g. ``pluginConfig.getString("PARAM_NAME")`` where PARAM_NAME is the name of the parameter.

"""""""
Example
"""""""
Let's take a look at an example of a plugin with parameters.  We'll use the `CrafterCMS Stripe Plugin <https://github.com/craftercms/stripe-plugin>`__ plugin (available from the Marketplace) to see how parameters are added to plugins that are then passed on to the projects after installing.

#. Let's take a look at the parameters in the ``craftercms-plugin.yaml`` file of the plugin. In  the  ``craftercms-plugin.yaml`` in the plugin folder, scroll to the following lines in the file:

   .. code-block:: yaml
      :linenos:
      :caption: *stripe-plugin/craftercms-plugin.yaml*

      ...

      parameters:
        - label: Publishable key
          name: publishableKey
          description: Stripe Publishable key
        - label: Secret key
          name: secretKey
          description: Stripe secret key
        - label: Callback domain
          name: callbackDomain
          description: Callback domain of your application

#. Next, we'll take a look at how to use the parameters passed in your scripts using ``pluginConfig`` in the ``checkout-session.get.groovy`` file

   .. code-block:: groovy
       :caption: *stripe-plugin/delivery/scripts/rest/plugins/org/craftercms/plugin/stripe/checkout-session.get.groovy*

       ...

       Stripe.apiKey = pluginConfig.getString('secretKey')

       ...

#. Let's now take a look at the plugin parameters in action during the plugin installation.
   Open the project you want to install the ``stripe-plugin`` into.  Next, click on ``Project Tools`` -> ``Plugin Management`` -> ``Search & Install`` and search for ``stripe``.  For the example below, we'll be installing the plugin in the ``Editorial`` project.

   .. image:: /_static/images/developer/plugins/search-for-stripe.webp
      :width: 60%
      :alt: Search for stripe in "Plugin Management"
      :align: center

   |

#. Click on the ``Install`` button to install the ``stripe-plugin``.  The next screen will now ask the user for the parameters we saw listed in the ``craftercms-plugin.yaml`` file:

   .. image:: /_static/images/developer/plugins/stripe-plugin-configuration.webp
      :width: 60%
      :alt: User prompted for values for parameters in the plugin
      :align: center

   |

^^^^^^^^^^^^^^^^^^^
Directory Structure
^^^^^^^^^^^^^^^^^^^

A plugin consist of a group of files that are copied to the project repository when installed.
To create your own plugin, your files/folders needs to go in the corresponding type of
plugin folder, following the structure below:


- ``craftercms-plugin.yaml``: the plugin descriptor, see :ref:`CrafterCMS Plugin Descriptor <plugin-descriptor-file>` for details

- ``.crafter``

  - ``screenshots``

    - ``default.png`` : the default representative image of the plugin placed under the default path ``.crafter/screenshots/``

- ``authoring``: contains all files related to Crafter Studio extensions

  - ``content-types``

    - ``component``: contains configuration files for components, see :ref:`below <example-component-plugin>` for an example
    - ``page``: contains configuration files for pages

  - ``static-assets``: contains files for Studio UI plugins
  - ``scripts``

    - ``classes``: contains Groovy classes
    - ``rest``: contains REST Groovy scripts

- ``delivery``: contains all files related to Crafter Engine extensions

  - ``templates``: contains Freemarker templates
  - ``static-assets``: contains binary files
  - ``scripts``

    - ``classes``: contains Groovy classes
    - ``components``: contains Groovy scripts for components
    - ``controllers``: contains Groovy controllers
    - ``filters``: contains Groovy filters
    - ``pages``: contains Groovy scripts for pages
    - ``rest``: contains Groovy REST scripts

An easy way to develop new plugins is to start with an empty project and when all the files are ready
copy them to a new repository following the given structure. However all references should be updated
to match the final destination of the file:

+------------------------------------------+---------------------------------------------------------------+
| Location in the plugin repository        | Location in the project repository                            |
+==========================================+===============================================================+
| ``authoring/content-types/component/*``  | ``/config/studio/content-types/component/<plugin id path>/*`` |
+------------------------------------------+---------------------------------------------------------------+
| ``authoring/content-types/page/*``       | ``/config/studio/content-types/page/<plugin id path>/*``      |
+------------------------------------------+---------------------------------------------------------------+
| ``authoring/static-assets/*``            | ``/config/studio/static-assets/plugins/<plugin id path>/*``   |
+------------------------------------------+---------------------------------------------------------------+
| ``authoring/scripts/classes/*``          | ``/config/studio/scripts/classes/<plugin id path>/*``         |
+------------------------------------------+---------------------------------------------------------------+
| ``authoring/scripts/rest/*``             | ``/config/studio/plugins/scripts/rest/<plugin id path>/*``    |
+------------------------------------------+---------------------------------------------------------------+
| ``delivery/templates/*``                 | ``/templates/<plugin id path>/*``                             |
+------------------------------------------+---------------------------------------------------------------+
| ``delivery/static-assets/*``             | ``/static-assets/<plugin id path>/*``                         |
+------------------------------------------+---------------------------------------------------------------+
| ``delivery/scripts/classes/*``           | ``/scripts/classes/<plugin id path>/*``                       |
+------------------------------------------+---------------------------------------------------------------+
| ``delivery/scripts/components/*``        | ``/scripts/components/<plugin id path>/*``                    |
+------------------------------------------+---------------------------------------------------------------+
| ``delivery/scripts/controllers/*``       | ``/scripts/controllers/<plugin id path>/*``                   |
+------------------------------------------+---------------------------------------------------------------+
| ``delivery/scripts/filters/*``           | ``/scripts/filters/<plugin id path>/*``                       |
+------------------------------------------+---------------------------------------------------------------+
| ``delivery/scripts/pages/*``             | ``/scripts/pages/<plugin id path>/*``                         |
+------------------------------------------+---------------------------------------------------------------+
| ``delivery/scripts/rest/*``              | ``/scripts/rest/<plugin id path>/*``                          |
+------------------------------------------+---------------------------------------------------------------+

Your plugin is installed in a project via the :ref:`Marketplace <plugin-install-from-marketplace>`
or via :ref:`copy-plugin <crafter-cli-command-help>` CLI command, your plugin descriptor containing the
directory structure will be read and corresponding plugin files copied to the project.

.. _ui-plugin-directory-structure:

"""""""""""""""""""""""""""""
UI Plugin Directory Structure
"""""""""""""""""""""""""""""

Authoring plugins should use the following directory structure:

``{PLUGIN_DIRECTORY}/authoring/static-assets/{ID}/{CATEGORY}/{NAME}/``

where:

- **ID**: A directory named after the plugin id (e.g. ``org.craftercms.sample``)
- **CATEGORY**: A directory named after the `type` of plugin (e.g. control, datasource, sidebar, app, lib, etc.)
- **NAME**: A directory named after the plugin name

  - Plugin sources and/or build output of the plugin would be placed here.

"""""""""""""""""""""""""""
Example Directory Structure
"""""""""""""""""""""""""""

Here's an example directory structure for a plugin with the *pluginId* value set to ``org.craftercms.sample`` :

.. code-block:: text
      :linenos:
      :caption:  *Directory structure example*

      {your_plugin_folder}/
        craftercms-plugin.yaml
        .crafter/
          screenshots/
            default.png
        authoring/
          static-assets/
            plugins/
              org/
                craftercms/
                  sample/
                    controls/
                      color-picker/
                        main.js
                        style.css
        delivery/
          scripts/
            rest/
              org/
                craftercms/
                  sample/
                    hello.groovy
          templates/
            org/
              craftercms/
                sample/
                  head.ftl

The repository `here <https://github.com/craftercms/craftercms-marketplace-plugin-template>`__ contains
a skeleton plugin directory structure for your use. You can use the repository to help you start
create your plugin. Simply fill in the plugin descriptor file ``craftercms-plugin.yaml`` file and
remove items you don't need

.. _plugins-create-your-plugin-files:

^^^^^^^^^^^^^^^^^^
Create your plugin
^^^^^^^^^^^^^^^^^^
To create a plugin, a descriptor file ``craftercms-plugin.yaml`` is required.
:ref:`This <plugin-descriptor-file>` article contains more information on what's in the file and
an example file.

The next requirement for creating your plugin are the plugin files.
Depending on the plugin type you are creating, this could be a JavaScript file, Freemarker template files,
Groovy file, XML file, etc. The plugin file/s should then be placed in a directory structure as described
above depending on the plugin created. For example, say your plugin is a component content type, your
plugin files should be placed under the directory  ``authoring/content-types/component``

  .. code-block:: text
     :caption: *Example directory structure for a component content type plugin*
     :emphasize-lines: 4-7

     authoring/
       content-types/
         component/
           <your_component_name>/
             config.xml
             controller.groovy
             form-definition.xml


CrafterCMS uses a default path to look for a default representative image of a plugin, the url ``../.crafter/screenshots/``. Here's a sample plugin files/directory with a default image to represent the plugin:

  .. code-block:: text
     :caption: *Example directory structure for a component content type plugin with a default representative image*
     :emphasize-lines: 1-3

     .crafter/
       screenshots/
         default.png
     authoring/
       content-types/
         component/
           <your_component_name>/
             config.xml
             controller.groovy
             form-definition.xml


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Reusing libraries written in Java in your plugin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Some users may have some libraries written in Java that they may want to reuse in their plugin.
To reuse those libraries, do the following:

* Publish your JARs to Maven Central
* Pull the JARs from Maven in a small Groovy script in your plugin via Grapes

  If the JAR is available in Maven Central

  .. code-block:: groovy
     :caption: *Pull JAR available in Maven Central via Grapes*

     @Grab(value='com.example:my-java-plugin:1.0.0', initClass=false)
     import com.example.java.Plugin // This class is made up, it can be anything

  |

  If the JAR is in a private Maven Repo

  .. code-block:: groovy
     :caption: *Pull JAR available in private Maven Repo via Grapes*

     @GrabResolver(name='my-repo', root='https://maven.example.com/')
     @Grab(value='com.example:my-java-plugin:1.0.0', initClass=false)
     import com.example.java.Plugin // This class is made up, it can be anything


|hr|

----------------------
Publishing Your Plugin
----------------------

To publish a plugin in the Crafter Marketplace you can follow the instructions in :ref:`marketplace-create-extensions`

|hr|

---------------------------
Retrieving Extension Assets
---------------------------

At the low level, Crafter Studio APIs provide an endpoint that gets a file for a given plugin, (i.e.
:base_url:`getPluginFile <_static/api/studio.html#tag/plugin/operation/getPluginFile>`). This API takes care of setting all the right headers
for whatever kind of asset you're retrieving (JavaScript, CSS, image, etc). There are also higher level
mechanisms for developers to load and use plugins like the :ref:`Plugin host <plugin-host>`
or through the :ref:`Crafter CLI <crafter-cli>`.

If you need to *manually* load assets from your plugin (e.g. set the ``src`` of a ``<script />``,
set the ``href`` of a ``<link />``, set the ``src`` of an ``<img />``, etc.), you should use the following URL

``/studio/1/plugin/file?siteId={siteId}&type={yourPluginType}&name={yourPluginName}&file={fileName}``

If your extensions is nested on a plugin id directory, you should also include the ``pluginId`` argument.

``/studio/1/plugin/file?siteId={siteId}&pluginId={yourPluginId}&type={yourPluginType}&name={yourPluginName}&file={fileName}``

|hr|

-------------------
Installing a Plugin
-------------------

Plugins may be installed a couple of ways depending on where the plugins are located:

* :ref:`plugin-install-from-marketplace`
* :ref:`plugin-install-from-local-folder`

After installing a plugin, depending on the plugin you created, the plugin will be be installed under the:

* {siteRoot}/config/studio/static-assets/plugins/{yourPluginId}/{yourPluginType}/{yourPluginName}/
* {siteRoot}/config/studio/content-types/component/{yourPluginType}/{yourPluginName}/
* {siteRoot}/config/studio/content-types/page/{yourPluginType}/{yourPluginName}/
* {siteRoot}/templates/{yourPluginId}/{yourPluginType}/{yourPluginName}
* {siteRoot}/static-assets/{yourPluginId}/{yourPluginType}/{yourPluginName}
* {siteRoot}/scripts/{yourScriptType}/{yourPluginId}/{yourPluginType}/{yourPluginName}


.. _plugin-install-from-marketplace:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Install a plugin from the Crafter Marketplace
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Once a plugin is published to the Crafter Marketplace it can be installed using the Crafter Studio user interface
or the REST API:

.. note::
    To access the Plugin Management tool or use the install plugin REST API your user needs to have the following
    permissions:

    - ``list_plugins``
    - ``install_plugins``


For more information on installing plugins from the Crafter Marketplace using Crafter Studio, see :ref:`plugin-management`

.. _plugin-install-from-local-folder:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Install a plugin in development from a Studio local folder
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
For developers who want to test out their plugins before submitting to the Crafter Marketplace, CrafterCMS
provides a CLI command ``copy-plugin`` for installing a plugin from a Studio local folder into a project using
the ``crafter-cli``.

Let's take a look at an example to show how to install a plugin using the CrafterCMS cli ``copy-plugin`` command.
We'll use a project named ``myeditorial`` where we will be installing the plugin, and the plugin we want to
install located in ``/Users/myuser/plugins/sidebar-plugin``

To install the plugin ``sidebar-plugin`` to our project ``myeditorial``, we'll run the ``copy-plugin`` command
like below:

.. code-block:: console

      ./crafter-cli copy-plugin -e local -s myeditorial --path /users/myuser/plugins/sidebar-plugin


Remember that the connection to CrafterCMS needs to be setup via the ``add-environment`` command before
using any of the ``crafter-cli`` commands.

For more information on the ``copy-plugin`` command, see the :ref:`command line help<crafter-cli-command-help>`

|hr|

.. _example-component-plugin:

-------------------------
Example Creating a Plugin
-------------------------

Let's take a look at an example of creating a component content type plugin named ``My Component``

First, we'll configure the descriptor file ``craftercms-plugin.yaml`` file for our plugin

.. literalinclude:: /_static/code/developer/plugins/component-content-type/craftercms-plugin.yaml
   :language: yaml
   :caption: *Descriptor file for the example component content type plugin*
   :linenos:

|

We'll then create the directory structure for a component content type plugin ``authoring/content-types/component/*``,
to place our plugin files in,

   .. code-block:: text
      :caption: *Directory structure for component content type plugin My Component*
      :emphasize-lines: 4-7

      authoring/
        content-types/
          component/
            mycomponent/
              config.xml
              controller.groovy
              form-definition.xml

|

The plugin is now ready to be tested. We'll install our plugin located  under ``/users/myuser/component-plugin``
using the ``crafter-cli`` command ``copy-plugin`` to test it out to a project named editorial

.. code-block:: console

   ./crafter-cli copy-plugin -e local -s editorial --path /users/myuser/component-plugin

|

After installing our plugin, we can now verify that our component plugin is available in |projectTools| Content Types

.. figure:: /_static/images/developer/plugins/project-plugins/plugins-sample-component.webp
   :align: center
   :alt: Example component content type plugin now available in project editorial
   :width: 80%

|

|hr|

------------------------------
Some More Examples & Resources
------------------------------

Here are some more examples and resources to help you create your plugins

.. _plugins-authoring-guides:

^^^^^^
Guides
^^^^^^

Below are more authoring examples of creating plugins:

.. toctree::
   :maxdepth: 1
   :titlesonly:

   resources/plugin-ui-example
   resources/full-screen-plugin-example


^^^^^^^^^^^^^^^
Example Plugins
^^^^^^^^^^^^^^^

CrafterCMS also provides various plugin examples available from the `Marketplace <https://craftercms.com/marketplace>`__ to help you create your own plugins. These plugins can be installed to your project using ``Plugin Management`` in |projectTools| through the Studio UI. See :ref:`plugin-management` for more information on installing plugins from the Crafter Marketplace.

* `Cliengo plugin <https://github.com/craftercms/chatbot-plugin/tree/cliengo>`__ to add Cliengo to a project
* `Image Carousel plugin <https://github.com/craftercms/carousel-plugin>`__ to add a highly configurable carousel plugin based on `Tiny Slider <https://github.com/ganlanyuan/tiny-slider/tree/v2.9.3>`__
* `Contact Form plugin <https://github.com/craftercms/contact-form-plugin>`__ to add one or more contact forms to your project
* `Google Analytics plugin <https://github.com/craftercms/google-analytics-plugin>`__ to add Google Analytics to your project
* `Google Maps plugin <https://github.com/craftercms/googlemaps-plugin>`__ to add Google Maps to your project
* `Google Tag Manager plugin <https://github.com/craftercms/google-tag-manager-plugin>`__ to add Google Tag Manager to your project
* `Sitemap plugin <https://github.com/craftercms/sitemap-plugin>`__ to generate a sitemap for your project
* `YouTube plugin <https://github.com/craftercms/youtube-plugin>`__ to add YouTube videos to your project
* `Redirect plugin <https://github.com/craftercms/redirect-plugin>`__ to add file-based redirects in your project
