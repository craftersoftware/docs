:is-up-to-date: True
:last-updated: 4.5.2

.. index:: Crafter Studio UI Plugin Examples, Studio Plugins, Plugins

.. meta::
   :description: Step-by-step examples of creating Crafter Studio plugins — UI widgets, Form Engine controls, and Form Engine data sources — including auto-wiring and installation with crafter-cli.
   :keywords: Studio plugin, UI plugin, form control plugin, form data source plugin, sidebar, Experience Builder, toolbar, dashboard, project tools, navigation menu, CrafterCMS, Crafter Studio, craftercms-plugin.yaml

.. _plugin-ui-example:

================================
Crafter Studio Plugin Examples
================================
Crafter Studio supports several types of authoring plugins. **UI widget plugins** add React components to
extension points in the Studio interface. **Form Engine control plugins** add custom input controls to content
types. **Form Engine data source plugins** add custom data pickers that controls use to retrieve content.

This article walks through creating each type of plugin and wiring it into Studio.

Here's a list of the plugin examples described in this article:

.. list-table::
   :header-rows: 1
   :widths: 22 20 58

   * - Plugin Type
     - Plugin ``type``
     - Where it appears
   * - :ref:`Sidebar <plugin-sidebar-example>`
     - ``sidebar``
     - The panel on the left of Studio
   * - :ref:`Experience Builder Panel <plugin-experience-builder-plugin-example>`
     - ``experiencebuilder``
     - The panel on the right of Studio, shown in Edit/Move mode
   * - :ref:`Toolbar <plugin-toolbar-example>`
     - ``toolbar``
     - The fixed bar at the top of Studio
   * - :ref:`Dashboard <plugin-dashboard-example>`
     - ``dashboard``
     - A dashlet on the project dashboard
   * - :ref:`Project Tools <plugin-project-tools-example>`
     - ``project-tool``
     - A tool under |projectTools|
   * - :ref:`Navigation Menu <plugin-navmenu-example>`
     - ``navmenu``
     - A tile in the Navigation Menu (Launcher)
   * - :ref:`Translations <plugin-translations-example>`
     - ``library``
     - A tile in the Navigation Menu (Launcher)
   * - :ref:`Form Engine Control <building-plugins-controls>`
     - ``control``
     - A control in the Content Type Editor
   * - :ref:`Form Engine Data Source <building-plugins-form-ds>`
     - ``datasource``
     - A data source in the Content Type Editor

All the examples below use a project called ``My Editorial`` created using the **Website Editorial** blueprint.

|hr|

------------------------
Studio UI Widget Plugins
------------------------

.. _plugin-ui-example-process:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Creating Your Studio UI Widget Plugin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Here are the steps for creating your Studio UI widget plugin:

#. Create the folder structure
#. Create the plugin's JavaScript file
#. Auto-wire the plugin
#. Install the plugin
#. See it in action

The steps are identical for every UI widget location. The only two things that change are the plugin
``type`` (which is also the ``CATEGORY`` directory) and the ``installation`` block that positions the widget.
Both of those are provided in the :ref:`Positioning the Widget in the UI <plugin-ui-example-positioning>`
section for your target location.

#. **Create the folder structure.** |br|
   We'll follow the convention listed in :ref:`ui-plugin-directory-structure`,
   ``{PLUGIN_DIRECTORY}/authoring/static-assets/plugins/{ID}/{CATEGORY}/{NAME}/``, where ``CATEGORY`` is the
   plugin ``type`` for your target location and ``NAME`` is your plugin name.

   In a local folder, create the descriptor file ``craftercms-plugin.yaml`` and set ``plugin.id`` (for
   example, ``org.craftercms.plugin.exampleui``), then create the directory structure. See the target
   location's section below for the exact ``CATEGORY``/``NAME`` and a concrete tree.

#. **Create the plugin's JavaScript file.** |br|
   Follow the instructions in the plugin example
   `here <https://github.com/craftercms/authoring-ui-plugin-examples/tree/master/examples/component-library>`__,
   which will generate the ``index.js`` file.

   Inside the ``{NAME}`` folder, create two empty files, ``index.css`` and ``script.js``, and place the
   ``index.js`` file in it.

#. **Auto-wire the plugin.** |br|
   To have the plugin automatically wired into the corresponding configuration file
   in Studio (for all these UI locations, that is the User Interface Configuration file) during installation,
   add an ``installation`` block to your ``craftercms-plugin.yaml`` descriptor file. The ``parentXpath`` and
   ``element`` differ by location — use the snippet from your target location's section below.

   .. note::
      Remember to use the same value used in ``plugin.id`` (found at the top of the descriptor file) for the
      ``installation`` section plugin id.

   See :ref:`here <plugin-descriptor-auto-wiring>` for more information on how auto-wiring works and what each
   field in the ``installation`` block means.

#. **Install the plugin.** |br|
   After placing your plugin files and setting up auto-wiring, install the plugin for
   testing/debugging using the ``crafter-cli`` command ``copy-plugin``.

   When running a ``crafter-cli`` command, the connection to CrafterCMS needs to be setup via the
   :ref:`add-environment <crafter-cli-add-environment>` command. Once the connection has been established,
   install the plugin to the project ``my-editorial`` by running the following (adjust the ``--path`` to
   point at your plugin folder):

   .. code-block:: bash

       ./crafter-cli copy-plugin -e local -s my-editorial --path /users/myuser/myplugins/<plugin-folder>

#. **See it in action.** |br|
   Reload Studio and open the relevant part of the UI. The exact place to look, along
   with the resulting auto-wired configuration, is shown in your target location's section below.

|hr|

.. _plugin-ui-example-positioning:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Positioning the Widget in the UI
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each section below provides the location-specific pieces for :ref:`the process above <plugin-ui-example-process>`:
the plugin ``type``, the directory structure, the ``installation`` auto-wiring block, where to see the widget
in action, and the resulting section in the User Interface Configuration file after installation.

.. _plugin-sidebar-example:

"""""""
Sidebar
"""""""
The Sidebar is the panel located on the left side of Studio, containing the ``Dashboard``, path navigators such
as ``Pages`` and ``Components``, and |projectTools|.

For this example, the plugin ``type`` (``CATEGORY``) is ``sidebar``, the ``NAME`` is ``react-sample``, and the
``plugin.id`` is ``org.craftercms.plugin.examplesidebar``.

.. code-block:: text
   :caption: *Sidebar Plugin Directory Structure*

   <plugin-folder>/
     craftercms-plugin.yaml
     authoring/
       static-assets/
         plugins/
           org/
             craftercms/
               plugin/
                 examplesidebar/
                   sidebar/
                     react-sample/

|

Add the following ``installation`` block to your ``craftercms-plugin.yaml`` descriptor file:

.. code-block:: yaml
   :linenos:
   :caption: *craftercms-plugin.yaml*
   :emphasize-lines: 17-18

   installation:
     - type: preview-app
       parentXpath: //widget[@id='craftercms.components.ToolsPanel']
       elementXpath: //plugin[@id='org.craftercms.sampleSidebarPlugin.components.reactComponent']
       element:
         name: configuration
         children:
         - name: widgets
           children:
           - name: widget
             attributes:
             - name: id
               value: org.craftercms.sampleSidebarPlugin.components.reactComponent
             children:
             - name: plugin
               attributes:
               - name: id
                 value: org.craftercms.plugin.examplesidebar
               - name: type
                 value: sidebar
               - name: name
                 value: react-sample
               - name: file
                 value: index.js

|

To see the plugin in action, click on the CrafterCMS logo at the top left of your browser to open the sidebar:

.. image:: /_static/images/developer/plugins/project-plugins/sidebar-plugin-in-action.webp
   :align: center
   :alt: Sidebar project plugin in action
   :width: 30%

|

Here's the auto-wired section in the configuration after installing the plugin:

.. code-block:: xml
   :linenos:
   :emphasize-lines: 31-36

   <siteUi>
     <widget id="craftercms.components.ToolsPanel">
       <configuration>
       <widgets>
         <widget id="craftercms.components.ToolsPanelEmbeddedAppViewButton">
            <configuration>
               <title id="words.dashboard" defaultMessage="Dashboard"/>
               <icon id="@mui/icons-material/DashboardRounded"/>
               <widget id="craftercms.components.Dashboard"/>
            </configuration>
         </widget>
         <widget id="craftercms.components.ToolsPanelPageButton">
            <configuration>
               <title id="previewSiteExplorerPanel.title" defaultMessage="Site Explorer"/>
               <icon id="craftercms.icons.SiteExplorer"/>
            ...
         </widget>
         <widget id="craftercms.components.ToolsPanelPageButton">
            <permittedRoles>
               <role>admin</role>
               <role>developer</role>
            </permittedRoles>
            <configuration>
               <title id="siteTools.title" defaultMessage="Project Tools"/>
               <icon id="@mui/icons-material/TuneRounded"/>
               <widgets>
                  <widget id="craftercms.components.SiteToolsPanel"/>
               </widgets>
            </configuration>
         </widget>
         <widget id="org.craftercms.sampleSidebarPlugin.components.reactComponent">
            <plugin id="org.craftercms.plugin.examplesidebar"
                    type="sidebar"
                    name="react-sample"
                    file="index.js"/>
         </widget>
       </widgets>
     </configuration>
   </widget>
   ...

|

.. _plugin-experience-builder-plugin-example:

""""""""""""""""""""""""
Experience Builder Panel
""""""""""""""""""""""""
The Experience Builder panel is the panel on the right of Studio that is enabled by clicking on ``Edit mode``
(pencil icon) or ``Move mode`` (two vertical ellipsis icon) on the top right of Studio, or by hitting the
``e`` or ``m`` key on your keyboard.

.. image:: /_static/images/developer/plugins/project-plugins/experience-builder-panel.webp
   :align: center
   :alt: Experience Builder Panel
   :width: 80%

|

For this example, the plugin ``type`` (``CATEGORY``) is ``experiencebuilder``, the ``NAME`` is
``test-experiencebuilder``, and the ``plugin.id`` is ``org.craftercms.plugin.exampleexperiencebuilder``.

.. code-block:: text
   :caption: *Experience Builder Plugin Directory Structure*

   <plugin-folder>/
     craftercms-plugin.yaml
     authoring/
       static-assets/
         plugins/
           org/
             craftercms/
               plugin/
                 exampleexperiencebuilder/
                   experiencebuilder/
                     test-experiencebuilder/

|

Add the following ``installation`` block to your ``craftercms-plugin.yaml`` descriptor file:

.. code-block:: yaml
   :linenos:
   :caption: *craftercms-plugin.yaml*
   :emphasize-lines: 17-18

   installation:
     - type: preview-app
       parentXpath: //widget[@id='craftercms.components.ICEToolsPanel']
       elementXpath: //plugin[@id='org.craftercms.sampleExperienceBuilderPlugin.components.reactComponent']
       element:
         name: configuration
         children:
         - name: widgets
           children:
           - name: widget
             attributes:
             - name: id
               value: org.craftercms.sampleExperienceBuilderPlugin.components.reactComponent
             children:
             - name: plugin
               attributes:
               - name: id
                 value: org.craftercms.plugin.exampleexperiencebuilder
               - name: type
                 value: experiencebuilder
               - name: name
                 value: test-experiencebuilder
               - name: file
                 value: index.js

|

To see the plugin in action, click on the pencil icon at the top right of your browser to open the Experience
Builder panel:

.. image:: /_static/images/developer/plugins/project-plugins/experiencebuilder-plugin-in-action.webp
   :align: center
   :alt: Experience Builder plugin in action
   :width: 30%

|

Here's the auto-wired section in the configuration after installing the plugin:

.. code-block:: xml
   :linenos:
   :emphasize-lines: 17-22

   <siteUi>
     ...
     <widget id="craftercms.components.ICEToolsPanel">
       <configuration>
         <widgets>
           <widget id="craftercms.components.ToolsPanelPageButton">
             <configuration>
               <target id="icePanel"/>
               <title id="previewSearchPanel.title" defaultMessage="Search"/>
               <icon id="@mui/icons-material/SearchRounded"/>
               <widgets>
                 <widget id="craftercms.components.PreviewSearchPanel"/>
               </widgets>
             </configuration>
           </widget>
           ...
           <widget id="org.craftercms.sampleExperienceBuilderPlugin.components.reactComponent">
             <plugin id="org.craftercms.plugin.exampleexperiencebuilder"
                     type="experiencebuilder"
                     name="test-experiencebuilder"
                     file="index.js"/>
           </widget>
          </widgets>
       </configuration>
      </widget>
      ...

|

.. _plugin-toolbar-example:

"""""""
Toolbar
"""""""
The toolbar is a fixed element at the top of Studio. It provides contextual workflow and other options relative
to the page you are looking at, content you have selected, or tool you are using.

.. image:: /_static/images/developer/plugins/project-plugins/studio-toolbar.webp
   :align: center
   :alt: Studio Toolbar
   :width: 80%

|

For this example, the plugin ``type`` (``CATEGORY``) is ``toolbar``, the ``NAME`` is ``test-toolbar``, and the
``plugin.id`` is ``org.craftercms.plugin.exampletoolbar``.

.. code-block:: text
   :caption: *Toolbar Plugin Directory Structure*

   <plugin-folder>/
     craftercms-plugin.yaml
     authoring/
       static-assets/
         plugins/
           org/
             craftercms/
               plugin/
                 exampletoolbar/
                   toolbar/
                     test-toolbar/

|

Add the following ``installation`` block to your ``craftercms-plugin.yaml`` descriptor file. Note that a toolbar
widget is wired into one of the toolbar's sections (``leftSection``, ``middleSection``, or ``rightSection``);
this example uses ``rightSection``:

.. code-block:: yaml
   :linenos:
   :caption: *craftercms-plugin.yaml*
   :emphasize-lines: 19-20

   installation:
     - type: preview-app
       parentXpath: //widget[@id='craftercms.components.PreviewToolbar']
       elementXpath: //plugin[@id='org.craftercms.sampleToolbarPlugin.components.reactComponent']
       element:
         name: configuration
         children:
         - name: rightSection
           children:
           - name: widgets
             children:
             - name: widget
               attributes:
               - name: id
                 value: org.craftercms.sampleToolbarPlugin.components.reactComponent
               children:
               - name: plugin
                 attributes:
                 - name: id
                   value: org.craftercms.plugin.exampletoolbar
                 - name: type
                   value: toolbar
                 - name: name
                   value: test-toolbar
                 - name: file
                   value: index.js

|

To see the plugin in action, refresh your browser:

.. image:: /_static/images/developer/plugins/project-plugins/toolbar-plugin-in-action.webp
   :align: center
   :alt: Toolbar project plugin in action
   :width: 100%

|

Here's the auto-wired section in the configuration after installing the plugin:

.. code-block:: xml
   :linenos:
   :emphasize-lines: 31-36

   <siteUi>
   ...
     <widget id="craftercms.components.PreviewToolbar">
       <configuration>
         <leftSection>
           <widgets>
             <widget id="craftercms.components.SiteSwitcherSelect"/>
             <widget id="craftercms.components.QuickCreate"/>
           </widgets>
         </leftSection>
         <middleSection>
           <widgets>
             <widget id="craftercms.components.PreviewAddressBar"/>
           </widgets>
         </middleSection>
         <rightSection>
           <widgets>
             <widget id="craftercms.components.EditModesSwitcher"/>
             <widget id="craftercms.components.PublishingStatusButton">
               <configuration>
                 <variant>icon</variant>
               </configuration>
             </widget>
             <widget id="craftercms.components.WidgetDialogIconButton">
               <configuration>
                 <title id="words.search" defaultMessage="Search"/>
                 <icon id="@mui/icons-material/SearchRounded"/>
                 <widget id="craftercms.components.EmbeddedSearchIframe"/>
               </configuration>
             </widget>
             <widget id="org.craftercms.sampleToolbarPlugin.components.reactComponent">
               <plugin id="org.craftercms.plugin.exampletoolbar"
                       type="toolbar"
                       name="test-toolbar"
                       file="index.js"/>
             </widget>
           </widgets>
         </rightSection>
       </configuration>
     </widget>
     ...

|

.. _plugin-dashboard-example:

"""""""""
Dashboard
"""""""""
The dashboard contains different dashlets that show, at a glance, all items currently in workflow, all items
recently modified by the current user, etc. Dashlets shown vary depending on the user's role. For more
information on the Dashboard, see :ref:`here <project-dashboard>`.

.. image:: /_static/images/content-author/project-dashboard.webp
   :align: center
   :alt: Studio Dashboard
   :width: 80%

|

For this example, the plugin ``type`` (``CATEGORY``) is ``dashboard``, the ``NAME`` is ``test-dashboard``, and
the ``plugin.id`` is ``org.craftercms.plugin.exampledashboard``.

.. code-block:: text
   :caption: *Dashboard Plugin Directory Structure*

   <plugin-folder>/
     craftercms-plugin.yaml
     authoring/
       static-assets/
         plugins/
           org/
             craftercms/
               plugin/
                 exampledashboard/
                   dashboard/
                     test-dashboard/

|

Add the following ``installation`` block to your ``craftercms-plugin.yaml`` descriptor file:

.. code-block:: yaml
   :linenos:
   :caption: *craftercms-plugin.yaml*
   :emphasize-lines: 17-18

   installation:
     - type: preview-app
       parentXpath: /siteUi/widget[@id='craftercms.components.Dashboard']
       elementXpath: //plugin[@id='org.craftercms.sampleDashboardPlugin.components.reactComponent']
       element:
         name: configuration
         children:
         - name: widgets
           children:
           - name: widget
             attributes:
             - name: id
               value: org.craftercms.sampleDashboardPlugin.components.reactComponent
             children:
             - name: plugin
               attributes:
               - name: id
                 value: org.craftercms.plugin.exampledashboard
               - name: type
                 value: dashboard
               - name: name
                 value: test-dashboard
               - name: file
                 value: index.js

|

To see the plugin in action, click on the CrafterCMS logo at the top left of your browser to open the sidebar,
then click on ``Dashboard``:

.. image:: /_static/images/developer/plugins/project-plugins/dashboard-plugin-in-action.webp
   :align: center
   :alt: Dashboard plugin in action

|

You may also open the Dashboard anywhere via the Launcher, which is opened by clicking the ``apps`` icon on the
top right:

.. image:: /_static/images/developer/plugins/project-plugins/open-dashboard-from-launcher.webp
   :align: center
   :alt: Open Dashboard from the Launcher

|

Here's the auto-wired section in the configuration after installing the plugin:

.. code-block:: xml
   :linenos:
   :emphasize-lines: 14-19

   <siteUi>
     ...
     <widget id="craftercms.components.Dashboard">
       <configuration>
         <widgets>
           <widget id="craftercms.components.AwaitingApprovalDashlet">
             <permittedRoles>
               <role>admin</role>
               <role>developer</role>
               <role>publisher</role>
             </permittedRoles>
           </widget>
           ...
           <widget id="org.craftercms.sampleDashboardPlugin.components.reactComponent">
             <plugin id="org.craftercms.plugin.exampledashboard"
                     type="dashboard"
                     name="test-dashboard"
                     file="index.js"/>
           </widget>
           ...

|

.. _plugin-project-tools-example:

"""""""""""""
Project Tools
"""""""""""""
|projectTools| contains tools that project administrators use for daily activities. For more information on the
available tools in |projectTools|, see :ref:`navigating-project-tools`.

.. image:: /_static/images/developer/plugins/project-plugins/studio-project-tools.webp
   :align: center
   :alt: Studio Project Tools
   :width: 80%

|

For this example, the plugin ``type`` (``CATEGORY``) is ``project-tool``, the ``NAME`` is ``test-project-tools``,
and the ``plugin.id`` is ``org.craftercms.plugin.exampleprojecttools``.

.. code-block:: text
   :caption: *Project Tools Plugin Directory Structure*

   <plugin-folder>/
     craftercms-plugin.yaml
     authoring/
       static-assets/
         plugins/
           org/
             craftercms/
               plugin/
                 exampleprojecttools/
                   project-tool/
                     test-project-tools/

|

Add the following ``installation`` block to your ``craftercms-plugin.yaml`` descriptor file:

.. code-block:: yaml
   :linenos:
   :caption: *craftercms-plugin.yaml*
   :emphasize-lines: 29-30

   installation:
     - type: preview-app
       parentXpath: //reference[@id='craftercms.siteTools']
       elementXpath: //plugin[@id='org.craftercms.sampleProjectToolsPlugin.components.reactComponent']
       element:
         name: tools
         children:
         - name: tool
           children:
           - name: title
             attributes:
             - name: id
               value: "test.projecttool"
             - name: defaultMessage
               value: "Test Adding Project Tool"
           - name: icon
             attributes:
             - name: id
               value: "@mui/icons-material/WidgetsOutlined"
           - name: url
             value: test
           - name: widget
             attributes:
             - name: id
               value: org.craftercms.sampleProjectToolsPlugin.components.reactComponent
             children:
             - name: plugin
               attributes:
               - name: id
                 value: org.craftercms.plugin.exampleprojecttools
               - name: type
                 value: project-tool
               - name: name
                 value: test-project-tools
               - name: file
                 value: index.js

|

To see the plugin in action, click on the CrafterCMS logo at the top left of your browser to open the sidebar,
then click on ``Project Tools``:

.. image:: /_static/images/developer/plugins/project-plugins/project-tools-plugin-in-action.webp
   :align: center
   :alt: Project Tools project plugin in action

|

Here's the auto-wired section in the configuration after installing the plugin:

.. code-block:: xml
   :linenos:
   :emphasize-lines: 13-18

   <siteUi>
     ...
     <references>
       <reference id="craftercms.siteTools">
         <tools>
           ...
           <tool>
             <title id="PluginManagement.title" defaultMessage="Plugin Management"/>
             <icon id="@mui/icons-material/ExtensionOutlined"/>
             <url>plugins</url>
             <widget id="craftercms.components.PluginManagement"/>
           </tool>
           <tool>
             <title id="test.sitetool" defaultMessage="Test Adding Project Tool"/>
             <icon id="@mui/icons-material/WidgetsOutlined"/>
             <url>test</url>
             <widget id="org.craftercms.sampleProjectToolsPlugin.components.reactComponent">
                <plugin id="org.craftercms.plugin.exampleprojecttools"
                        type="project-tool"
                        name="test-project-tools"
                        file="index.js"/>
             </widget>
           </tool>
         </tools>
       ...

|

.. _plugin-navmenu-example:

"""""""""""""""
Navigation Menu
"""""""""""""""
The Navigation Menu (Launcher) contains tools that are used for daily activities by users. For more information
on the Navigation Menu, see :ref:`here <navigating-main-menu>`.

.. image:: /_static/images/developer/plugins/project-plugins/open-dashboard-from-launcher.webp
   :align: center
   :alt: Navigation Menu
   :width: 80%

|

For this example, the plugin ``type`` (``CATEGORY``) is ``navmenu``, the ``NAME`` is ``test-navmenu``, and the
``plugin.id`` is ``org.craftercms.plugin.examplenavmenu``.

.. code-block:: text
   :caption: *Navigation Menu Plugin Directory Structure*

   <plugin-folder>/
     craftercms-plugin.yaml
     authoring/
       static-assets/
         plugins/
           org/
             craftercms/
               plugin/
                 examplenavmenu/
                   navmenu/
                     test-navmenu/

|

Add the following ``installation`` block to your ``craftercms-plugin.yaml`` descriptor file:

.. code-block:: yaml
   :linenos:
   :caption: *craftercms-plugin.yaml*
   :emphasize-lines: 18-19

   installation:
     - type: preview-app
       parentXpath: //widget[@id='craftercms.components.Launcher']
       elementXpath: //plugin[@id='org.craftercms.sampleNavMenuPlugin.components.reactComponent']
       element:
         name: configuration
         children:
         - name: widgets
           children:
           - name: widget
             children:
             - name: configuration
               children:
               - name: widgets
                 children:
                 - name: widget
                   attributes:
                   - name: id
                     value: org.craftercms.sampleNavMenuPlugin.components.reactComponent
                   children:
                   - name: plugin
                     attributes:
                     - name: id
                       value: org.craftercms.plugin.examplenavmenu
                     - name: type
                       value: navmenu
                     - name: name
                       value: test-navmenu
                     - name: file
                       value: index.js

|

To see the plugin in action, click on the Navigation Menu icon on the top right:

.. image:: /_static/images/developer/plugins/project-plugins/navmenu-plugin-in-action.webp
   :align: center
   :alt: Navigation Menu project plugin in action

|

Here's the auto-wired section in the configuration after installing the plugin:

.. code-block:: xml
   :linenos:
   :emphasize-lines: 21-27

   <siteUi>
     ...
     <widget id="craftercms.components.Launcher">
       <configuration>
         <widgets>
           <widget id="craftercms.components.LauncherSection">
             <configuration>
               <title id="launcher.siteSectionTitle">
                 <defaultMessage><![CDATA[
   					Site <muted>• {siteName}</muted>]]></defaultMessage>
               </title>
               <widgets>
                 <widget id="craftercms.components.LauncherLinkTile">
                   <configuration>
                     <title id="words.dashboard" defaultMessage="Dashboard"/>
                     <systemLinkId>siteDashboardDialog</systemLinkId>
                     <icon id="@mui/icons-material/DashboardRounded"/>
                   </configuration>
                 </widget>
                 ...
                 <widget id="craftercms.components.LauncherPublishingStatusTile"/>
                 <widget id="org.craftercms.sampleNavMenuPlugin.components.reactComponent">
                   <plugin id="org.craftercms.plugin.examplenavmenu"
                           type="navmenu"
                           name="test-navmenu"
                           file="index.js"/>
                 </widget>
                 ...

|hr|

.. _plugin-translations-example:

"""""""""""""""""""""
Translations Override
"""""""""""""""""""""
This plugin example shows the user how to override the translations for the example placeholder text in the ``Author``
filter of the *Activity* widget in the Dashboard.

.. image:: /_static/images/developer/plugins/project-plugins/example-placeholder-text-in-dashboard.webp
   :align: center
   :alt: Navigation Menu
   :width: 80%

|

Here's the plugin directory structure for our example:

.. code-block:: text
   :caption: *Translation Override Plugin Directory Structure*

   <plugin-folder>/
     craftercms-plugin.yaml
     authoring/
       static-assets/
         plugins/
           org/
             craftercms/
               examples/
                 library/
                   index.js
     translations-library/
       src/
         components/
           Translations.tsx
         i18n/
           translations/
             de.json
             en.json
             es.json
             ko.json

|

Below are the sample files listed in the structure above.
For the translations files, examples for English and Korean are provided.
Simply click on the triangle next to the summary to expand/collapse the file contents.

.. raw:: html

   <details>
   <summary><a>Translation override example index.js file</a></summary>

.. code-block:: js
    :linenos:

    var w7YdZu$3 = "e.g. my-user, author@mail.com";
    var en = {
        w7YdZu: w7YdZu$3,
        "words.activity": "System Activity"
    };

    var w7YdZu$2 = "ej: mi-usuario, autor@mail.com";
    var es = {
        w7YdZu: w7YdZu$2,
        "words.activity": "Actividad del sistema"
    };

    var w7YdZu$1 = "z.B. mein-benutzer, autor@mail.com";
    var de = {
        w7YdZu: w7YdZu$1,
        "words.activity": "Systemaktivität"
    };

    var w7YdZu = "예: my-user, author@mail.com";
    var ko = {
        w7YdZu: w7YdZu,
        "words.activity": "시스템 활동"
    };

    function Translations() {
        return null;
    }

    var plugin = {
        id: 'org.craftercms.examples.componentLibrary',
        locales: {
            en: en,
            es: es,
            de: de,
            ko: ko
        },
        widgets: {
            'org.craftercms.examples.translations': Translations
        },
        scripts: [
        // Below are examples of how to load scripts into the Studio runtime
        // {
        //   src: 'https://code.jquery.com/jquery-3.5.1.min.js',
        //   integrity: 'sha256-9/aliU8dGd2tb6OSsuzixeV4y/faTqgFtohetphbbj0=',
        //   crossorigin: 'anonymous'
        // },
        // 'script.js'
        ],
        stylesheets: [
        // Examples of how to load stylesheets into the Studio runtime
        // 'index.css'
        ]
    };

    export { Translations, plugin as default };

.. raw:: html

   </details>

.. raw:: html

   <details>
   <summary><a>Translation override example Translations.tsx file</a></summary>

.. code-block:: tsx

    export function Translations() {
        return null;
    }

    export default Translations;


.. raw:: html

   </details>

.. raw:: html

   <details>
   <summary><a>Translation override example en.json file</a></summary>

.. code-block:: json

    {
        "w7YdZu": "e.g. my-user, author@mail.com"
    }


.. raw:: html

   </details>

.. raw:: html

   <details>
   <summary><a>Translation override example ko.json file</a></summary>

.. code-block:: json

    {
        "w7YdZu": "예: my-user, author@mail.com"
    }


.. raw:: html

   </details>

|

Add the following ``installation`` block to your ``craftercms-plugin.yaml`` descriptor file:

.. code-block:: yaml
   :linenos:
   :caption: *craftercms-plugin.yaml*

   installation:
     - type: preview-app
       parentXpath: //widget[@id='craftercms.components.ToolsPanel']
       elementXpath: //widget[@id='org.craftercms.examples.translations']
       element:
         name: configuration
         children:
           - name: widgets
             children:
               - name: widget
                 attributes:
                   - name: id
                     value: org.craftercms.examples.translations
                 children:
                   - name: plugin
                     attributes:
                       - name: id
                         value: org.craftercms
                       - name: type
                         value: examples
                       - name: name
                         value: library
                       - name: file
                         value: index.js

|

To see the plugin in action, open the Dashboard then click on ``Author`` in the ``Activity`` widget:

.. image:: /_static/images/developer/plugins/project-plugins/overridden-placeholder-text-in-dashboard.webp
   :align: center
   :alt: Dashboard placeholder text override plugin in action

|

Here's the auto-wired section in the configuration after installing the plugin:

.. code-block:: xml
   :linenos:
   :emphasize-lines: 14-19

   <siteUi>
     ...
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
            ...
            <widget id="org.craftercms.examples.translations">
               <plugin id="org.craftercms"
                       type="examples"
                       name="library"
                       file="index.js"/>
            </widget>

|

|hr|

-----------------------------------
Form Engine Control and Data Source
-----------------------------------
Crafter Studio allows plugins for Form Engine controls and data sources through the ``getPluginFile`` API found
:base_url:`here <_static/api/studio.html#tag/plugin/operation/getPluginFile>`.

.. image:: /_static/images/content-model/create-content-type-2.webp
   :width: 75 %
   :alt: Content Type Editor
   :align: center

|

Form Engine **controls** (#4 in the image above) are UX elements that help authors capture and edit content and
metadata properties. Form Engine **data sources** (#5) are swappable components that controls delegate to for
retrieving the content authors select. Controls should be written independently of the data they capture so they
can be reused across a wide range of data sets.

The process for creating and installing a Form Engine plugin is the same for controls and data sources:

#. Create the folder structure and ``craftercms-plugin.yaml`` descriptor file
#. Implement the JavaScript interface in ``main.js``
#. Add an ``installation`` block to auto-wire the plugin
#. Install the plugin with ``crafter-cli copy-plugin``
#. Verify the plugin in the Content Type Editor

The only differences are the JavaScript interface, directory ``CATEGORY`` (``control`` vs ``datasource``),
``installation`` block, and the section of ``site-config-tools.xml`` where the plugin is registered. Both are
covered below.

.. _building-plugins-controls:

^^^^^^^^^^^^^^^^^^^^^^^^^^
Form Engine Control Plugin
^^^^^^^^^^^^^^^^^^^^^^^^^^

"""""""""""""""""
What is a Control
"""""""""""""""""
A form control is a UX element to help authors capture and edit content and metadata properties.
Crafter Studio form controls should be written in a way that makes them independent of the data they allow the user to select so that they can be (re)used across a wide range of data sets.

Out of the box controls are:

.. include:: /includes/form-controls/list-form-controls.rst

"""""""""""""""""""""""""""""""""""""""
The anatomy of a Control Project Plugin
"""""""""""""""""""""""""""""""""""""""
Form Engine Control consist of (at a minimum)

* A single JavaScript file which implements the control interface.

    * The JS file name and the control name in the configuration does not need to be the same. The JS file name can be any meaningful name, different from the control name in the configuration.

* Configuration in a Crafter Studio project to make that control available for use

.. _control-interface:

"""""""""""""""""
Control Interface
"""""""""""""""""
.. code-block:: javascript
    :linenos:

    /**
     * Constructor: Where .X is substituted with your class name
     * ID is the variable name
     * FORM is the form object
     * OWNER is the parent section/form
     * PROPERTIES is the collection of configured property values
     * CONSTRAINTS is the collection of configured constraint values
     * READONLY is a true/false flag indicating re-only mode
     */
    CStudioForms.Controls.X = CStudioForms.Controls.X ||
    function(id, form, owner, properties, constraints, readonly)  { }

    YAHOO.extend(CStudioForms.Controls.X, CStudioForms.CStudioFormField, {

      /**
       * Return a user friendly name for the control (will show up in content type builder UX)
       */
      getLabel: function() { },

      /**
       * method is called by the engine when the value of the control is changed
       */
      _onChange: function(evt, obj) { },

      /**
       * method is called by the engine to invoke the control to render. The control is responsible for creating and managing its own HTML.
       * CONFIG is a structure containing the form definition and other control configuration
       * CONTAINER EL is the containing element the control is to render in to.
       */
      render: function(config, containerEl) { },

       /**
        * returns the current value of the control
        */
       getValue: function() { },

       /**
        * sets the value of the control
        */
       setValue: function(value) { },

       /**
        * return a string that represents the kind of control (this is the same as the file name)
        */
       getName: function() {  },

       /**
        * return a list of properties supported by the control.
        * properties is an array of objects with the following structure { label: "", name: "", type: "" }
        */
       getSupportedProperties: function() { },

       /**
        * return a list of constraints supported by the control.
        * constraints is an array of objects with the following structure { label: "", name: "", type: "" }
        */
       getSupportedConstraints: function() { }
    });

.. _plugin-directory-structure:

""""""""""""""""""""""""""""""""""""
Control Plugin Directory and Example
""""""""""""""""""""""""""""""""""""
When creating control plugins, the JS file goes in the following location:

``authoring/static-assets/plugins/{yourPluginId}/control/{yourPluginName}/JS_FILE.js``

where **{yourPluginName}** is the name of the form engine control plugin and **JS_FILE.js** is the JavaScript
file containing the control interface implementation.

Let's take a look at an example of a control plugin. We will be adding a control named ``text-input`` to
``My Editorial``.

For this example, the ``plugin.id`` is ``org.craftercms.plugin.excontrol``, the ``CATEGORY`` is ``control``,
the ``NAME`` is ``text-input``, and the JS file is ``main.js``.

.. code-block:: text
   :caption: *Form Engine Control Plugin Directory Structure*

   <plugin-folder>/
     craftercms-plugin.yaml
     authoring/
       static-assets/
         plugins/
           org/
             craftercms/
               plugin/
                 excontrol/
                   control/
                     text-input/
                       main.js

|

For our example, the ``<plugin-folder>`` is located here: ``/users/myuser/myplugins/form-control-plugin``

In the JS file, please note that the ``CStudioAuthoring.Module`` is required and that the prefix for
``CStudioAuthoring.Module.moduleLoaded`` must be the name of the control. For our example, the prefix is
``text-input`` as shown in the example.

.. code-block:: js
    :linenos:
    :emphasize-lines: 51
    :caption: *authoring/static-assets/plugins/org/craftercms/plugin/excontrol/control/text-input/main.js*

    CStudioForms.Controls.textInput = CStudioForms.Controls.textInput ||
    function(id, form, owner, properties, constraints, readonly)  {
    	this.owner = owner;
    	this.owner.registerField(this);
    	this.errors = [];
    	this.properties = properties;
    	this.constraints = constraints;
    	this.inputEl = null;
    	this.patternErrEl = null;
    	this.countEl = null;
    	this.required = false;
    	this.value = "_not-set";
    	this.form = form;
    	this.id = id;
    	this.readonly = readonly;

    	return this;
    }

    YAHOO.extend(CStudioForms.Controls.textInput, CStudioForms.CStudioFormField, {

        getLabel: function() {
            return CMgs.format(langBundle, "Text Input");
        },
        .
        .
        .

        getName: function() {
    	    	return "text-input";
        },

        getSupportedProperties: function() {
    	    return [
    		    { label: CMgs.format(langBundle, "displaySize"), name: "size", type: "int", defaultValue: "50" },
    		    { label: CMgs.format(langBundle, "maxLength"), name: "maxlength", type: "int",  defaultValue: "50" },
    		    { label: CMgs.format(langBundle, "readonly"), name: "readonly", type: "boolean" },
    		    { label: "Tokenize for Indexing", name: "tokenize", type: "boolean",  defaultValue: "false" }
    	    ];
        },

        getSupportedConstraints: function() {
    	    return [
    		    { label: CMgs.format(langBundle, "required"), name: "required", type: "boolean" },
    		    { label: CMgs.format(langBundle, "matchPattern"), name: "pattern", type: "string" },
    	    ];
        }

    });

    CStudioAuthoring.Module.moduleLoaded("text-input", CStudioForms.Controls.textInput);

|

Here's the complete example form control plugin file for the ``text-input`` control (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample form control plugin file "main.js".</a></summary>

.. literalinclude:: /_static/code/plugins/control/main.js
    :language: js
    :linenos:

.. raw:: html

   </details>

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Saving additional form control elements to XML
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To save additional elements from your form control into the XML content, call ``registerDynamicField`` from the form when initializing the form control. When ``updateField`` is called, your element will be saved into the XML content.

 .. code-block:: js

    this.form.registerDynamicField(this.timezoneId);

|

See `here <https://github.com/craftersoftware/craftercms/blob/support/4.x/studio-ui/static-assets/components/cstudio-forms/controls/date-time.js#L881>`__ for an example of calling ``registerDynamicField`` in the date-time form control code.

.. _configure-descriptor-file-for-autowiring:

"""""""""""""""""""""""""""""""""""""""""""
Auto-wiring and Installing a Control Plugin
"""""""""""""""""""""""""""""""""""""""""""
To setup our form control to be automatically wired in the corresponding configuration file in Studio (which for a form control, is the Project Config Tools Configuration file) during the installation, add the following to your ``craftercms-plugin.yaml`` descriptor file:

.. code-block:: yaml
   :linenos:
   :caption: *craftercms-plugin.yaml*

   installation:
    - type: form-control
      elementXpath: //control/plugin[pluginId='org.craftercms.plugin.excontrol']
      element:
        name: control
        children:
          - name: plugin
            children:
              - name: pluginId
                value: org.craftercms.plugin.excontrol
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

|

See :ref:`CrafterCMS Plugin Descriptor <plugin-descriptor-file>` for more information on setting up automatic wiring of your plugin in Studio.

After placing your JS file, the plugin may now be installed for testing/debugging using the ``crafter-cli`` command ``copy-plugin``.

When running a ``crafter-cli`` command, the connection to CrafterCMS needs to be setup via the :ref:`add-environment <crafter-cli-add-environment>` command. Once the connection has been established, we can now install the plugin to the project ``my-editorial`` by running the following:

   .. code-block:: bash

       ./crafter-cli copy-plugin -e local -s my-editorial --path /users/myuser/myplugins/form-control-plugin

   |

Let's take a look at the auto-wiring performed during installation of the plugin. Form controls are setup in the ``site-config-tools.xml`` file.

The items we setup in the descriptor file for auto-wiring :ref:`above <configure-descriptor-file-for-autowiring>` should now be in the ``Project Config Tools`` configuration file, which can be accessed by opening the ``Sidebar``, then clicking on |projectTools| -> ``Configuration`` -> ``Project Config Tools``

**Location (In Repository) SITENAME/config/studio/administration/site-config-tools.xml**

.. code-block:: xml
    :linenos:
    :emphasize-lines: 10-18

    <controls>
        <control>
            <name>auto-filename</name>
            .
            .
        </control>
        .
        .
        <control>
            <plugin>
                <pluginId>org.craftercms.plugin.excontrol</pluginId>
                <type>control</type>
                <name>text-input</name>
                <filename>main.js</filename>
            </plugin>
            <icon>
                <class>fa-pencil-square-o</class>
            </icon>
        </control>
    </controls>

|

Here's our plugin control added to the list of controls in content types:

.. image:: /_static/images/form-controls/control-plugin-added.webp
    :width: 50 %
    :alt: Form Engine Control Project Plugin Added to Content Type
    :align: center

|

.. _building-plugins-form-ds:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Form Engine Data Source Plugin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

"""""""""""""""""""""
What is a Data Source
"""""""""""""""""""""
Crafter Studio form controls should be written in a way that makes them independent of the data they allow the user to select so that they can be (re)used across a wide range of data sets. To accomplish this objective we use a data source pattern where by the form control widget code is concerned with rendering and facilitating the data capture/selection process but delegates the retrieval of the content to a separate swappable component interface known as a data source.

Out of the box data sources are:

.. include:: /includes/form-sources/list-form-sources.rst

"""""""""""""""""""""""""""""""""""""""""""
The anatomy of a Data Source Project Plugin
"""""""""""""""""""""""""""""""""""""""""""
Data Sources consist of (at a minimum)

* A single JavaScript file which implements the data source interface.

    * The JS file name and the data source name in the configuration does not need to be the same. The JS file name can be any meaningful name, different from the data source name in the configuration.

* Configuration in a Crafter Studio project to make that data source available for use.

.. _data-source-interface:

"""""""""""""""""""""
Data Source Interface
"""""""""""""""""""""
.. code-block:: javascript
    :linenos:

    /**
     * Constructor: Where .X is substituted with your class name
     */
    CStudioForms.Datasources.X = CStudioForms.Datasources.X ||
    function(id, form, properties, constraints)  {
    }

    /**
     * Extension of the base class
     */
    YAHOO.extend(CStudioForms.Datasources.X, CStudioForms.CStudioFormDatasource, {

    	/**
         * Return a user friendly name for the data source (will show up in content type builder UX
         */
    	getLabel: function() {  },

    	/**
    	 * return a string that represents the type of data returned by the data source
    	 * This is often of type "item"
    	 */
    	getInterface: function() { },

    	/**
    	 * return a string that represents the kind of data source (this is the same as the file name)
    	 */
        getName: function() { },

    	/**
    	 * return a list of properties supported by the data source.
    	 * properties is an array of objects with the following structure { label: "", name: "", type: "" }
    	 */
    	getSupportedProperties: function() { },

    	/**
    	 * method responsible for getting the actual values. Caller must pass callback which meets interface:
    	 * { success: function(list) {}, failure: function(exception) }
    	 */
    	getList: function(cb) { }
    });

.. _plugin-ds-directory-structure:

""""""""""""""""""""""""""""""""""""""""
Data Source Plugin Directory and Example
""""""""""""""""""""""""""""""""""""""""
When creating data source plugins, the JS file goes in the following location:

``authoring/static-assets/plugins/{yourPluginId}/datasource/{yourPluginName}/JS_FILE.js``

where **{yourPluginName}** is the name of the form engine data source plugin and **JS_FILE.js** is the
JavaScript file containing the data source interface implementation.

Let's take a look at an example of a data source plugin. We will be adding a data source named ``parent-content``.

For this example, the ``plugin.id`` is ``org.craftercms.plugin.examples``, the ``CATEGORY`` is ``datasource``,
the ``NAME`` is ``parent-content``, and the JS file is ``main.js``.

.. code-block:: text
   :caption: *Form Engine Data Source Plugin Directory Structure*

   <plugin-folder>/
     craftercms-plugin.yaml
     authoring/
       static-assets/
         plugins/
           org/
             craftercms/
               plugin/
                 examples/
                   datasource/
                     parent-content/
                       main.js

|

For our example, the ``<plugin-folder>`` is located here: ``/users/myuser/myplugins/form-datasource-plugin``

In the JS file, please note that the ``CStudioAuthoring.Module`` is required and that the prefix for
``CStudioAuthoring.Module.moduleLoaded`` must be the name of the data source. For our example, the prefix is
``parent-content`` as shown in the example.

.. code-block:: js
    :linenos:
    :emphasize-lines: 73
    :caption: *authoring/static-assets/plugins/org/craftercms/plugin/examples/datasource/parent-content/main.js*

    CStudioForms.Datasources.ParentContent= CStudioForms.Datasources.ParentContent ||
    function(id, form, properties, constraints)  {
       	this.id = id;
       	this.form = form;
       	this.properties = properties;
       	this.constraints = constraints;
    	this.selectItemsCount = -1;
    	this.type = "";
        this.defaultEnableCreateNew = true;
        this.defaultEnableBrowseExisting = true;
        this.countOptions = 0;

       	for(var i=0; i<properties.length; i++) {
       		if(properties[i].name == "repoPath") {
     			this.repoPath = properties[i].value;
       		}
       		if(properties[i].name == "browsePath") {
     			this.browsePath = properties[i].value;
       		}

    		if(properties[i].name == "type"){
    			this.type = (Array.isArray(properties[i].value))?"":properties[i].value;
    		}

            if(properties[i].name === "enableCreateNew"){
                this.enableCreateNew = properties[i].value === "true" ? true : false;
                this.defaultEnableCreateNew = false;
                properties[i].value === "true" ? this.countOptions ++ : null;
            }

            if(properties[i].name === "enableBrowseExisting"){
                this.enableBrowseExisting = properties[i].value === "true" ? true : false;
                this.defaultEnableBrowseExisting = false;
                properties[i].value === "true" ? this.countOptions ++ : null;
            }
       	}

        if(this.defaultEnableCreateNew){
            this.countOptions ++;
        }
        if(this.defaultEnableBrowseExisting){
            this.countOptions ++;
        }

    	return this;
    }

    YAHOO.extend(CStudioForms.Datasources.ParentContent, CStudioForms.CStudioFormDatasource, {
        .
        .
        .
        getName: function() {
    		return "parent-content";
    	},

    	getSupportedProperties: function() {
    		return [
                { label: CMgs.format(langBundle, "Enable Create New"), name: "enableCreateNew", type: "boolean", defaultValue: "true"  },
                { label: CMgs.format(langBundle, "Enable Browse Existing"), name: "enableBrowseExisting", type: "boolean", defaultValue: "true" },
    			{ label: CMgs.format(langBundle, "repositoryPath"), name: "repoPath", type: "string" },
    			{ label: CMgs.format(langBundle, "browsePath"), name: "browsePath", type: "string" },
    			{ label: CMgs.format(langBundle, "defaultType"), name: "type", type: "string" }
    		];
    	},

    	getSupportedConstraints: function() {
    		return [
    		];
    	}

    });

    CStudioAuthoring.Module.moduleLoaded("parent-content", CStudioForms.Datasources.ParentContent);

|

Here's the complete example form data source plugin file for the ``parent-content`` data source (click on the triangle on the left to expand/collapse):

.. raw:: html

   <details>
   <summary><a>Sample form data source plugin file "main.js".</a></summary>

.. literalinclude:: /_static/code/plugins/datasource/main.js
   :language: js
   :linenos:

.. raw:: html

   </details>

|

.. _configure-descriptor-file-for-autowiring-datasource:

"""""""""""""""""""""""""""""""""""""""""""""""
Auto-wiring and Installing a Data Source Plugin
"""""""""""""""""""""""""""""""""""""""""""""""
To setup our form data source to be automatically wired in the corresponding configuration file in Studio (which for a form data source, is the Project Config Tools Configuration file) during the installation, add the following to your ``craftercms-plugin.yaml`` descriptor file:

.. code-block:: yaml
   :linenos:
   :caption: *craftercms-plugin.yaml*

   installation:
    - type: form-datasource
      elementXpath: //datasource/plugin[pluginId='org.craftercms.plugin.examples']
      element:
        name: datasource
        children:
          - name: plugin
            children:
              - name: pluginId
                value: org.craftercms.plugin.examples
              - name: type
                value: datasource
              - name: name
                value: parent-content
              - name: filename
                value: main.js
          - name: icon
            children:
              - name: class
                value: fa-pencil-square-o

|

See :ref:`CrafterCMS Plugin Descriptor <plugin-descriptor-file>` for more information on setting up automatic wiring of your plugin in Studio.

After placing your JS file, the plugin may now be installed for testing/debugging using the ``crafter-cli`` command ``copy-plugin``.

When running a ``crafter-cli`` command, the connection to CrafterCMS needs to be setup via the :ref:`add-environment <crafter-cli-add-environment>` command. Once the connection has been established, we can now install the plugin to the project ``my-editorial`` by running the following:

   .. code-block:: bash

       ./crafter-cli copy-plugin -e local -s my-editorial --path /users/myuser/myplugins/form-datasource-plugin

   |

Let's take a look at the auto-wiring performed during installation of the plugin. Form data sources are setup in the ``site-config-tools.xml`` file.

The items we setup in the descriptor file for auto-wiring :ref:`above <configure-descriptor-file-for-autowiring-datasource>` should now be in the ``Project Config Tools`` configuration file, which can be accessed by opening the ``Sidebar``, then clicking on |projectTools| -> ``Configuration`` -> ``Project Config Tools``

**Location (In Repository) SITENAME/config/studio/administration/site-config-tools.xml**

.. code-block:: xml
    :linenos:
    :emphasize-lines: 10,11,12,13,14,15

    <datasources>
        <datasource>
            <name>img-desktop-upload</name>
            .
            .
        </datasource>
        .
        .
        <datasource>
            <plugin>
                <pluginId>org.craftercms.plugin.examples</pluginId>
                <type>datasource</type>
                <name>parent-content</name>
                <filename>main.js</filename>
            </plugin>
            <icon>
                <class>fa-users</class>
            </icon>
        </datasource>
    </datasources>

|

Here's our plugin data source added to the list of data sources in content types:

.. image:: /_static/images/form-sources/datasource-plugin-added.webp
    :width: 50 %
    :alt: Form Engine Data Source Project Plugin Added to Content Type
    :align: center

