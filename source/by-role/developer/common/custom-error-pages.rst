:is-up-to-date: True
:last-updated: 4.1.5

.. meta::
   :description: Create custom error pages in CrafterCMS to handle 404, 403, 500 and other HTTP errors with user-friendly templates for your Crafter Engine project.
   :keywords: custom error pages, 404, 500, error handling, CrafterCMS, Crafter Engine, FreeMarker, error templates

.. _custom-error-pages:

==================
Custom Error Pages
==================
When an error in a browser request occurs in your application, what the user sees in their browser, called an *error page*
depends on what has been configured in your application. The most common error pages are client (4xx) and server (5xx) errors,
for status code such as:

- ``400 Bad Request`` - This error appears when the request is invalid
- ``401 Authentication Required`` - This error indicates lack of authorization credentials
- ``403 Forbidden`` - This error appears when you don't have the necessary permissions to access a page
- ``404 Not Found`` - This error indicates the server couldn't find the requested page
- ``500 Internal Server Error`` - This errors appears when the server encountered a problem and could not complete the request
- ``502 Bad Gateway`` - This error indicates some network connectivity problem
- ``503 Service Unavailable`` - This error indicates the server is not available for usage due to temporary overloading or a maintenance issue

Default error pages are most of the time unsightly, containing the exception/error details and maybe some stack trace
and other information that may be useful to developers but confusing for users.

Custom error pages allows you to handle unexpected situations in your site/application in a user-friendly way. It makes
it easier for users who stumble across errors in your site/application to continue their session and get back on track
by informing users that something has gone wrong and then provides them with options on how to continue.

CrafterCMS provides default templates for error pages such as ``404 Not Found``, ``403 Forbidden`` and others, located in
``CRAFTER_HOME/bin/apache-tomcat/webapps/studio/WEB_INF/templates/web/errors``
and/or ``CRAFTER_HOME/bin/apache-tomcat/webapps/ROOT/WEB_INF/templates/web/errors``. These default error
pages are displayed when no custom error pages have been created for the corresponding error status code in your project.

Below is an example of the default error page displayed by CrafterCMS for a ``404`` error:

.. image:: /_static/images/system-admin/craftercms-default-error-page.webp
   :align: center
   :width: 65 %
   :alt: Default CrafterCMS 404 Not Found Error Page

|

CrafterCMS supports using custom error pages. When Crafter Engine detects an error trying to fulfill a request it will
also look for a custom error page to display in the browser. If it doesn't find a custom error page in the project, it
will display the default error page provided by CrafterCMS mentioned above.

To add custom errors page in your project, do the following:

1. Create a new folder under ``/templates/web/errors`` on the Sidebar
2. Create a Freemarker template using as name the error code for which the page will be displayed, e.g., ``404.ftl``

In the custom error templates developers are free to include any HTML/CSS/JS to make sure that the page matches
the rest of the site.

.. note::
   Custom Error pages are standalone templates, they are not associated with any site item and will not have
   the ``model`` object available.

.. note::
   A valid context is required for custom error pages to render.  If Crafter Engine is not able to load your
   site context due to a configuration or some other error condition, you will receive a default system error page.

Once the file is saved it will be used automatically in preview, for a delivery node you need to publish
the new file so that it takes effect in the live site.

|

-------
Example
-------
Let's take a look at examples of custom error pages. For this example, we will be using a project created using the
`Website Editorial` blueprint. We'll look at the custom error page for ``404 Not Found`` status code provided in the
Website Editorial blueprint, and then create a custom error page for ``403 Forbidden`` status code.

The Website Editorial blueprint provides example custom error pages for ``404`` and ``500``, so we don't need to create
the folder ``templates/web/errors/``. Open the Sidebar and navigate to ``templates/web/errors/``. Let's take a look at
the ``404`` custom error page by clicking on ``404.ftl`` then clicking on ``Edit``.

.. raw:: html

   <details>
   <summary><a>Sample "404.ftl"</a></summary>

.. rli:: https://raw.githubusercontent.com/craftersoftware/craftercms/support/4.x/studio/src/main/webapp/repo-bootstrap/global/blueprints/1000_website_editorial/templates/web/errors/404.ftl
   :caption: *CRAFTER_HOME/data/repos/sites/SITENAME/sandbox/templates/web/errors/404.ftl*
   :linenos:

.. raw:: html

   </details>

|

Notice that in the template file above, the header used for the Website Editorial is used in the page, to give the custom
error page a cohesive look with the site. Below is how the above custom error page looks like:

.. figure:: /_static/images/error-page-custom.webp
        :alt: Website Editorial Custom Error Page 404 Not Found
        :align: center
        :width: 75 %

        Default Website Editorial blueprint custom error page.

|

Next, let's create a custom error page for ``403 Forbidden`` status code. In the Sidebar, navigate to ``/templates/web/errors``
then right click on it. Select ``New Template``, then type in ``403.ftl`` as the File Name. We can copy the contents of
the ``404.ftl`` file, and modify it for the 403 status code by adding an image and text to let the user know that the
page they're trying to reach is forbidden.

.. code-block:: html
    :force:
    :linenos:
    :caption: *403.ftl*

    ...
    <!-- Header -->
	<@renderComponent componentPath="/site/components/headers/header.xml" />

    <div style="text-align: center">
      <br/>
      <img src="/static-assets/images/no-entry.jpg" alt="No entry sign" style="width: 55vw; min-width: 330px;display: block; margin-left: auto; margin-right: auto;"/>
      <br/>
      <header><h1>Sorry!</h1></header>
      This area is forbidden. Go back <a href="/">home</a> now
    </div>
    ...

Below is how the above ``403 Forbidden`` custom error page looks like:

.. figure:: /_static/images/custom-403-error-page.webp
        :alt: Website Editorial 403 Forbidden Custom Error Page
        :align: center
        :width: 75 %

        Custom 403 Forbidden error page.

Remember to publish the new ``403.ftl`` file so that it takes effect in the live site.