:is-up-to-date: True
:last-updated: 4.6.0

.. index:: Content Queries, Groovy, FreeMarker, searchClient, Site Item Service, templateModel

.. meta::
   :description: Query content in Groovy page and component controllers and render the results in FreeMarker templates, using the Website Editorial blueprint as a working example.
   :keywords: Groovy, FreeMarker, content query, searchClient, OpenSearch, SiteItemService, templateModel, CrafterCMS, Crafter Engine, templating

.. _content-queries-groovy-freemarker:

==================================================
Content Queries in Groovy and FreeMarker Templates
==================================================
This article shows you how to do content queries in Groovy and use the results in FreeMarker.
We will walk through a templated project using the out-of-the-box blueprint Website Editorial, where groovy scripts in page and component controllers, queries content, and puts the results on the ``templateModel`` variable, which is used for rendering the HTML.

There are two common ways to query content from Groovy:

* **Search index** (``searchClient``) — filter and sort by content type, fields, dates, and targeting. Use this for listings, related content, and search-driven pages.
* **Repository / XML** (``siteItemService``) — load a specific item or walk the site tree, then read fields with XPath. Use this for navigation, taxonomies, and a known path.

For the Search API itself, see :ref:`content-search`. For controller variables, see :ref:`page-and-component-controllers`. For template variables, see :ref:`templating-api`.

---------------------------
How Groovy Reaches the View
---------------------------
A page or component controller (Groovy script) does not return HTML. It populates ``templateModel``. Any property you set there is a FreeMarker variable with the same name.

+-------------------+----------------------------------------------------------------------------------+
|| Variable         || Role                                                                            |
+===================+==================================================================================+
|| ``contentModel`` || The current page or component XML (a SiteItem). Read fields the author edited.  |
+-------------------+----------------------------------------------------------------------------------+
|| ``templateModel``|| The map passed to FreeMarker. Set ``templateModel.articles = ...`` then use     |
||                  || ``${articles}`` / ``<#list articles as article>`` in the template.              |
+-------------------+----------------------------------------------------------------------------------+

Bind a script in one of two ways (see :ref:`page-and-component-controllers`):

#. **By content type name.** For content type ``/page/home``, place the script at ``scripts/pages/home.groovy``.
#. **By item selector.** Add a ``scripts`` (or ``scripts_o``) item selector on the content type and pick Groovy files under ``scripts/pages`` or ``scripts/components``.

Engine also injects ``searchClient``, ``siteItemService``, and ``urlTransformationService`` into scripts. Convert a content store path (for example ``/site/website/articles/.../index.xml``) to a public URL with:

.. code-block:: groovy

    urlTransformationService.transform("storeUrlToRenderUrl", doc.localId)

Indexed field names follow the content-type suffixes authors see in Studio (``subject_t``, ``image_s``, ``date_dt``, ``featured_b``, ``categories_o.item.key``, and so on).

----------------------------------------------------
Example: Featured Articles on the Home Page (Search)
----------------------------------------------------
The home page content type is bound to ``scripts/pages/home.groovy``. The script searches for featured articles and stores the list on the template model.

.. code-block:: groovy
    :linenos:
    :caption: scripts/pages/home.groovy

    import org.craftercms.sites.editorial.SearchHelper
    import org.craftercms.sites.editorial.ProfileUtils

    def segment = ProfileUtils.getSegment(profile, siteItemService)
    def searchHelper = new SearchHelper(searchClient, urlTransformationService)
    def articles = searchHelper.searchArticles(true, null, segment)

    templateModel.articles = articles

``searchArticles(true, ...)`` limits the query to items whose ``featured_b`` field is true. ``segment`` is optional targeting; it can be ``null``.

The home template iterates ``articles``. Each map has ``url``, ``title``, ``summary``, and ``image`` — values the helper copied from the search hit. ``$model=article`` tells Experience Builder which content item the markup belongs to.

.. code-block:: html
    :force:
    :linenos:
    :caption: templates/web/pages/home.ftl (featured articles section)

    <#list articles as article>
        <@crafter.article $model=article>
            <a href="${article.url}" class="image">
                <@crafter.img
                    $model=article
                    $field="image_s"
                    src=article.image???then(article.image, "/static-assets/images/placeholder.png")
                    alt=""
                />
            </a>
            <h3>
                <@crafter.a $model=article $field="subject_t" href="${article.url}">
                    ${article.title}
                </@crafter.a>
            </h3>
            <@crafter.p $model=article $field="summary_t">
                ${article.summary}
            </@crafter.p>
            <ul class="actions">
                <li><a href="${article.url}" class="button">More</a></li>
            </ul>
        </@crafter.article>
    </#list>

The same listing pattern is used on category landing pages: ``scripts/pages/category-landing.groovy`` reads ``contentModel.category_s`` and ``contentModel.max_articles_i``, then calls ``searchHelper.searchArticles(false, category, segment, 0, maxArticles)``.

-------------------------------------------
The Search Query (What SearchHelper Builds)
-------------------------------------------
You do not have to use a helper class. Any Groovy controller can call ``searchClient`` directly. The editorial ``SearchHelper.searchArticles`` method is equivalent to a bool query that:

* Filters ``content-type`` to ``/page/article``
* Optionally filters featured, categories, segments, and extra query-string criteria
* Sorts by ``date_dt`` descending
* Maps each hit into a simple map for FreeMarker

A self-contained version of that query in a page or component script:

.. code-block:: groovy
    :linenos:
    :caption: Inline search in a Groovy controller

    import org.opensearch.client.opensearch._types.SortOrder
    import org.opensearch.client.opensearch.core.SearchRequest

    def request = SearchRequest.of(r -> r
        .query(q -> q
            .bool(b -> b
                .filter(f -> f
                    .match(m -> m
                        .field("content-type")
                        .query(v -> v.stringValue("/page/article"))
                    )
                )
                .filter(f -> f
                    .term(t -> t
                        .field("featured_b")
                        .value(v -> v.booleanValue(true))
                    )
                )
            )
        )
        .from(0)
        .size(10)
        .sort(s -> s
            .field(f -> f
                .field("date_dt")
                .order(SortOrder.Desc)
            )
        )
    )

    def result = searchClient.search(request, Map)
    def articles = []

    result.hits().hits()*.source().each { doc ->
        articles << [
            id      : doc.objectId,
            path    : doc.localId,
            title   : doc.subject_t,
            summary : doc.summary_t,
            image   : doc.image_s,
            url     : urlTransformationService.transform("storeUrlToRenderUrl", doc.localId)
        ]
    }

    templateModel.articles = articles

Put reusable query logic under ``scripts/classes`` (package path must match the folder path). The editorial helper lives at ``scripts/classes/org/craftercms/sites/editorial/SearchHelper.groovy``.

For Query DSL vs builder APIs, aggregations, and type-ahead, see :ref:`content-search`.

--------------------------------------------------------
Example: Latest and Related Articles (Component Scripts)
--------------------------------------------------------
The left rail uses an **Articles Widget** component (``/component/articles-widget``) whose display template is ``templates/web/components/articles-widget.ftl``. Authors attach a controller with the **Controllers** (``scripts_o``) item selector.

Here is the latest articles component XML (an **Articles Widget** component):

.. code-block:: xml
    :caption: site/components/articles-widget/latest-articles-widget.xml (scripts)

    <scripts_o item-list="true">
        <item>
            <key>/scripts/components/latest-articles.groovy</key>
            <value>latest-articles.groovy</value>
        </item>
    </scripts_o>

The latest-articles script searches for the three newest articles (not limited to featured) and again sets ``templateModel.articles``:

.. code-block:: groovy
    :linenos:
    :caption: scripts/components/latest-articles.groovy

    import org.craftercms.sites.editorial.SearchHelper
    import org.craftercms.sites.editorial.ProfileUtils

    def segment = null

    if (authToken) {
        segment = ProfileUtils.getSegment(authToken.principal, siteItemService)
    }

    def searchHelper = new SearchHelper(searchClient, urlTransformationService)
    def articles = searchHelper.searchArticles(false, null, segment, 0, 3)

    templateModel.articles = articles

The widget template is shared by latest and related widgets. It only cares that ``articles`` exists:

.. code-block:: html
    :force:
    :linenos:
    :caption: templates/web/components/articles-widget.ftl

    <#import "/templates/system/common/crafter.ftl" as crafter />

    <#if articles?? && articles?size &gt; 0>
        <@crafter.section>
            <header class="major">
                <@crafter.h2 $field="title_t">${contentModel.title_t}</@crafter.h2>
            </header>
            <div class="mini-posts">
                <#list articles as article>
                    <@crafter.article $model=article>
                        <a href="${article.url}" class="image">
                            <img src="${article.image!"/static-assets/images/placeholder.png"}" alt=""/>
                        </a>
                        <h4>
                            <@crafter.a href="${article.url}" $model=article $field="title_t">
                                ${article.title}
                            </@crafter.a>
                        </h4>
                    </@crafter.article>
                </#list>
            </div>
        </@crafter.section>
    </#if>

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Passing Extra Parameters into a Script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Related articles need the **current** article’s categories and path so the query can match the same categories and exclude the page being viewed. The article template builds an ``additionalModel`` map and passes it into ``renderComponent``. Those keys become Groovy variables in the component script (``articleCategories``, ``articlePath``).

.. code-block:: html
    :force:
    :linenos:
    :caption: templates/web/pages/article.ftl (before the left rail)

    <#assign articleCategories = contentModel.queryValues("//categories_o/item/key")/>
    <#assign articlePath = contentModel.storeUrl />
    <#assign additionalModel = {"articleCategories": articleCategories, "articlePath": articlePath }/>

    <@renderComponent component = contentModel.left_rail_o.item additionalModel = additionalModel />

.. code-block:: groovy
    :linenos:
    :caption: scripts/components/related-articles.groovy

    import org.craftercms.sites.editorial.SearchHelper
    import org.craftercms.sites.editorial.ProfileUtils

    def segment = null

    if (authToken) {
        segment = ProfileUtils.getSegment(authToken.principal, siteItemService)
    }

    def searchHelper = new SearchHelper(searchClient, urlTransformationService)
    def articles = searchHelper.searchArticles(false, articleCategories, segment, 0, 3, "-localId:\"${articlePath}\"")

    templateModel.articles = articles

The last argument is extra OpenSearch query-string criteria: exclude the current article’s ``localId``.

-----------------------------------------------
Example: Load a Taxonomy with Site Item Service
-----------------------------------------------
Search is not required when you already know the path. The search-results page controller loads the categories taxonomy selected on the page and exposes the items to FreeMarker for the refine-by checkboxes.

.. code-block:: groovy
    :linenos:
    :caption: scripts/pages/search-results.groovy

    def categoriesItem = siteItemService.getSiteItem(contentModel.categories_o.item.key.text)
    templateModel.categories = categoriesItem.items.item

.. code-block:: html
    :force:
    :linenos:
    :caption: templates/web/pages/search-results.ftl (category filters)

    <#list categories as category>
        <div class="3u 6u(medium) 12u$(small)">
            <input type="checkbox" id="${category.key}" name="${category.key}" value="${category.key}">
            <label for="${category.key}">${category.value}</label>
        </div>
    </#list>

``getSiteItem`` returns a SiteItem. Nested XML becomes properties you can walk in Groovy or FreeMarker (``items.item``, ``key``, ``value``). You can also run XPath against the current or loaded item:

.. code-block:: groovy

    def title = siteItemService.getSiteItem("/site/website/index.xml").queryValue("internal-name")

``queryValue`` / ``queryValues`` work in FreeMarker on ``contentModel`` as well, as shown in the article template above.

For tree-based queries (for example building navigation from ``/site/website``), use ``siteItemService.getSiteTree(...)``. Examples are in :ref:`groovy-examples`.

-----------------
When to Use Which
-----------------
* Use **``searchClient``** when the set of items is defined by fields (type, category, date, featured, full text) rather than a fixed folder listing.
* Use **``siteItemService``** when you have a store path, need the full XML (including values that are not indexed the way you need), or are walking the repository tree.
* Prefer putting query logic in **Groovy**, not FreeMarker. Templates should loop and print; scripts should filter, sort, and map fields.
* Always map search hits to a small structure (title, url, image, …) before the view. That keeps templates independent of index field names.
* Guard empty results in FreeMarker with ``<#if articles?? && articles?size gt 0>``.

See also :ref:`targeting` for how the editorial blueprint combines these queries with profile segments.
