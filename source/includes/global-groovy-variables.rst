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
   * - |UrlTransform|
     - Service for transforming URLs, like transforming the content URL of a page to the web or render URL.
     - |UrlTransformationService|
   * - search
     - Service that can be used to execute search queries against OpenSearch.
     - |OpenSearchWrapper|
   * - applicationContext
     - Provides access to the Crafter Engine's Spring beans and site beans defined in config/spring/application-context.xml
     - |ApplicationContextAccessor|
   * - globalProperties
     - Provides access to global configuration properties defined in server-config.properties.
     - |PropertySources|_
   * - navBreadcrumbBuilder
     - Helper class that returns the list of path components in an URL, to create navigation breadcrumbs.
     - |BreadcrumbBuilder|
   * - navTreeBuilder
     - Helper class that creates navigation trees to facilitate rendering
     - |NavTreeBuilder|
   * - tenantsResolver
     - Can be used to retrieve the Profile tenants associated to the current site.
     - |TenantsResolver|
   * - profileService
     - Provides access to the Crafter Profile API for profiles.
     - |ProfileService|
   * - tenantService
     - Provides access to the Crafter Profile API for tenants.
     - |TenantService|
   * - authenticationService
     - Provides access to the Crafter Profile API for authentication.
     - |AuthenticationService|
   * - authenticationManager
     - Manages Crafter Security Provider based authentications.
     - |AuthenticationManager|
   * - textEncryptor
     - Utility class for encrypting/decrypting text with AES.
     - |TextEncryptor|
   * - modePreview
     - Can be used to check whether Engine is being executed in preview mode (also the value of the ``crafter.engine.preview`` property)
     - Boolean
   * - crafterEnv
     - Indicates the value of the ``crafter.engine.environment`` property
     - String
   * - logger
     - The GroovyUtils SLF4J logger
     - `Logger`_
   * - siteConfig
     - The current site Configuration, loaded from /config/site.xml.
     - |XMLConfiguration|
   * - siteContext
     - The current SiteContext
     - |SiteContext|

.. |SiteItemService| replace:: :javadoc_base_url:`SiteItemService <engine/org/craftercms/engine/service/SiteItemService.html>`
.. |UrlTransform| replace:: urlTransformationService
.. |PropertySources| replace:: PropertySourcesPropertyResolver
.. _PropertySources: https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/core/env/PropertySourcesPropertyResolver.html
.. |UrlTransformationService| replace:: :javadoc_base_url:`UrlTransformationService <engine/org/craftercms/engine/service/UrlTransformationService.html>`
.. |OpenSearchWrapper| replace:: :javadoc_base_url:`OpenSearchWrapper <search/org/craftercms/search/opensearch/OpenSearchWrapper.html>`
.. |ApplicationContextAccessor| replace:: :javadoc_base_url:`ApplicationContextAccessor <engine/org/craftercms/engine/util/spring/ApplicationContextAccessor.html>`
.. |BreadcrumbBuilder| replace:: :javadoc_base_url:`BreadcrumbBuilder <engine/org/craftercms/engine/navigation/NavBreadcrumbBuilder.html>`
.. |NavTreeBuilder| replace:: :javadoc_base_url:`NavTreeBuilder <engine/org/craftercms/engine/navigation/NavTreeBuilder.html>`
.. |TenantsResolver| replace:: :javadoc_base_url:`TenantsResolver <profile/org/craftercms/security/utils/tenant/TenantsResolver.html>`
.. |ProfileService| replace:: :javadoc_base_url:`ProfileService <profile/org/craftercms/profile/api/services/ProfileService.html>`
.. |TenantService| replace:: :javadoc_base_url:`TenantService <profile/org/craftercms/profile/api/services/TenantService.html>`
.. |AuthenticationService| replace:: :javadoc_base_url:`AuthenticationService <profile/org/craftercms/profile/api/services/AuthenticationService.html>`
.. |AuthenticationManager| replace:: :javadoc_base_url:`AuthenticationManager <profile/org/craftercms/security/authentication/AuthenticationManager.html>`
.. _Logger: http://www.slf4j.org/api/org/slf4j/Logger.html
.. |XMLConfiguration| replace:: See ``XMLConfiguration`` under ``org.apache.commons.configuration2`` in the `Apache Commons <https://commons.apache.org/proper/commons-configuration/index.html>`__ apidocs
.. |SiteContext| replace:: :javadoc_base_url:`SiteContext <engine/org/craftercms/engine/service/context/SiteContext.html>`
.. |TextEncryptor| replace:: See ``TextEncryptor`` under ``org.springframework.security.crypto.encrypt`` in the `Spring Security <https://docs.spring.io/spring-security/reference/index.html>`__ apidocs
