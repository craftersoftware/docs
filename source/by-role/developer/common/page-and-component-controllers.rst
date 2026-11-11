:is-up-to-date: True
:last-updated: 4.6.0

.. index:: Page Controllers, Component Controllers

.. meta::
   :description: Build Groovy page and component controllers in CrafterCMS to populate template models and execute logic before rendering in Crafter Engine.
   :keywords: page controllers, component controllers, Groovy, CrafterCMS, Crafter Engine, templateModel, contentModel, scripts

.. _page-and-component-controllers:

------------------------------
Page and Component Controllers
------------------------------
.. |SiteItem| replace:: :javadoc_base_url:`SiteItem <engine/org/craftercms/engine/model/SiteItem.html>`
.. |AllHttpScopesAndAppContextHashModel| replace:: :javadoc_base_url:`AllHttpScopesAndAppContextHashModel <engine/org/craftercms/engine/view/freemarker/AllHttpScopesAndAppContextHashModel.html>`

Crafter pages and components can have their own controller scripts too, that are executed before the page or component
is rendered, and that can contribute to the model of the template. These scripts, besides the common variables, have
the ``templateModel`` and the ``contentModel`` available.

+-------------------------+-----------------------------------------------------------------------+
|| Model Related Variable || Description                                                          |
+=========================+=======================================================================+
|| ``contentModel``       || The XML descriptor content                                           |
||                        || It is an instance of the |SiteItem| class                            |
+-------------------------+-----------------------------------------------------------------------+
|| ``templateModel``      || The actual map model of the template                                 |
||                        || It is an instance of the |AllHttpScopesAndAppContextHashModel| class |
+-------------------------+-----------------------------------------------------------------------+

The ``templateModel`` is the actual map model of the
template, and any variable put in it will be accessible directly in the template, eg. if the script has the line
``templateModel.var = 5``, then in the template the var's value can be printed with ``${var}``. The ``contentModel``
is the XML descriptor content, of type SiteItem. The scripts don't have to return any result, just populate the
``templateModel``.

There are 2 ways in which you can "bind" a script to a page or component:

#. Put the script under ``Scripts`` > ``pages`` or ``Scripts`` > ``components``, and name it after the page or component content type.
#. When creating the content type for the page or component, add an Item Selector with the variable name ``scripts``. Later when creating
   a page or component of that type, you can select multiple scripts that will be associated to the page or component.

The following is an example of a component script. The component content type is ``/component/upcoming-events``. We can then place the
script in ``Scripts`` > ``components`` > ``upcoming-events.groovy`` so that it is executed for all components of that type.

.. code-block:: groovy
    :linenos:

    import org.craftercms.engine.service.context.SiteContext
    import utils.DateUtils
    import org.opensearch.client.opensearch.core.SearchRequest
    import org.craftercms.search.opensearch.client.OpenSearchClientWrapper
    import org.opensearch.client.opensearch._types.SortOrder

    def now = DateUtils.formatDateAsIso(new Date())
    def q = "crafterSite:\"${siteContext.siteName}\" AND content-type:\"/component/event\" AND disabled:\"false\" AND date_dt:[${now} TO *]"
    def start = 0
    def rows = 1000
    def events = []

    // Execute the query
    def result = searchClient.search(r -> r
      .query(q -> q
        .queryString(s -> s
          .query(q as String)
        )
      )
      .source(s -> s
        .filter(f -> f
          .includes('localId')
        )
      )
      .from(start)
      .size(rows)
      .sort(s -> s
        .field(f -> f
          .field(date_dt)
          .order(SortOrder.Asc)
        )
      )
    , Map)

    result.hits().hits().each {
      def event = [:]
      def item = siteItemService.getSiteItem(it.source())

      event.image = item.image.text
      event.title = item.title_s.text
      event.date = DateUtils.parseModelValue(item.date_dt.text)
      event.summary = item.summary_html.text

      events.add(event)
    }

    templateModel.events = events

|

You might notice that we're importing a ``utils.DateUtils`` class. This class is not part of CrafterCMS,
but instead it is a Groovy class specific to the project. To be able to use this class, you should place
it under ``scripts > classes`` and name it DateUtils.groovy,
where everything after the groovy directory is part of the class' package. It's recommended for all
Groovy classes to follow this convention.

.. code-block:: groovy
    :linenos:

    package utils

    import java.text.SimpleDateFormat

    class DateUtils {

      static def parseModelValue(value){
        def dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss")
        dateFormat.setTimeZone(TimeZone.getTimeZone("UTC"))
        return dateFormat.parse(value)
      }

      static def formatDateAsIso(date) {
        def dateFormat = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'")
        dateFormat.setTimeZone(TimeZone.getTimeZone("UTC"))
        return dateFormat.format(date)
      }
    }

|

For a walkthrough of querying content in these scripts and listing the results in FreeMarker (search and
Site Item Service), see :ref:`content-queries-groovy-freemarker`.

For more information on the FreeMarker (Templating) APIs, please see :ref:`templating-api`.

For more information on the Groovy APIs, please see :ref:`groovy-java-api`

  .. include:: /includes/scripts-templates-security.rst
