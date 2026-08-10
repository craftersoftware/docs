.. list-table::
   :align: left
   :header-rows: 1
   :widths: 25 45 30

   * - Name
     - Description
     - Type
   * - siteItemService
     - Allows access to the site content.
     - |SiteItemService|
   * - urlTransformationService
     - Service for transforming URLs, like transforming the content URL of a page to the web or render URL.
     - |UrlTransformationService|
   * - applicationContext
     - Provides access to the Crafter Engine's Spring beans and site beans defined in config/spring/application-context.xml.
     - |ApplicationContextAccessor|
   * - globalProperties
     - Provides access to global configuration properties defined in server-config.properties.
     - `PropertySourcesPropertyResolver`_
   * - navBreadcrumbBuilder
     - Helper class that returns the list of path components in an URL, to create navigation breadcrumbs.
     - |BreadcrumbBuilder|
   * - navTreeBuilder
     - Helper class that creates navigation trees to facilitate rendering.
     - |NavTreeBuilder|
   * - tenantsResolver
     - Can be used to retrieve the Profile tenants associated to the current site.
     - |TenantsResolver|
   * - modePreview
     - Can be used to check whether Engine is being executed in preview mode (also the value of the ``crafter.engine.preview`` property) e.g.

       .. code-block:: html
           :force:

           <#if modePreview>
               <!-- Preview-only markup or logic -->
           </#if>

     - Boolean
   * - crafterEnv
     - Indicates the value of the ``crafter.engine.environment`` property
     - String
   * - siteConfig
     - The current site Configuration loaded from /config/site.xml.
     - |XMLConfiguration|
   * - siteContext
     - The current SiteContext
     - |SiteContextHashModel|
   * - request
     - The current request
     - |HttpRequestHashModel|
   * - requestParameters
     - The parameter values for the current request
     - `HttpRequestParametersHashModel`_
   * - cookies
     - The cookie values for the current request
     - |Map|
   * - session
     - The current session
     - `HttpSessionHashModel`_
   * - locale
     - The current locale for the current user
     - |Locale|
   * - authToken
     - The current authentication (if the user has logged in), created by Spring Security
     - |SpringAuthentication|

The following variables are provided for backward compatibility when using Crafter Profile, should be replaced
with ``authToken`` if possible:

.. list-table::
   :align: left
   :header-rows: 1
   :widths: 25 45 30

   * - Name
     - Description
     - Type
   * - authentication
     - The current authentication (if the user has logged in), created by the Crafter Security Provider
     - |Authentication|
   * - profile
     - The current profile (if the user has logged in), created by the Crafter Security Provider
     - |Profile|

.. note::
    The variables ``profile`` and ``authentication`` listed  above will be null in most cases and should not be used anymore


The following variables are restricted by default, to use them see :ref:`configure-custom-services`

.. list-table::
   :align: left
   :header-rows: 1
   :widths: 25 45 30

   * - Name
     - Description
     - Type
   * - application
     - The servlet context
     - |ServletContextHashModel|
   * - siteContext
     - The current SiteContext
     - |SiteContext|

.. |SiteItemService| replace:: :javadoc_base_url:`SiteItemService <engine/org/craftercms/engine/service/SiteItemService.html>`
.. |UrlTransformationService| replace:: :javadoc_base_url:`UrlTransformationService <engine/org/craftercms/engine/service/UrlTransformationService.html>`
.. |ApplicationContextAccessor| replace:: :javadoc_base_url:`ApplicationContextAccessor <engine/org/craftercms/engine/util/spring/ApplicationContextAccessor.html>`
.. _PropertySourcesPropertyResolver: https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/core/env/PropertySourcesPropertyResolver.html
.. |BreadcrumbBuilder| replace:: :javadoc_base_url:`BreadcrumbBuilder <engine/org/craftercms/engine/navigation/NavBreadcrumbBuilder.html>`
.. |NavTreeBuilder| replace:: :javadoc_base_url:`NavTreeBuilder <engine/org/craftercms/engine/navigation/NavTreeBuilder.html>`
.. |TenantsResolver| replace:: :javadoc_base_url:`TenantsResolver <profile/org/craftercms/security/utils/tenant/TenantsResolver.html>`
.. |ProfileService| replace:: :javadoc_base_url:`ProfileService <profile/org/craftercms/profile/api/services/ProfileService.html>`
.. |TenantService| replace:: :javadoc_base_url:`TenantService <profile/org/craftercms/profile/api/services/TenantService.html>`
.. |AuthenticationService| replace:: :javadoc_base_url:`AuthenticationService <profile/org/craftercms/profile/api/services/AuthenticationService.html>`
.. |AuthenticationManager| replace:: :javadoc_base_url:`AuthenticationManager <profile/org/craftercms/security/authentication/AuthenticationManager.html>`
.. |XMLConfiguration| replace:: See ``XMLConfiguration`` under ``org.apache.commons.configuration2`` in the `Apache Commons <https://commons.apache.org/proper/commons-configuration/index.html>`__ apidocs
.. |SiteContext| replace:: :javadoc_base_url:`SiteContext <engine/org/craftercms/engine/service/context/SiteContext.html>`
.. |ServletContextHashModel| replace:: :javadoc_base_url:`ServletContextHashModel <engine/org/craftercms/engine/freemarker/ServletContextHashModel.html>`
.. |SiteContextHashModel| replace:: :javadoc_base_url:`ServletContextHashModel <engine/org/craftercms/engine/util/freemarker/SiteContextHashModel.html>`
.. |HttpRequestHashModel| replace:: :javadoc_base_url:`HttpRequestHashModel <engine/org/craftercms/engine/util/freemarker/HttpRequestHashModel.html>`
.. _HttpRequestParametersHashModel: http://freemarker.org/docs/api/freemarker/ext/servlet/HttpRequestParametersHashModel.html
.. _HttpSessionHashModel: http://freemarker.org/docs/api/freemarker/ext/servlet/HttpSessionHashModel.html
.. |Map| replace:: See ``Map`` under the ``java.util`` package of the ``java.base`` module in the `Java documentation <https://docs.oracle.com/en/java/javase/index.html>`__
.. |Locale| replace:: See ``Locale`` under the ``java.util`` package of the ``java.base`` module in the `Java documentation <https://docs.oracle.com/en/java/javase/index.html>`__
.. |Authentication| replace:: :javadoc_base_url:`Authentication <profile/org/craftercms/security/authentication/Authentication.html>`
.. |Profile| replace:: :javadoc_base_url:`Profile <profile/org/craftercms/profile/api/Profile.html>`
.. |SpringAuthentication| replace::  See ``Authentication`` under ``org.springframework.security.core`` in the `Spring Security <https://docs.spring.io/spring-security/reference/index.html>`__ apidocs
