:is-up-to-date: True
:last-updated: 4.1.6

.. highlight:: groovy
   :linenothreshold: 5

.. index:: Groovy, Groovy API, Java API, Custom Services, Services, Controllers, Unit Testing

.. meta::
   :description: CrafterCMS Groovy/Java API reference — build REST services, MVC controllers, scheduled jobs, and filters using server-side Groovy scripting.
   :keywords: CrafterCMS, Groovy API, Java API, server-side scripting, REST services, controllers, scripting

.. _groovy-java-api:

===============
Groovy/Java API
===============
CrafterCMS supports server-side development with Groovy. By using Groovy, you can create RESTful services, MVC controllers,
code that runs before a page or component is rendered, servlet filters, scheduled jobs, and entire backend applications.

----------
Groovy API
----------
^^^^^^^^^^^^^^^^
Global Variables
^^^^^^^^^^^^^^^^
CrafterCMS provides a number of useful global variables that can be used in all the different types of scripts available:

.. include:: /includes/global-groovy-variables.rst

.. _groovy-examples:

""""""""
Examples
""""""""
The following examples shows you how to use the global variables in your scripts. For more information on available
interfaces for your Groovy scripts, see the :ref:`CrafterCMS JavaDocs <java-api>`.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Make a Query for Content Based on Structure
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The following code examples use the Site Item Service in Crafter Engine to get content.
You can find the interface for this service here at :javadoc_base_url:`Site Item Service JavaDoc <engine/org/craftercms/engine/service/SiteItemService.html>`

.. code-block:: groovy

    def topNavItems = [:]
    def siteDir = siteItemService.getSiteTree("/site/website", 2)

    if (siteDir) {
        def dirs = siteDir.childItems
        dirs.each { dir ->
            def dirName = dir.getStoreName()
            def dirItem = siteItemService.getSiteItem("/site/website/${dirName}/index.xml")
            if (dirItem != null) {
                def dirDisplayName = dirItem.queryValue('internal-name')
                topNavItems.put(dirName, dirDisplayName)
            }
        }
    }

    return topNavItems

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Make a Query for Content Based on Structure with Filter
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The following code examples use the Site Item Service in Crafter Engine to get content.
In the example we build on the Site Item Service of getting objects under a specific tree in the repository by supplying
a filter that will be applied to each object first to determine if it should be part of the result.
Filters can make their determination based on the path or the content or even "outside" influence.

    * You can find the interface for this service here at :javadoc_base_url:`Site Item Service JavaDoc <engine/org/craftercms/engine/service/SiteItemService.html>`
    * Note in the example below we define our own filter based on the ItemFilter interface found at :javadoc_base_url:`ItemFilter JavaDoc <core/org/craftercms/core/service/ItemFilter.html>`
    * However, you may use out of the box filters as well if they meet your needs. These are found here at the :javadoc_base_url:`Filter Package Summary <engine/org/craftercms/engine/service/filter/package-summary.html>`
    * Finally be aware that for simple filename patterns, methods for this already exist in the Site Item Service and no filter is required (but they make for an simple to understand example.)

.. code-block:: groovy

    import org.craftercms.core.service.ItemFilter
    import org.craftercms.core.service.Item
    import java.util.List


    def result = [:]
    def navItems = [:]
    def siteDir = siteItemService.getSiteTree("/site/website", 2, new StartsWithAItemFilter(), null)

    if (siteDir) {
        def dirs = siteDir.childItems
        dirs.each { dir ->
                def dirName = dir.getStoreName()
                def dirItem = siteItemService.getSiteItem("/site/website/${dirName}/index.xml")
                if (dirItem != null) {
                    def dirDisplayName = dirItem.queryValue('internal-name')
                       navItems.put(dirName, dirDisplayName)
                }
       }
    }
    result.navItems = navItems

    return result

    /**
     * Define a filter that returns only items that have a name that starts with "A" or "a"
     */
    class StartsWithAItemFilter implements ItemFilter {

        public boolean runBeforeProcessing() {
            return true
        }

        public boolean runAfterProcessing() {
            return false
        }

        public boolean accepts(Item item, List acceptedItems, List rejectedItems, boolean runBeforeProcessing) {

          if (item.getName().toLowerCase().startsWith("a")) {
              return true
          }

          return false
        }
     }

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Make a Query Against Fields in a Content Object
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The following code examples use the Site Item Service in Crafter Engine to get content.
You can find the interface for this service at :javadoc_base_url:`Site Item Service JavaDoc <engine/org/craftercms/engine/service/SiteItemService.html>`

.. code-block:: groovy

    def result = [:]
    def segment = "a segment value" // could come from profile, query param etc

    // load a specific content object
    def itemDom = siteItemService.getSiteItem("/site/components/sliders/default.xml")

    // query specific values from the object
    result.header = itemDom.queryValue("/component/targetedSlide//segment[contains(.,'" +  segment + "')]../label")
    result.image = itemDom.queryValue("/component/targetedSlide//segment[contains(.,'" +  segment + "')]/../image")

    return result

~~~~~~~~~~~~~~~~~~
Check Preview Mode
~~~~~~~~~~~~~~~~~~
The following code example checks if Engine is running in preview mode:

.. code-block:: groovy

    if (modePreview) {
        // Preview-only logic
    }

^^^^^^^^^^^^^^^
Other Variables
^^^^^^^^^^^^^^^
There are also several other variables available to scripts that are executed during the scope of a request (REST scripts, controller
scripts, page/component scripts and filter scripts):

.. include:: /includes/request-groovy-variables.rst

All scripts are executed in a sandbox to prevent insecure code from running, to change the configuration see
:ref:`groovy-sandbox-configuration`

To create unit tests for your groovy code, see :ref:`unit-testing-groovy-code`

|hr|

.. _java-api:

--------
Java API
--------
CrafterCMS provides the following Java libraries for managing your projects:

.. list-table::
   :widths: 30 70

   * - Crafter Commons
     - :javadoc_base_url:`commons/index.html`
   * - Crafter Core
     - :javadoc_base_url:`core/index.html`
   * - Crafter Deployer
     - :javadoc_base_url:`deployer/index.html`
   * - Crafter Engine
     - :javadoc_base_url:`engine/index.html`
   * - Crafter Profile
     - :javadoc_base_url:`profile/index.html`
   * - Crafter Search
     - :javadoc_base_url:`search/index.html`
   * - Crafter Social
     - :javadoc_base_url:`social/index.html`
   * - Crafter Studio
     - :javadoc_base_url:`studio/index.html`

The Groovy examples listed :ref:`above <groovy-examples>` uses interfaces from the Java libraries in the table.

|hr|

----------------
Types of Scripts
----------------
There are different types of scripts you can create, depending on the subfolder under Scripts where they're placed. The following are
the ones currently supported:

^^^^^^^^^^^^
REST Scripts
^^^^^^^^^^^^
REST scripts function just like RESTful services. They just need to return the object to serialize
back to the caller. REST scripts must be placed in any folder under Scripts > rest.

A REST script URL has the following format: it starts with ``/api/1/services``, then contains all the folders that are part of the hierarchy
for the particular script, and ends with the script name, the HTTP method and the ``.groovy`` extension. So, a script file at
``Scripts > rest > myfolder > myscript.get.groovy`` will respond to GET method calls at ``http://mysite/api/1/services/myfolder/myscript.json``.

The following is a very simple sample script that returns a date attribute saved in the session. If no attribute is set yet, the current
date is set as the attribute. Assume that the REST script exists under Scripts > rest > session_date.get.groovy
::

    import java.util.Date

    if (!session) {
    	session = request.getSession(true)
    }

    def date = session.getAttribute("date")
    if (!date) {
    	date = new Date()

    	session.setAttribute("date", date)
    }

    return ["date": date]

.. _groovy-path-variables:

""""""""""""""""""""
Using Path Variables
""""""""""""""""""""
Path variables allows you to pass a value as part of the URL. It's a part of the path of the document
to be accessed during the API call. In a REST controller the system can automatically pick out those portions of
the URL and feed them to you with the name you supplied.

Use of path variables is done by allowing you to have a folder in ``{NAME}`` format under ``scripts/rest/``
and these folders become templates for parameterized URLs. Values added in these ``/`` locations are added to the
params map with the key of the name inside the ``{..}``. The ``pathVars`` variable is available for accessing the
value passed in the API call.

To specify path variables for a REST API, simply name the folder as the variables, for example, for the following API
call ``http://mysite/api/1/services//bar/{model}/{make}/{year}/foo``, to create the path variables ``{model}``,
``{make}`` and ``{year}``, your folder structure under ``/scripts/rest`` should look something like this:
``/bar/{model}/{make}/{year}/foo``. To access the values passed to the path variables, use the ``pathVars`` variable
e.g. to access the value passed to ``{make}`` in the API call, use ``pathVars.make``, for ``{model}`` use
``pathVars.model`` and for ``{year}`` use ``pathVars.year}``.

Let's take a look at an example of creating a REST API ``http://mysite/api/1/services/foo/{version}/helloworld.json``.

First, we'll set up the folder structure for the API. Under ``scripts`` > ``rest``, add a folder named ``foo``, then
under the ``foo`` folder, add a folder named ``{version}`` which will be the path variable.
Next we'll add the script. Under  ``scripts`` > ``rest`` > ``foo`` > ``{version}``, create the script ``helloworld.get.groovy``

.. code-block:: groovy
    :caption: *scripts/rest/foo/{version}/helloworld.get.groovy*

    def version = pathVars.version
    return "Hello world version ${version}!"


Here's how the folders and files should look like for our example:

.. code-block:: text
    :caption: *Folder structure for REST API*

    scripts/
      rest/
        foo/
          {version}/
            helloworld.json

When we make a call to ``http://mysite/api/1/services/foo/1/helloworld.json``, the output will be:

.. code-block:: text

    "Hello world version 1!"

Similarly, a call to ``http://mysite/api/1/services/foo/2/helloworld.json`` will output:

.. code-block:: text

    "Hello world version 2!"

.. _groovy-rest-script-not-found:

"""""""""""""""""""""
Custom HTTP Responses
"""""""""""""""""""""
Rest scripts will return the ``404`` page when a script is not found.
Developers will still be able to return custom ``404`` responses from rest scripts. e.g.:

.. code-block:: groovy

    response.setStatus(404)
    return 'This is the custom message'

If desired, they could even conditionally send the default response page as well by using ``sendError`` instead:

.. code-block:: groovy

    response.sendError(404)

^^^^^^^^^^^^^^^^^^
Controller Scripts
^^^^^^^^^^^^^^^^^^
Controller scripts are very similar to REST scripts. They have the same variables available, but instead of returning an object,
they return a string with the view to render. Most of the time, this is just the template path, like in the following snippet:

::

    return "/templates/web/registration.ftl"

Controller scripts basically work like a controller in the MVC pattern. They should be put under Scripts > controllers,
and similarly to REST scripts, the URL is made up of the directory hierarchy, the script name and the HTTP method. For example,
a script at Scripts > controllers > myfolder > mycontroller.get.groovy will respond to GET calls at ``http://mysite/myfolder/mycontroller``.

The following is a very simple example script that will do the sum of 2 parameters, put the result in the ``templateModel`` and return
the path of the FTL template that will render the result:

::

    templateModel.result = Integer.parseInt(params.num1) + Integer.parseInt(params.num2)

    return "/templates/web/sum.ftl"

One very common controller script is the sitemap.groovy. A sitemap is used by search engines to have a better idea of how to "crawl"
a website. A sitemap is an XML with references to most of the site's pages, and basically looks like this:

.. code-block:: xml
    :linenos:

    <?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
        <url>
            <loc>http://www.domain.com /</loc>
            <lastmod>2008-01-01</lastmod>
            <changefreq>weekly</changefreq>
            <priority>0.8</priority>
        </url>
        <url>
            <loc>http://www.domain.com/catalog?item=vacation_hawaii</loc>
            <changefreq>weekly</changefreq>
        </url>
        <url>
            <loc>http://www.domain.com/catalog?item=vacation_new_zealand</loc>
            <lastmod>2008-12-23</lastmod>
            <changefreq>weekly</changefreq>
        </url>
        <url>
            <loc>http://www.domain.com/catalog?item=vacation_newfoundland</loc>
            <lastmod>2008-12-23T18:00:15+00:00</lastmod>
            <priority>0.3</priority>
        </url>
        <url>
            <loc>http://www.domain.com/catalog?item=vacation_usa</loc>
            <lastmod>2008-11-23</lastmod>
        </url>
    </urlset>

Search engines look for the sitemap just after the domain, so a sitemap URL would look like www.domain.com/sitemap. The sitemap
controller then must be placed in Scripts > controllers > sitemap.groovy. The code would be similar to this:

::

    import groovy.xml.MarkupBuilder
    import groovy.xml.MarkupBuilderHelper

    def sitemap = []
    def excludeContentTypes = ['/component/level-descriptor']

    parseSiteItem = { siteItem ->
        if (siteItem.isFolder()) {
            def children = siteItem.childItems;
            children.each { child ->
                parseSiteItem(child);
            }
        } else {
            def contentType = siteItem.queryValue('content-type')
            if (!excludeContentTypes.contains(contentType)) {
                def storeUrl = siteItem.getStoreUrl();
                def location = urlTransformationService.transform('storeUrlToFullRenderUrl', storeUrl);
                sitemap.add(location);
            }
        }
    }

    def siteTree = siteItemService.getSiteTree("/site/website", -1)
    if (siteTree) {
        def items = siteTree.childItems;
        items.each { siteItem ->
            parseSiteItem(siteItem);
        }
    }

    response.setContentType("application/xml;charset=UTF-8")

    def writer = response.getWriter()
    def xml = new MarkupBuilder(writer)
    def xmlHelper = new MarkupBuilderHelper(xml)

    xmlHelper.xmlDeclaration(version:"1.0", encoding:"UTF-8")

    xml.urlset(xmlns:"http://www.sitemaps.org/schemas/sitemap/0.9") {
        sitemap.each { location ->
            url {
                loc(location)
                changefreq("daily")
            }
        }
    }

    response.flushBuffer()

    return null

|hr|

.. _unit-testing-groovy-code:

-----------------------------------
Unit Testing CrafterCMS Groovy Code
-----------------------------------
For larger sites with complex services implemented in Groovy, it is very helpful to have a way to include unit tests in a way that can be easily integrated with CI/CD systems.

This section details how to create unit tests for CrafterCMS Groovy code with Gradle.

For more information on the classes of the variables that can be mocked for unit testing, see :ref:`above <groovy-java-api>`

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Steps for Creating Groovy Unit Test
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To create a unit test:

#. Designate a folder for all test related files
#. Write your unit test code
#. Setup your unit test to run with Gradle
#. Execute your unit test

"""""""""""""""""""""""""""""""""""""""""""
Designate Folder for All Test Related Files
"""""""""""""""""""""""""""""""""""""""""""
Designate a folder in the site repository to contain all test related files. For example:

   .. code-block:: text
      :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox*

      /scripts
         /test
           /classes   (all packages & groovy classes for testing)
           /resources (all additional files required for testing)

   |


This structure is the equivalent of the standard folders used for Java/Groovy projects:

   .. code-block:: text

      /src/test/groovy
      /src/test/resources

   |

"""""""""""""""""""""""""
Write Your Unit Test Code
"""""""""""""""""""""""""
There are no restrictions or requirements for the unit test code, developers can choose any testing framework supported by the build tool. Examples: `spring-test <http://docs.spring.io/spring-batch/reference/html/testing.html>`__, `junit <http://junit.org/>`__, `testng <https://testng.org/>`__, `spock <https://spockframework.org/>`__

Remember when writing unit test code, developers will be responsible for:

- Choosing & configuring the testing framework
- Making sure all required dependencies are included (for example external jars)
- Mocking all Crafter Engine classes used by the classes under testing


"""""""""""""""""""""""""""""""""""""""
Setup Your Unit Test to Run With Gradle
"""""""""""""""""""""""""""""""""""""""
To use Gradle the only requirement is to add a ``build.gradle`` in the root folder of the site repository and execute the ``test`` task. Example:

.. code-block:: groovy
   :force:
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/build.gradle*
   :linenos:

   // Enable Gradle’s Groovy plugin
   plugins {
     id 'groovy'
   }

   sourceSets {
     // Add the site Groovy classes that will be tested
     main {
       groovy {
         srcDir 'scripts/classes'
       }
     }

     // Add the Groovy classes & resources to perform the tests
     test {
       groovy {
         srcDir 'scripts/test/classes'
       }
       resources {
         srcDir 'scripts/test/resources'
       }
     }
   }

   // Enable the testing framework of choice
   test {
     useJUnit()
   }

   repositories {
     mavenCentral()
   }

   // Include the required dependencies
   dependencies {
     // This dependency is required for two reasons:
     // 1. Make Engine’s classes available for compilation & testing
     // 2. Include the Groovy dependencies required by Gradle
     implementation 'org.craftercms:crafter-engine:4.1.1:classes'

     // Include the chosen testing dependencies
     testImplementation 'junit:junit:4.13.2'
     testImplementation 'org.mockito:mockito-core:4.11.0'
   }

|

""""""""""""""""""""""
Execute Your Unit Test
""""""""""""""""""""""
Given the previous example the tests can be executed using a single command:

   .. code-block:: bash

      gradle test

   |

^^^^^^^
Example
^^^^^^^
Let's take a look at an example of a groovy unit test in a site created using the empty blueprint with a custom groovy script, ``MyService``

.. image:: /_static/images/developer/unit-test/unit-test-groovy-sample-service.webp
    :alt: Unit Testing Groovy - Sample Service
    :width: 35 %
    :align: center

|

.. literalinclude:: /_static/code/developer/groovy-unit-test/MyService.groovy
   :language: groovy
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/scripts/classes/org/company/site/api/MyService.groovy*
   :linenos:

|

.. literalinclude:: /_static/code/developer/groovy-unit-test/MySearchService.groovy
   :language: groovy
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/scripts/classes/org/company/site/api/MySearchService.groovy*
   :linenos:

|

.. literalinclude:: /_static/code/developer/groovy-unit-test/ExternalApi.groovy
   :language: groovy
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/scripts/classes/org/company/site/api/ExternalApi.groovy*
   :linenos:

|

.. literalinclude:: /_static/code/developer/groovy-unit-test/MyServiceImpl.groovy
   :language: groovy
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/scripts/classes/org/company/site/impl/MyServiceImpl.groovy*
   :linenos:

|

Let's begin creating our unit test for ``MyService``

"""""""""""""""""""""""""""""""""""""""""""
Designate Folder for All Test Related Files
"""""""""""""""""""""""""""""""""""""""""""
The first thing we need to do is to designate a folder for all test related files. We'll designate the ``/scripts/test`` folder to be used for all test related files.

"""""""""""""""""""""""""
Write Your Unit Test Code
"""""""""""""""""""""""""
Next, we'll write the unit test code.

.. image:: /_static/images/developer/unit-test/unit-test-groovy-sample-unit-test.webp
   :alt: Unit Testing Groovy - Sample Service
   :width: 35 %
   :align: center

|

.. literalinclude:: /_static/code/developer/groovy-unit-test/MyServiceImplTest.groovy
   :language: groovy
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/scripts/test/classes/org/company/impl/MyServiceImplTest.groovy*
   :linenos:

|

"""""""""""""""""""""""""""""""""""""""
Setup Your Unit Test to Run With Gradle
"""""""""""""""""""""""""""""""""""""""
We'll now setup our unit test to run with Gradle, by adding a ``build.gradle`` file in the root folder of the site repository and execute the ``test`` task.

.. literalinclude:: /_static/code/developer/groovy-unit-test/build.gradle
   :language: groovy
   :force:
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/build.gradle*
   :linenos:

""""""""""""""""""""""
Execute Your Unit Test
""""""""""""""""""""""
Finally, we can run our unit test by running ``gradle test``

   .. code-block:: bash
      :caption: *Output when running unit test*

      $ gradle test

      BUILD SUCCESSFUL in 4s
      3 actionable tasks: 3 up-to-date

   |

Let's take a look at the result of our unit test which can be found here: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/build/reports/tests/test/index.html*

.. image:: /_static/images/developer/unit-test/unit-test-build-result.webp
   :alt: Unit Testing Groovy - Unit test  build report
   :width: 60 %
   :align: center

|hr|

------------
Other Topics
------------
.. _groovy-studio-content-write:

^^^^^^^^^^^^^^^^^^^^
Studio Content Write
^^^^^^^^^^^^^^^^^^^^
.. version_tag::
    :label: Since
    :version: 4.1.6

To write content from an input stream and notify subscribers (including the preview indexing) about the ``ContentEvent``
in Studio only, use the method ``writeContentAndNotify``:

.. code-block:: java

    boolean writeContentAndNotify(String site, String path, InputStream content) throws ServiceLayerException;

This method can be used from any Groovy script in Studio, this includes for example REST scripts in Studio plugins and
content type controllers. Remember to get the ``contentService`` bean when using the method like below:

.. code-block:: groovy
    :emphasize-lines: 2-3
    :caption: *Example retrieving contentService bean and using the writeContentAndNotify method*

    def documentStream = ContentUtils.convertDocumentToStream(document, "UTF-8")
    def contentService = applicationContext["cstudioContentService"]
    contentService.writeContentAndNotify(site, path, documentStream)

|hr|

--------
See Also
--------
- :ref:`configure-custom-services`