:is-up-to-date: True
:last-updated: 5.0.0

.. meta::
   :description: Guide to upgrading CrafterCMS search from Elasticsearch to OpenSearch, including the upgrade-search.sh script and reindexing steps.
   :keywords: OpenSearch upgrade, Elasticsearch migration, CrafterCMS search, upgrade-search.sh, search reindex, CrafterCMS 4.1, search engine upgrade

.. _upgrading-search:

================
Upgrading Search
================
Starting version 4.1.0, CrafterCMS now uses OpenSearch.
This section describes how to upgrade search for CrafterCMS installed on a server.

Please read through the upgrade instructions :ref:`here <upgrading-craftercms-on-a-server>` first.
The steps for upgrading to OpenSearch follows almost exactly the same steps as listed in that document.

.. important::
    Remember to **manually shut down and backup CrafterCMS** before beginning your upgrades!

|hr|

------------------------------
Upgrading 4.0.x (from ES 7.15)
------------------------------
When upgrading from 4.0.x (running ES 7) the indices are not compatible at all, so the content needs to be reprocessed
and indices rebuilt completely. The rebuilding of the indices is handled by the ``post-upgrade.sh`` script.

To upgrade your 4.0.x installation, we'll be running the upgrade scripts from a new binary archive.
Here are the steps:

#. Download the CrafterCMS version you'd like to upgrade to, and extract the files.
#. Upgrade using the ``upgrade-target.sh`` script from your newly extracted files:

   .. code-block:: bash
       :caption: *Run the upgrade-target script*

       ./upgrade-target.sh /path/of/install/to/be/upgraded

#. Run the ``post-upgrade.sh`` script. You'll need to configure the installation root directory to use Java version 21
   before running the script. This will:

   - Remove old *data/indexes-es* directory (old indexes are not usable by OpenSearch)
   - Start CrafterCMS and ask for signal to continue
   - Once started and CrafterCMS is up (including UM execution), let the post-upgrade continue by typing ``Y``:

     .. code-block:: bash

         Please make sure Crafter has started successfully before continuing
         > Continue? [(Y)es/(N)o]:

   - Post-upgrade will continue to trigger the reindex of all targets by calling the Deployer API ``/api/1/target/deploy-all``

#. Monitor the Deployer logs and wait for the reindex to be completed. You should see a message like the following:

   .. code-block:: text

       2024-04-20 14:36:46.050  INFO 376430 --- [deployment-1] org.craftercms.deployer.impl.TargetImpl  : Deployment for editorial110-authoring finished in 9.953 secs

|hr|

---------------
Upgrading 4.1.x
---------------
There are no extra steps required for upgrading your 4.1.x install, simply follow the instructions
:ref:`here <upgrading-craftercms-on-a-server>`.

|hr|

-------------------------
Manual Updates for Search
-------------------------
The Upgrade Manager (UM) performs most of the updates required to upgrade your project to OpenSearch, such as the import updates in your classes.  There are some instances where manual updates may need to be performed like below:

^^^^^^^^^^^^^^^^^^^^^
Updating Search Beans
^^^^^^^^^^^^^^^^^^^^^
If you have an application context that injects Elasticsearch like below, it will need to be updated to inject OpenSearch:

.. code-block:: xml
    :caption: *Application context that injects Elasticsearch*
    :emphasize-lines: 8

    <beans xmlns="http://www.springframework.org/schema/beans"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                 xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean id="demoProfileService" class="com.demo.services.ProfileService" />

        <bean id="demoSearchService" class="com.demo.services.SearchService">
            <property name="elasticsearch" ref="crafter.elasticsearchService" />
            <property name="urlTransformationService" ref="crafter.urlTransformationService" />
        </bean>
    </beans>

|

To update to OpenSearch, in the example above, the property is called ``elasticsearch`` and will need to be renamed.  In the example below, the property has been renamed to ``searchClient``:

.. code-block:: xml
    :caption: *Application context injection updated to OpenSearch*
    :emphasize-lines: 8

    <beans xmlns="http://www.springframework.org/schema/beans"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                 xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean id="demoProfileService" class="com.demo.services.ProfileService" />

        <bean id="demoSearchService" class="com.demo.services.SearchService">
            <property name="searchClient" ref="crafter.searchClient" />
            <property name="urlTransformationService" ref="crafter.urlTransformationService" />
        </bean>
    </beans>

Here's a sample Groovy script that executes a search query in a project created using the Website Editorial blueprint:

.. raw:: html

   <details>
   <summary><a>Sample search query Groovy script.</a></summary>

.. literalinclude:: /_static/code/developer/SearchService.groovy
    :language: groovy
    :linenos:

.. raw:: html

   </details>

|

^^^^^^^^^^^^^^^^^^^^^^^^^^
Search Methods/Groovy Code
^^^^^^^^^^^^^^^^^^^^^^^^^^
You might encounter the following error in your project, which indicates your search methods in your groovy code needs to be updated for OpenSearch:

.. code-block:: text
    :caption: *Error message in logs indicating groovy code needs to be updated*

    Caused by: org.craftercms.engine.exception.ScriptException: No signature of method: org.craftercms.engine.search.SiteAwareOpenSearchClient.search() is applicable for
        argument types: (org.opensearch.action.search.SearchRequest) values: [SearchRequest{searchType=QUERY_THEN_FETCH, indices=[],
        indicesOptions=IndicesOptions[ignore_unavailable=false, allow_no_indices=true, expand_wildcards_open=true, expand_wildcards_closed=false, expand_wildcards_hidden=false,
        allow_aliases_to_multiple_indices=true, forbid_closed_indices=true, ignore_aliases=false, ignore_throttled=true], routing='null', preference='null', requestCache=null,
        scroll=null, maxConcurrentShardRequests=0, batchedReduceSize=512, preFilterShardSize=null, allowPartialSearchResults=null, localClusterAlias=null,
        getOrCreateAbsoluteStartMillis=-1, ccsMinimizeRoundtrips=true, source={"from":0,"size":6,"query":{"query_string":{"query":"content-type:\"/page/blogpost\" AND ( (NOT
        (_exists_:unlisted_b)) OR unlisted_b:false) ","fields":[],"type":"best_fields","default_operator":"or","max_determinized_states":10000,"enable_position_increments":true,
        "fuzziness":"AUTO","fuzzy_prefix_length":0,"fuzzy_max_expansions":50,"phrase_slop":0,"escape":false,"auto_generate_synonyms_phrase_query":true,"fuzzy_transpositions":true,
        "boost":1.0}},"sort":[{"publishedDate_dt":{"order":"desc"}}]}, cancelAfterTimeInterval=null, pipeline=null}]
    Possible solutions: search(org.opensearch.client.opensearch.core.SearchRequest, java.lang.Class, java.util.Map), each(groovy.lang.Closure), macro(groovy.lang.Closure)




