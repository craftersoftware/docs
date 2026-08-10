:is-up-to-date: True
:last-updated: 4.1.8

.. index:: Templating

.. meta::
   :description: Learn CrafterCMS templating with FreeMarker to transform content models into HTML pages using templates, controllers, and dynamic rendering.
   :keywords: templating, FreeMarker, CrafterCMS, Crafter Engine, HTML rendering, templates, content model, page rendering

.. _templating:

==========
Templating
==========
Templates are a starting point for your pages and components, providing a layout/structure for your content.
It lets you model content as general reusable items. It contains editable sections for Authors to place content into.
Any changes made to the template is immediately available to all pages/components that uses that template.

Templating is the way content is transformed from a template to an HTML page. CrafterCMS embeds FreeMarker to
provide a high-performance, clean, flexible, and tolerant of syntax variance, templating engine to render HTML
directly from CrafterCMS.

Templates typically look like an HTML file but instead of actual content, placeholder variables and other logic is used
in the file. These templates are then used by the templating engine to render the HTML page.

Below is a template file ``entry.ftl`` taken from the Empty Blueprint. Notice the placeholder variables in the file.

.. raw:: html

   <details>
   <summary><a>Empty Blueprint Template file "entry.ftl"</a></summary>

.. code-block:: html
    :force:
    :emphasize-lines: 5,26,27

    <!DOCTYPE html>
    <html lang="en">
	  <head>
		<meta charset="utf-8">
		<title>${contentModel.title_t}</title>
		<style>
          html, body {
            color: #333;
			height: 100%;
			background: #f3f3f3;
			font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
		  }
          main {
			max-width: 800px;
			padding: 40px;
			background: rgba(255,255,255,0.6);
			border-radius: 20px;
			margin: 100px auto;
		  }
		</style>
		<@crafter.head/>
	  </head>
	  <body>
		<@crafter.body_top/>
		<main>
		  <@crafter.h1 $field="title_t">${contentModel.title_t}</@crafter.h1>
		  <@crafter.div $field="body_html">${contentModel.body_html}</@crafter.div>
		</main>
		<@crafter.body_bottom/>
	  </body>
    </html>

.. raw:: html

   </details>

|

After the templating engine renders the above template, the resulting HTML file is below. Notice that the placeholder
variables are now replaced with content.

.. raw:: html

   <details>
   <summary><a>HTML file output from the templating engine</a></summary>

.. code-block:: html
    :emphasize-lines: 5,24,26-36

    <!DOCTYPE html>
    <html lang="en">
	  <head>
		<meta charset="utf-8">
		<title>Hello!</title>
		<style>
		  html, body {
			color: #333;
			height: 100%;
			background: #f3f3f3;
			font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
		  }
		  main {
			max-width: 800px;
			padding: 40px;
			background: rgba(255,255,255,0.6);
			border-radius: 20px;
			margin: 100px auto;
		  }
		</style>
	  </head>
	  <body>
		<main>
          <h1>Hello!</h1>
		  <div>
            <h1>Welcome to Your CrafterCMS Project.</h1>
		    <p>This project blueprint is the equivalent of a Hello World. It's a blank slate on which you can build your own digital experience.</p>
		    <ul>
		      <li>To create new content types use the Project Tools &gt; Content Types menu on the left Sidebar</li>
		      <li>To update markup, edit this template by clicking on the Options menu on the top toolbar and select "Edit Template"</li>
		      <li>To modify this text, hover/click on it or use the options menu and select "Edit" on the top toolbar</li>
		      <li>Crafter documentation can be found <a href="https://docs.craftercms.org" target="_blank" rel="noopener">here</a></li>
		      <li>CrafterCMS authoring and developer training is available. Please contact <a href="mailto:info@craftercms.com">info@craftercms.com</a></li>
		    </ul>
		    <div>
		      <p>This content is managed by the form. &nbsp;Click edit to change OR in the upper right hand corner, click the pencil to turn on edit mode.</p>
		    </div>
	      </div>
		</main>
	  </body>
    </html>

.. raw:: html

   </details>

|

|hr|

----------------------------------
Content Rendering API (FreeMarker)
----------------------------------
When using CrafterCMS in templated mode, each page and component content type generally has its own view. Crafter Engine
uses the FreeMarker templating engine to render these views into the HTML returned to the client.

Review the section :ref:`templating-api` for more information on available variables for your templates and the full API.

In authoring, CrafterCMS provides a UI layer on top of your applications that enables authors with in-context editing
for all the model fields defined in the content types of pages and components, called **Experience Builder (XB)**. In order to
integrate with XB, CrafterCMS provides macros for use in your templates. See the :ref:`Freemarker section in XB<xb-freemarker>`
for more information on using the CrafterCMS provided macros for integrating XB into your application/templates, and
:ref:`content-modeling` for more information on model fields and content types.

|hr|

---------------
Rendering Pages
---------------
Rendering pages is done by Crafter Engine. Simply follow the guidelines in :ref:`templating-api` and :ref:`Freemarker section in XB<xb-freemarker>` when creating your template. In this section, we'll show you a sample template for an article page
that uses the FreeMarker (Templating) API and macros for integrating XB and the output HTML file when rendered.

.. _templating-article-template:

.. raw:: html

   <details>
   <summary><a>Template file for a page showing use of CrafterCMS FreeMarker macros "article.ftl"</a></summary>

.. code-block:: html
    :force:
    :emphasize-lines: 1,11,14,24,33,42
    :linenos:

    <#import "/templates/system/common/crafter.ftl" as crafter />

    <!DOCTYPE HTML>
    <!--
	  Editorial by HTML5 UP
	  html5up.net | @ajlkn
	  Free for personal and commercial use under the CCA 3.0 license (html5up.net/license)
    -->
    <html lang="en">
    <head>
      <#include "/templates/web/fragments/head.ftl">
	  <@crafter.head/>
    </head>
    <body>
      <@crafter.body_top/>

      <!-- Wrapper -->
        <div id="wrapper">
	      <!-- Main -->
	      <div id="main">
		    <div class="inner">

			  <!-- Header -->
			  <@renderComponent component = contentModel.header_o.item />

			  <!-- Content -->
			  <section>
				<header class="main">
                  <@crafter.h1 $field="subject_t">
                    ${contentModel.subject_t!""}
                  </@crafter.h1>
                  <div>
                    by <@crafter.span $field"author_s">${contentModel.author_s!""}</@crafter.span>
                  </div>
				</header>
				<#if contentModel.image_s??>
				  <#assign image = contentModel.image_s/>
				<#else>
				  <#assign image = "/static-assets/images/placeholder.png"/>
				</#if>
				<span class="image main">
                  <@crafter.img $field='image_s' src="${image}" alt=""/>
                </span>

                <@crafter.renderRepeatGroup
                  $field="sections_o"
                  $containerAttributes={'style': 'list-style: none; padding-left: 0;'};
                  item, index
                >
                  <@crafter.div
                    $field="sections_o.section_html"
                    $index=index
                  >
                    ${item.section_html}
                  </@crafter.div>
                  <hr class="major" />
                </@crafter.renderRepeatGroup>
			  </section>
		    </div>
	      </div>

	      <#assign articleCategories = contentModel.queryValues("//categories_o/item/key")/>
	      <#assign articlePath = contentModel.storeUrl />
	      <#assign additionalModel = {"articleCategories": articleCategories, "articlePath": articlePath }/>

	      <!-- Left Rail -->
	      <@renderComponent component = contentModel.left_rail_o.item additionalModel = additionalModel />

        </div>

        <#include "/templates/web/fragments/scripts.ftl">

        <@crafter.body_bottom/>
      </body>
    </html>

.. raw:: html

   </details>

|

In the template above, notice the line importing the CrafterCMS FreeMarker library. This allows you to convert tags to
editable elements by switching each of the tags that represent CrafterCMS content model fields, from plain HTML tags e.g.
``h1`` to a macro tag e.g. ``@crafter.h1`` for XB. As you can see in the highlighted sections of the template, macro tags
are used.

Here is an example of an authoring screen for the example template above with XB integration:

.. image:: /_static/images/developer/templating-sample-xb-integration.webp
   :width: 65 %
   :align: center
   :alt: Preview page for a template with XB integration

|

Below you'll see a rendered HTML file that used the template above. All placeholder variables have been replaced.

.. raw:: html

   <details>
   <summary><a>HTML file output from the templating engine for "article.ftl"</a></summary>

.. code-block:: html
    :force:

    <!DOCTYPE HTML>
    <!--
	  Editorial by HTML5 UP
	  html5up.net | @ajlkn
	  Free for personal and commercial use under the CCA 3.0 license (html5up.net/license)
    -->
    <html lang="en">
    <head>
      <title>Top Clubs In Virginia</title>
      <meta charset="utf-8"/>
      <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no"/>
      <link rel="stylesheet" href="/static-assets/css/font-awesome.min.css?site=ed"/>
      <link rel="stylesheet" href="/static-assets/css/main.css?site=ed"/>
      <link rel="stylesheet" href="/static-assets/css/social-media-widget.css?site=ed"/>
    </head>
    <body>

      <!-- Wrapper -->
      <div id="wrapper">
	    <!-- Main -->
	    <div id="main">
		  <div class="inner">

			<!-- Header -->

            <header
              id="header"
            >
            <a href="/" class="logo">
              <img
                border="0"
                src="/static-assets/images/1-gear.png"
                alt=""
              >
              Howdy, stranger
            </a>
            <div
              data-craftercms-type="collection"
            >
              <div>
                <div>
                  <ul
                    class="social-media-container inline"
                    data-craftercms-type="collection"
                  >
                    <li
                      class="social-media-item"
                      >
                      <a
                        href="#"
                        target="_blank"
                        >
                        <img
                          src="/static-assets/images/social-media-widget/facebook.svg"
                          width="24"
                          height="24"
                        >
                      </a>
                    </li>
                    <li
                      class="social-media-item"
                      >
                      <a
                        href="#"
                        target="_blank"
                        >
                        <img
                          src="/static-assets/images/social-media-widget/twitter.svg"
                          width="24"
                          height="24"
                        >
                      </a>
                    </li>
                    <li
                      class="social-media-item"
                      >
                      <a
                        href="#"
                        target="_blank"
                        >
                        <img
                          src="/static-assets/images/social-media-widget/github.svg"
                          width="24"
                          height="24"
                        >
                      </a>
                    </li>
                  </ul>
                </div>
              </div>
            </div>
          </header>

		  <!-- Content -->
          <section>
		    <header class="main">
              <h1
                >Top Clubs In Virginia
              </h1>
              <div>
                by
                <span>John Doe</span>
              </div>
			</header>
			<span class="image main">
              <img
                src="/static-assets/images/clubs-virginia-pic.jpg"
                alt=""
                >
            </span>

            <div
              style="list-style: none; padding-left: 0;"
              data-craftercms-type="collection"
              >
              <div>
                <div>
                <p>
                  Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Etiam a elit justo. Morbi sagittis pulvinar tristique. Aenean pharetra risus a libero faucibus, iaculis porta mi ornare. Ut suscipit efficitur ultrices. Vestibulum odio enim, luctus consectetur diam nec, molestie porta mi. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. Fusce molestie mattis felis, eget aliquet nisi ultricies dapibus. Donec congue nibh nec nisi mattis, sit amet tempor tellus sollicitudin. Mauris accumsan ipsum leo, consectetur pretium tellus aliquet in. Praesent egestas lectus non sollicitudin gravida. Curabitur faucibus consectetur nulla, nec maximus nisl venenatis vel. Nulla sit amet felis quis dui imperdiet fermentum nec eget sapien. In porta, augue eget porta aliquam, massa tellus hendrerit lectus, et lobortis nulla libero ut eros. Ut cursus efficitur libero, vel accumsan odio tincidunt vitae. Donec ligula dui, lacinia eget nunc ac, gravida blandit dolor.
                </p>
                <p>Suspendisse malesuada, libero rhoncus interdum dictum, enim leo blandit elit, ut varius ligula urna at tortor. Mauris porta tellus quis nisl ultricies, a euismod justo varius. In cursus mi id suscipit commodo. Nulla in luctus mi, a aliquam turpis. Integer efficitur a magna id volutpat. Aenean vel eleifend nulla. Sed et bibendum neque. Etiam a quam ut ligula vehicula ornare. Quisque ultrices quam dui, ut facilisis urna aliquet id. Maecenas porta ipsum pretium turpis efficitur finibus. Etiam pulvinar ipsum ac turpis ornare feugiat. Curabitur placerat eu dui at consequat. Aliquam gravida, est rutrum iaculis cursus, risus urna tincidunt nunc, sed tempus felis justo nec tortor. Proin rutrum magna mi, in consectetur turpis rhoncus sit amet.
                </p>
              </div>
              <hr class="major" />
            </div>
            <div>
              <div>
                <p>
                  Aliquam bibendum eros vel libero tincidunt, ac consectetur tortor blandit. Proin auctor odio neque. Duis vel consectetur nunc, nec hendrerit justo. Nunc tristique fermentum odio ac bibendum. Fusce egestas risus at vehicula efficitur. Ut eleifend, nisl quis vestibulum dapibus, magna nisl pulvinar purus, quis vestibulum augue tortor nec neque. Phasellus fringilla pretium ipsum vitae hendrerit. Praesent posuere erat et odio pulvinar vehicula. Suspendisse sit amet dapibus justo, at rutrum justo. Duis ac erat quis felis porttitor gravida. Fusce ac neque augue. Aliquam rutrum rutrum eros vitae varius. Phasellus sit amet pharetra velit, sit amet scelerisque neque. Integer eget mauris mauris. Etiam luctus, orci non pharetra egestas, nunc turpis congue sem, eu semper nisi sapien vel lorem. Ut at pellentesque libero.</p>
                <p>Curabitur vestibulum, odio vel lacinia faucibus, turpis elit imperdiet elit, et pellentesque leo sem ut lorem. Aliquam lacinia maximus lectus, at ultrices nulla tempus a. Suspendisse vestibulum nibh et pulvinar finibus. Integer ac consequat lectus. Nullam venenatis varius ante vel elementum. Integer nisi odio, condimentum vitae semper eu, commodo at enim. Maecenas fringilla lorem vel arcu luctus, in feugiat lectus mollis. Curabitur a ligula nulla. Pellentesque gravida accumsan nunc, et semper dolor ornare nec. Etiam faucibus risus id neque euismod, vel scelerisque ligula malesuada.</p>
                <p>In fringilla ultrices nisi ac consectetur. Cras at venenatis libero. Praesent vestibulum vitae purus sit amet feugiat. Praesent elementum blandit magna, id pulvinar velit vestibulum ac. Praesent mattis nulla nec risus gravida accumsan ac in eros. Proin tempus tellus mi, id egestas urna euismod pharetra. Vestibulum tellus odio, venenatis quis nisl id, venenatis facilisis nibh. Praesent feugiat sapien vitae ligula posuere, non ullamcorper ante commodo. Mauris sed risus eget ante sollicitudin molestie ut at dui. Donec in massa nisi. Vestibulum vestibulum ante nisl, quis tincidunt massa efficitur ut. Curabitur diam est, pretium id congue id, volutpat non lectus. Vestibulum dictum urna ac hendrerit varius.
                </p>
              </div>
              <hr class="major" />
            </div>
          </div>
	    </section>
	  </div>
	</div>

	<!-- Left Rail -->

    <div
        id="sidebar"
    >  <div class="inner">

    <!-- Search -->
    <section id="search" class="alt">
      <form method="post" action="#">
        <input type="text" name="query" id="query" placeholder="Search"/>
      </form>
    </section>

    <!-- Menu -->
    <nav id="menu">
      <header class="major">
        <h2>Menu</h2>
      </header>

      <ul >
        <li class="  ">
          <a
            href="/"
            class=" "
            > Home
          </a>
        </li>

        <li class="  ">
          <a
            href="/style"
            class=" "
            >  Style
          </a>
        </li>

        <li class="  ">
          <a
            href="/health"
            class=" "
            >  Health
          </a>
        </li>

        <li class="  ">
          <a
            href="/entertainment"
            class=" "
            >  Entertainment
          </a>
        </li>

        <li class="  ">
          <a
            href="/technology"
            class=" "
            >  Technology
          </a>
        </li>
      </ul>

    </nav>

    <!-- Widgets -->
    <div
      data-craftercms-type="collection"
      >
      <div>
        <section>
          <header class="major">
            <h2>Related Articles</h2>
          </header>
          <div class="mini-posts">
            <article>
              <a href="/articles/2021/2/top-romantic-valentine-movies" class="image">
                <img src="/static-assets/images/romantic-pic.jpg" alt="" />
              </a>
              <h4>
                <a href="/articles/2021/2/top-romantic-valentine-movies"
                  >  Top Romantic Valentine Movies
                </a>
              </h4>
            </article>
            <article>
              <a href="/articles/2020/12/top-books-for-young-women" class="image">
                <img src="/static-assets/images/book-woman-pic.jpg" alt="" />
              </a>
              <h4>
                <a href="/articles/2020/12/top-books-for-young-women"
                  >Top Books For Young Women
                </a>
              </h4>
            </article>
          </div>
        </section>
      </div>
      <div>
        <section>
          <header class="major">
            <h2>Contact Us</h2>
          </header>
          <div>
            <p><span>Sed varius enim lorem ullamcorper dolore aliquam aenean ornare velit lacus, ac varius enim lorem ullamcorper dolore. Proin sed aliquam facilisis ante interdum. Sed nulla amet lorem feugiat tempus aliquam.</span></p>
          </div>
          <ul class="contact">
            <li class="icon solid fa-envelope">
              <a
                href="mailto:info@example.com"
                >info@example.com</a>
            </li>
            <li
              class="icon solid fa-phone"
              >  (999) 999-9999
            </li>
            <li
              class="icon solid fa-home"
              >  <p>5321 Somewhere Road #789<br />Reston, Virginia</p>
            </li>
          </ul>
        </section>
      </div>
    </div>
    <!-- /Widgets -->

    <!-- Footer -->
    <footer id="footer">
      <p class="copyright">
        &copy; Untitled. All rights reserved. Demo Images:
        <a href="https://unsplash.com">Unsplash</a>. Design: <a href="https://html5up.net">HTML5 UP</a>.
      </p>
    </footer>
    <!-- /Footer -->

    </div>
    </div>

    </div>

    <!-- Scripts -->
    <script src="/static-assets/js/jquery.min.js?site=ed"></script>
    <script src="/static-assets/js/browser.min.js"></script>
    <script src="/static-assets/js/breakpoints.min.js"></script>
    <script src="/static-assets/js/util.js"></script>
    <script src="/static-assets/js/main.js?site=ed"></script>
    <!-- /Scripts -->

    </body>
    </html>

.. raw:: html

   </details>

|

|hr|

--------------------
Rendering Components
--------------------
CrafterCMS provides a macro for rendering components, :ref:`renderComponent`.
In the example template ``article.ftl`` :ref:`above<templating-article-template>`, line 24 shows an example of using `renderComponent`.

See :ref:`renderComponent` for more information on the macro.

^^^^^^^^^^^^^^^^^^^^^^^^^^^
Rendering Nested Components
^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each component has it's own template. Rendering nested components is similar to rendering a component and no special
steps are required. Below is an example of a component named ``feature`` with a nested component named ``quote``.

.. raw:: html

   <details>
   <summary><a>Template file with a nested component "feature.ftl"</a></summary>

.. code-block:: html
    :force:
    :emphasize-lines: 14

    <#import "/templates/system/common/crafter.ftl" as crafter />

    <!-- Feature Component -->
    <@crafter.article class="feature">
      <@crafter.span class="icon ${contentModel.icon_s}" $field="icon_s"/>
      <div class="content">
        <@crafter.h3 $field="title_t">
          ${contentModel.title_t}
        </@crafter.h3>
        <@crafter.div $field="body_html">
          ${contentModel.body_html}
        </@crafter.div>

        <@crafter.renderComponentCollection $field="quote_o" />
      </div>
    </@crafter.article>
    <!-- /Feature Component -->

.. raw:: html

   </details>

|


.. raw:: html

   <details>
   <summary><a>Template file "quote.ftl" for "quote" component nested in the "feature" component above</a></summary>

.. code-block:: html
    :force:

    <#import "/templates/system/common/crafter.ftl" as crafter />

    "<@crafter.em $field="quoteText_t">${contentModel.quoteText_t}</@crafter.em>"

.. raw:: html

   </details>

|

|hr|

----------------
More Information
----------------
CrafterCMS provides some out-of-the-box blueprints that shows examples of templated projects:

- `Editorial Blueprint <https://github.com/craftersoftware/craftercms/tree/support/4.x/studio/src/main/webapp/repo-bootstrap/global/blueprints/1000_website_editorial>`_
- `commerceTools Blueprint <https://github.com/craftercms/commercetools-blueprint>`_
- `Wordify Blueprint <https://github.com/craftercms/wordify-blueprint>`_

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Using FreeMarker Date Built-ins
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The article :ref:`working-with-dates-in-freemarker` describes how to use the Freemarker date built-ins

.. toctree::
   :maxdepth: 1
   :titlesonly:
   :hidden:

   working-with-dates-freemarker
