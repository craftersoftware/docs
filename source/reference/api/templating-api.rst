:is-up-to-date: True
:last-update: 4.6.0

.. meta::
   :description: CrafterCMS FreeMarker templating API reference — available variables, macros, and rendering helpers for building page and component views.
   :keywords: CrafterCMS, FreeMarker, templating API, Crafter Engine, contentModel, page rendering

.. _templating-api:

===========================
FreeMarker (Templating) API
===========================
Each page and component content type in CrafterCMS generally has it's own view. Crafter Engine, which is in charge
of rendering this views, uses Freemarker as the template engine to generate the HTML returned to the client.
Documentation for Freemarker can be found at http://freemarker.org.

The most important variable available in the page and component Freemarker templates is the ``contentModel``. Through
the ``contentModel`` you have access to the content itself. So if in the page you defined a variable name ``title``,
you can get the value of title with ``${contentModel.title}``. Or if you defined a repeater named ``links``, and under
it ``url`` fields, then accessing the url links is just a matter of iterating the list using
``<#list contentModel.links.url as url>``. The type of the ``contentModel`` is :javadoc_base_url:`SiteItem <engine/org/craftercms/engine/model/SiteItem.html>`, so you can also call the methods of that class if
the nested property access described previously is not enough, or if you need to access other properties like the
``storeUrl``.

Crafter Engine also populates templates with other useful variables, described below.

.. include:: /includes/freemarker-variables.rst

For a walkthrough of querying content in scripts and listing the results in FreeMarker (search and Site Item Service), see :ref:`content-queries-groovy-freemarker`.

.. _renderComponent:

--------------------
Rendering Components
--------------------
Crafter Engine provides the ``renderComponent`` macro that can be used to render components in any template:

**Render a Component by Referencing the Path**

.. code-block:: html
  :force:
  :caption: Using the RenderComponent Macro to Render a component by referencing the path

  <!-- Render a component by referencing the path -->
  <@renderComponent componentPath='/site/components/headers/header.xml' />

|

**Render a Component by Referencing a Shared Component**

.. code-block:: html
  :force:
  :caption: Using the RenderComponent Macro to Render a component by referencing a shared component

  <!-- Render a component by referencing a shared component from the current item -->
  <@renderComponent component=contentModel.mySharedComponent_o.item  />

|

**Render a Component by Referencing an Embedded Component**

.. code-block:: html
  :force:
  :caption: Using the RenderComponent Macro to Render a component by referencing an embedded component

  <!-- Render a component by referencing an embedded component from the current item -->
  <@renderComponent component=contentModel.myEmbeddedComponent_o.item />

|

**Render an Embedded Component from Another Component**

.. code-block:: html
  :force:
  :caption: Using the RenderComponent Macro to Render an Embedded Component from Another Component

  <!-- Render an embedded component from another component instead of the current item -->
  <#assign sharedItem = siteItemService.getSiteItem('/site/components/global.xml')/>
  <@renderComponent parent=sharedItem component=sharedItem.myEmbeddedComponent_o.item />

|

**Render a Component Passing Additional Variables (parameters)**

.. code-block:: html
  :force:
  :caption: Using the RenderComponent Macro Render a Component Passing Additional Variables

  <!-- Render a component passing additional variables -->
  <!-- Inside the component's template 'theme' will be available as a global variable -->
  <@renderComponent component=contentModel.myComponent.item additionalModel={ 'theme': contentModel.theme_s } />

|

where:

* **componentPath**: a path for a shared component in the site
* **component**: a XML node from a node selector field, supports both shared and embedded components
* **parent**: the SiteItem to use as a parent in the case of embedded components (defaults to the current item
  being rendered)
* **additionalModel**: a Freemarker hash containing key-value pairs that can be used as global variables in the
  component's template

.. _templating-rendering-navigation:

--------------------
Rendering Navigation
--------------------
Crafter Engine provides the option of rendering automatically the navigation for you, just by using the macro
``navigation``

See :ref:`navigation` for more information on the macro

.. _templating-rendering-breadcrumbs:

---------------------
Rendering Breadcrumbs
---------------------
Crafter also offers a ``renderBreadcrumb`` macro to easily generate a dynamic list with all the
parent pages of a specific url.

See :ref:`breadcrumb` for more information on the macro

..
  Todo: Review the section below if still applicable

..
  ~~~~~~~~~~~~~~~~~~~
  Using Custom Macros
  ~~~~~~~~~~~~~~~~~~~

  Both ``renderNavigation`` and ``renderBreadcrumb`` use other macros to render the items.
  In the case of navigation each type of item has it's own macro:

  *   ``renderRootItem``: for the top level item.
  *   ``renderNavItem``: for items with no sub-items.
  *   ``renderNavItemWithSubItems``: for items with sub-items.
  *   ``renderNavSubItem``: for sub-items with no other sub-items.
  *   ``renderNavSubItemWithSubItems``: for sub-items with other sub-items.

  For breadcrumb there is only one type of item:

  *   ``renderBreadcrumbItem``

  Engine includes a default implementation that can be found in ``templates/web/navigation2/nav-macros.ftl``
  and ``templates/web/navigation2/breadcrumb-macros.ftl`` but it also provides the option to use your
  own sets of macros, which you'll probably want since the navigation HTML is generally specific to the site.

  .. code-block:: html
    :force:
    :linenos:
    :caption: Default Nav Macros

    <#macro renderNavItem navItem>
       <li <#if navItem.active>class="active"</#if>><a href="${navItem.url}">${navItem.label}</a></li>
    </#macro>

    <#macro renderRootItem navItem>
       <@renderNavItem navItem/>
    </#macro>

    <#macro renderNavItemWithSubItems navItem>
       <li <#if navItem.active>class="dropdown active"<#else>class="dropdown"</#if>>
           <a class="dropdown-toggle" data-toggle="dropdown" href="${navItem.url}">${navItem.label}</a>
           <ul class="dropdown-menu">
               <#nested>
           </ul>
       </li>
    </#macro>

    <#macro renderNavSubItem navItem>
       <@renderNavItem navItem/>
    </#macro>

    <#macro renderNavSubItemWithSubItems navItem>
       <li class="dropdown-submenu">
           <a href="${navItem.url}">${navItem.label}</a>
           <ul class="dropdown-menu">
               <#nested>
           </ul>
       </li>
    </#macro>

  .. code-block:: html
    :force:
    :linenos:
    :caption: Default Breadcrumb Macros

    <#macro renderBreadcrumbItem item >
       <#if item.active>
           <li class="active">${item.label}</li>
       <#else>
           <li><a href="${item.url}">${item.label}</a></li>
       </#if>
    </#macro>

  You can define your own macros in an additional Freemarker template and include and optional
  parameter with the namespace that was given in the ``import`` tag:

  .. code-block:: html
    :force:
    :linenos:
    :caption: Using Custom Navigation Macros

    <#import "/templates/web/navigation2/navigation.ftl" as nav>
    <#import "/templates/web/navigation2/breadcrumb.ftl" as breadcrumb>
    <#import "/templates/web/components/custom-nav-macros.ftl" as customNav>

    <@nav.renderNavigation "/site/website", 1, false, customNav />
  
    <@breadcrumb.renderBreadcrumb contentModel.storeUrl, "/site/website/articles", customNav />

  .. note::
    If you use custom macros for navigation, you need to define each one with the same name as shown
    in the list and include all parameters according to the default implementation provided by
    Engine.

  In case the structure provided by the macros is not suitable for your need you can also use the
  beans to get the navigation tree or breadcrumb list in a Groovy script:

  .. code-block:: groovy
    :linenos:
    :caption: Navigation Tree from Groovy

    def rootPath = "/site/website"
    templateModel.navTree = navTreeBuilder.getNavTree(rootPath, 2, contentModel.storeUrl)
    templateModel.breadcrumb = navBreadcrumb.getBreadcrumb(contentModel.storeUrl, rootPath)

  .. note::
    These beans are the same used internally by the builtin macros, they will return |NavItem| objects.

  .. |NavItem| replace:: :javadoc_base_url:`NavItem<engine/org/craftercms/engine/navigation/NavItem.html>`



---------------------------
Running Scripts/Controllers
---------------------------
Crafter Engine allows executing scripts/controllers from inside Freemarker templates by using the tag ``@controller``. It requires a single parameter, ``path``, which is the path of the script/controller in the site:

.. code-block:: html
   :force:
   :caption: Running Scripts/Controllers from inside Freemarker templates

   <@controller path=“/scripts/plugins/MyPlugin/1/get-tweets.groovy” />
   <@controller path=“/scripts/plugins/MyPlugin/1/get-fbs.groovy” />

-------------------------
Escaping HTML and Scripts
-------------------------
To escape HTML and scripts and prevent their execution in your template, simply wrap the HTML/script to be
escaped with the ``outputformat`` Freemarker directive set to ``html``:

.. code-block::
    :caption: *Escaping HTML and Scripts in Templates*

    <#outputformat "HTML">
    ...
    </#outputformat>

------------------------
Keeping Templates Simple
------------------------
You can access all Crafter Engine services directly from the Freemarker templates as described in
the previous sections, however it is a good practice to keep logic and complex operations outside
of the templates to simplify debugging and maintenance. An easy way to extract logic from the
templates is using Groovy's closures:

.. code-block:: groovy
  :caption: Page or Component Script
  :linenos:

  // Add a closure to the templateModel map, in this case it will find if a page has a custom
  // nav icon defined in the xml descriptor.
  
  templateModel.getNavIcon = { item ->
    def storeUrl = urlTransformationService.transform("renderUrlToStoreUrl", item.url)
    def siteItem = siteItemService.getSiteItem(storeUrl)
    if(siteItem) {
      def navIcon = siteItem.navIcon?.text
      if(navIcon) {
        return navIcon
      }
    }
    return "fa-file-o"
  }

.. code-block:: html
  :caption: Page or Component Template
  :linenos:

  <!-- Now the closure is available to use in the template, you only have to use the same name
       from the script and use the `call` method. -->
       
  <i class="fa ${getNavIcon.call(navItem)}">

If you have a large amount of closures or operations that need to be shared between multiple 
templates then you should consider creating a Groovy class instead:

.. code-block:: groovy
  :caption: Custom Service Class
  :linenos:

  // Define a class with all the logic
  
  package org.site.service
  
  class NavIconService {
  
    def urlTransformationService
    def siteItemService
    def defaultIcon
  
    def getNavItem(item) {
      ...
      return this.defaultIcon
    }
  
  }

.. code-block:: xml
  :caption: *Engine Site Application Context file - application-context.xml*
  :linenos:

  <!-- Add a bean definition using the new class -->
  
  <bean id="navIconService" class="org.site.service.NavIconService">
    <property name="urlTransformationService" ref="crafter.urlTransformationService"/>
    <property name="siteItemService" ref="crafter.siteItemService"/>
    <property name="defaultIcon" value="${nav.defaultIcon}"/>
  </bean>

.. code-block:: xml
  :caption: *Engine Site Configuration file - site-config.xml*
  :linenos:

  <!-- If needed the bean can use external configuration for easy management -->
  
  <?xml version="1.0" encoding="UTF-8"?>
  <site>
    ...
    <nav>
      <defaultIcon>fa-file-o</defaultIcon>
    </nav>
    ...
  </site>

.. code-block:: html
  :caption: Template
  :linenos:
  
  <!-- Now the bean can be use just like Crafter built-in services -->
  
  <i class="fa ${navIconService.getNavIcon(navItem)}">

.. note::

  All beans defined in the :ref:`Engine Site Application Context <engine-project-spring-configuration>`
  file will be available in templates.

  For more information on Crafter Engine Site configuration files, see :ref:`engine-site-configuration-files`