:is-up-to-date: True
:last-update: 4.1.0

.. index:: REST, REST API

.. meta::
   :description: CrafterCMS REST Content Retrieval API reference — access published content from Crafter Engine via HTTP endpoints in any application.
   :keywords: CrafterCMS, REST API, content retrieval, Crafter Engine, HTTP API, headless CMS

.. _rest-content-retrieval-api:

===========================
REST Content Retrieval APIs
===========================
Content can be accessed via the REST Content Retrieval APIs. To view the REST Content Retrieval APIs:

.. open_iframe_modal_button::
   :label: Open here
   :url: ../../_static/api/engine.html
   :title: REST Content Retrieval APIs

.. raw:: html

    or <a href="../../_static/api/engine.html"  target="_blank">in a new tab</a>

|
|

.. note::
    Make sure that the request includes the ``crafterSite`` parameter to set a project value. The content
    retrieval API's are project specific, and so, it needs to know the project for each request made.

    Here's an example to get an Item from the content store:

    .. code-block:: text

        http://localhost:8080/api/1/site/content_store/item.json?url=/site/website/index.xml&crafterSite=mysite

    |

--------
Examples
--------
Below are some examples of creating RESTful services in CrafterCMS.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Render Page Components as JSON
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
CaaS (Content as a Service) is a common need in today's multi-channel world. Typically CaaS use cases
require that the content is devoid of presentation markup so that the consumer can present the content
as it desires. Other times consumers may wish to pull rendered content. This provides a simple
REST example (a single Groovy based rest controller script) that will give you a way to render all of the
components associated to a page.

"""""""""""""
Prerequisites
"""""""""""""
* None

""""""""""""""""""""""""""""""""
Step 1: Create a REST Controller
""""""""""""""""""""""""""""""""
* Under Scripts/rest right click and click create controller
	* Enter get-rendered-components.get as the controller name

* Add the following code to the controller.

.. code-block:: groovy
	:linenos:

	import java.io.ByteArrayOutputStream
	import java.io.IOException
	import java.io.PrintWriter

	import javax.servlet.Filter
	import javax.servlet.FilterChain
	import javax.servlet.FilterConfig
	import javax.servlet.ServletException
	import javax.servlet.ServletOutputStream
	import javax.servlet.ServletRequest
	import javax.servlet.ServletResponse
	import javax.servlet.http.HttpServletRequest
	import javax.servlet.http.HttpServletResponse
	import javax.servlet.http.HttpServletResponseWrapper
	import javax.servlet.WriteListener
	import groovy.util.XmlSlurper

	def result = [:]
	def targetPage = params.pageId

	if (targetPage != null) {
		result.page = targetPage

		def pageItem = siteItemService.getSiteItem(targetPage)

		if (pageItem != null) {
			def componentPaths = pageItem.queryValues("//include")
			result.components = []

			if (componentPaths != null) {
				componentPaths.each { componentPath ->
					if (componentPath.endsWith(".xml") && !componentPath.startsWith("/site/website") ) {
						logger.info("Including component ${componentPath} into JSON response")

						def component = [:]
						component.id = componentPath

						// wrap the response to capture the output
						def wrappedResponse = new CapturingResponseWrapper(response)

						// "include" the page that does the actual work
						request.getRequestDispatcher("/crafter-controller/component?path=" + componentPath).include(request, wrappedResponse)

						// get the captured output, parse it and prepare the actual response
						def capturedOut = wrappedResponse.getCaptureAsString()

						component.markup = capturedOut

						result.components.add(component)
					}
				}
			} else {
				result.message = "No components found"
			}
		} else {
			result.message = "Page '${targetPage}` not found"
		}
	} else {
		result.message = "Parameter pageId is required."
	}

	return result

	protected class CapturingResponseWrapper extends HttpServletResponseWrapper {

		private final ByteArrayOutputStream capture
		private ServletOutputStream output
		private PrintWriter writer

		public CapturingResponseWrapper(HttpServletResponse response) {
			super(response)
			capture = new ByteArrayOutputStream(response.getBufferSize())
		}

		@Override
		public ServletOutputStream getOutputStream() {
			if (writer != null) {
				throw new IllegalStateException("getWriter() has already been called on this response.")
			}

			if (output == null) {
				output = new ServletOutputStream() {

					@Override
					public void write(int b) throws IOException {
						capture.write(b)
					}

					@Override
					public void flush() throws IOException {
						capture.flush()
					}

					@Override
					public void close() throws IOException {
						capture.close()
					}

					@Override
					public void setWriteListener(WriteListener writeListener) {
					}

					@Override
					public boolean isReady() {
						return true
					}
				}
			}

			return output
		}

		@Override
		public PrintWriter getWriter() throws IOException {
			if (output != null) {
				throw new IllegalStateException("getOutputStream() has already been called on this response.")
			}

			if (writer == null) {
				writer = new PrintWriter(new OutputStreamWriter(capture, getCharacterEncoding()))
			}

			return writer
		}

		@Override
		public void flushBuffer() throws IOException {
			super.flushBuffer()

			if (writer != null) {
				writer.flush()
			}
			else if (output != null) {
				output.flush()
			}
		}

		public byte[] getCaptureAsBytes() throws IOException {
			if (writer != null) {
				writer.close()
			}
			else if (output != null) {
				output.close()
			}

			return capture.toByteArray()
		}

		public String getCaptureAsString() throws IOException {
			return new String(getCaptureAsBytes(), getCharacterEncoding())
		}

	}

"""""""""""""""""""""""""""
Step 2: Execute the Service
"""""""""""""""""""""""""""
* Open a browser and hit the following URL:
	* ``http://localhost:8080/api/1/services/get-rendered-components.json?pageId=/site/website/index.xml``

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Get the Names of Sites Running in Crafter Engine
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In this example we create a simple RESTful service that returns the list of sites running in Crafter Engine.
You can find the API for the Context Manager `HERE <https://github.com/craftersoftware/craftercms/blob/support/4.x/engine/src/main/java/org/craftercms/engine/service/context/SiteContextManager.java>`_

"""""""""""""
Prerequisites
"""""""""""""
* None

""""""""""""""""""""""""""""""""
Step 1: Create a REST Controller
""""""""""""""""""""""""""""""""
* Under Scripts/rest right click and click create controller
    * Enter get-sites.get as the controller name

* Add the following code to the controller.

.. code-block:: groovy
    :linenos:

    def siteContextManager = applicationContext["crafter.siteContextManager"]
    def siteContextList = siteContextManager.listContexts()
    def siteNames = []

    siteContextList.each { siteContext ->
        def name = siteContext.getSiteName()
        siteNames.add(name)
    }

    return siteNames

"""""""""""""""""""""""""""
Step 2: Execute the Service
"""""""""""""""""""""""""""
* Open a browser and hit the following URL:
    * ``http://localhost:8080/api/1/services/get-sites.json``
    * See results

^^^^^^^^^^^^^^^^^^^^^^^^^^
Get Pages for a Given Site
^^^^^^^^^^^^^^^^^^^^^^^^^^
In this example we create a simple RESTful service that returns the list of Pages in a site.
The service is parameterized to allow the caller to set a starting point and depth.

"""""""""""""
Prerequisites
"""""""""""""
* None

""""""""""""""""""""""""""""""""
Step 1: Create a REST Controller
""""""""""""""""""""""""""""""""
* Under Scripts/rest right click and click create controller
    * Enter get-pages.get as the controller name

* Add the following code to the controller.

.. code-block:: groovy
    :linenos:

    def pathParam = (params.path != null) ? params.path : ""
    def depthParam = (params.depth != null) ? params.depth.toInteger() : 0

    def path = "/site/website" + pathParam
    def depth = depthParam != 0 ? depthParam : 2

    def navItems = [:]
    def siteDir = siteItemService.getSiteTree(path, depth)

    if(siteDir) {
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

    return navItems

"""""""""""""""""""""""""""
Step 2: Execute the Service
"""""""""""""""""""""""""""
* Open a browser and hit the following URL:
    * ``http://localhost:8080/api/1/services/get-pages.json``
    * See results
