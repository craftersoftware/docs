:is-up-to-date: True
:last-updated: 4.0.0


.. index:: Content Inheritance, Inheritance

.. meta::
   :description: Learn how CrafterCMS content inheritance works using level descriptors and merge strategies to share common metadata across content hierarchies.
   :keywords: content inheritance, level descriptor, merge strategy, CrafterCMS, content modeling, inherit-levels, Crafter Engine

.. _content-inheritance:

===================
Content Inheritance
===================

CrafterCMS support content inheritance out of the box, and supports it via a pluggable mechanism that
allows developers to augment or override what's out of the box.

--------------------------
Content Inheritance Basics
--------------------------

Content objects in CrafterCMS are essentially structured markup, XML by default, and house data authored
via Crafter Studio by content authors. Content objects are typically structured as a tree which naturally
suits the notion of inheriting from a parent (not to say that the inheritance mechanics are limited to
that topology). Inheritance works as follows:

Assume we have two objects, one called Parent and one called Child and they're set up as follows:

**Parent**: Below you'll see a typical **level descriptor** which will be the parent of another object.
You'll note the **level descriptor** defines multiple elements that are common to everything at this level
in the hierarchy and below it. This **level descriptor** defines a primary CSS file ``main.css``, a common
header component ``default-header.xml`` and a common footer component ``default-footer.xml``.

.. code-block:: xml
    :linenos:

	<?xml version="1.0" encoding="UTF-8"?>
	<component>
      <content-type>/component/level-descriptor</content-type>
      <display-template/>
      <merge-strategy>inherit-levels</merge-strategy>
      <objectGroupId>4123</objectGroupId>
      <objectId>41d1c0c5-bfc9-8fe8-2461-dc57a82b6cab</objectId>
      <file-name>crafter-level-descriptor.level.xml</file-name>
      <folder-name/>
      <cssGroup>
        <item>
          <key>/static-assets/css/main.css</key>
          <value>/static-assets/css/main.css</value>
          <fileType_s>css</fileType_s>
        </item>
      </cssGroup>
      <jsGroup/>
      <createdDate>2/7/2021 19:40:03</createdDate>
      <lastModifiedDate>10/8/2021 19:58:30</lastModifiedDate>
      <defaultHeader>
        <item>
          <key>/site/components/components/header/default-header.xml</key>
          <value>Default Header</value>
          <include>/site/components/components/header/default-header.xml</include>
          <disableFlattening>false</disableFlattening>
        </item>
      </defaultHeader>
      <defaultFooter>
        <item>
          <key>/site/components/components/footer/default-footer.xml</key>
          <value>Default Footer</value>
          <include>/site/components/components/footer/default-footer.xml</include>
          <disableFlattening>false</disableFlattening>
        </item>
      </defaultFooter>
      <lastModifiedDate_dt>10/8/2021 19:58:30</lastModifiedDate_dt>
	</component>

|

**Child**: Below is the XML file of a page residing under the above **level descriptor** and is setup to inherit from it. You'll note the definition of the ``merge-strategy`` as ``inherit-levels``, this invokes the level-based inheritance mechanics that require CrafterCMS to look at current and higher levels for files named ``crafter-level-descriptor.level.xml`` (this is configurable). You'll also note that this page doesn't specify the CSS file/group of files to include, nor will it need to specify the header nor footer components.

.. code-block:: xml
    :linenos:

	<?xml version="1.0" encoding="UTF-8"?>
	<page>
      <content-type>/page/one-col-parallax</content-type>
      <display-template>/templates/web/pages/one-col-parallax.ftl</display-template>
      <merge-strategy>inherit-levels</merge-strategy>
      <objectGroupId>9cef</objectGroupId>
      <objectId>001f0955-6da3-8b7a-4e6b-6b373139d0ba</objectId>
      <file-name>index.xml</file-name>
      <folder-name>child-page</folder-name>
      <internal-name>Child</internal-name>
      <navLabel>CHILD</navLabel>
      <title>Child Page</title>
      <headerOverlap>no-overlap</headerOverlap>
      <placeInNav>true</placeInNav>
      <orderDefault_f>12000</orderDefault_f>
      <description>This is the Child page.</description>
      <disabled>false</disabled>
      <createdDate>7/31/2021 16:52:39</createdDate>
      <lastModifiedDate>8/1/2021 18:55:09</lastModifiedDate>
      <body>
        <h1>Hello World</h1>
      </body>
	</page>

|

CrafterCMS will invoke the inheritance mechanics implemented in the merge strategy ``inherit-levels`` to merge
the page and the **level descriptor** and the merge strategy will pull in the elements defined in the
**level descriptor** into the child page before handing the new model (XML) to the rendering system. This
means that when the page renders, the model will automatically contain the meta-data defined in the parent
**level descriptor**. In our example above, the page will automatically inherit the meta-data fields
``cssGroup``, ``defaultHeader``, and ``defaultFooter``.

.. note:: When an element is defined by the **level descriptor** and then subsequently defined by a child,
    the child's definition overrides the **level descriptor**.

This mechanism allows you to define meta-data that flows down the information architecture of the project
such that an entire project can have defaults and those defaults can be overwritten by sections individual
page. Some examples of real-life use of inheritance:

* Project logo
* Global stylesheet and JS includes
* Global headers and footers
* Section meta-data (flows to all pages/subsections)

|

.. note:: The ``inherit-levels`` mechanism allows you to set **level descriptors** at various levels of
          the information architecture with lower levels overriding upper levels.

What we discussed thus far is a single inheritance strategy implementation, ``inherit-levels``, the code
to which is available here: `InheritLevelsMergeStrategy.java <https://github.com/craftersoftware/craftercms/blob/support/4.x/core/src/main/java/org/craftercms/core/xml/mergers/impl/strategies/InheritLevelsMergeStrategy.java>`_. There are more inheritance strategies implemented out of the box with CrafterCMS and you can build your own to suit your needs.

-------------------------
Out of the Box Strategies
-------------------------

+-----------------------+------------------------------------------------------------------------+
+-----------------------+------------------------------------------------------------------------+
|| Strategy             || Description                                                           |
+=======================+========================================================================+
|| ``single-file``      || No content should be inherited.                                       |
+-----------------------+------------------------------------------------------------------------+
|| ``inherit-levels``   || Content from Crafter level descriptors (crafter-level-descriptor.xml) |
||                      || in the current and upper levels should be inherited.                  |
+-----------------------+------------------------------------------------------------------------+
|| ``explicit-parent``  || The parent descriptor to inherit is specified explicitly in the XML   |
||                      || tag ``parent-descriptor``.                                            |
+-----------------------+------------------------------------------------------------------------+
|| ``targeted-content`` || The page will be merged with other pages in a targeted content        |
||                      || hierarchy, including level descriptors. For example,                  |
||                      || ``/en_US/about-us`` will generate the following merging list:         |
||                      || ``/en_US/about-us/index.xml``,                                        |
||                      || ``/en_US/about-us/crafter-level-descriptor.xml``,                     |
||                      || ``/en/about-us/index.xml``,                                           |
||                      || ``/en/about-us/crafter-level-descriptor.xml``,                        |
||                      || ``/about-us/index.xml``, ``/about-us/crafter-level-descriptor.xml``,  |
||                      || ``/crafter-level-descriptor.xml``.                                    |
+-----------------------+------------------------------------------------------------------------+

.. _inherit-levels-example:

---------------------------------------------------
Example of Out of the Box Strategy "inherit-levels"
---------------------------------------------------

Let's take a look at an example of the out of the box strategy ``inherit-levels`` used in the Website
Editorial blueprint.

Let's begin by looking at the home page of a project created using the Website Editorial blueprint.
Take note of the top (header) and left (left-rail) side of the page.

.. image:: /_static/images/developer/content-inheritance/home-page-view.webp
    :width: 75%
    :alt: Content Inheritance - Home Page
    :align: center

|

Click on one of the category, say ``Entertainment`` and again take note of the top (header) and
left (left-rail)side of the page.

.. image:: /_static/images/developer/content-inheritance/category-page-view.webp
    :width: 75%
    :alt: Content Inheritance - Home Page
    :align: center

|

The top part of the page is the header and the left side is the left-rail. As shown above, the
two pages we previewed uses the same information for the header, while the left-rail uses the
same information on the top part of it, but different information on the bottom part.

.. image:: /_static/images/developer/content-inheritance/header-leftrail.webp
    :width: 75%
    :alt: Content Inheritance - Home Page
    :align: center

|

The Website Editorial blueprint uses a ``Section Defaults`` component content type (the level descriptor)
to provide inherited values to all children and sibling content items, which for the example we are
working on, is the header and the left-rail. Below is the Section Defaults content type, showing us the
content type as ``/component/level-descriptor`` with the merge strategy ``inherit-levels`` used, and two
components, the header and the left-rail:

.. image:: /_static/images/developer/content-inheritance/section-defaults-content-type.webp
    :width: 75%
    :alt: Content Inheritance - Section Defaults Content Type
    :align: center

|

In the site tree screens below, we have two section defaults (``crafter-level-descriptor.level.xml``) used,
one residing under the **Home** folder, and another residing under **articles** folder.

.. image:: /_static/images/developer/content-inheritance/site-tree-section-default-home.webp
    :width: 30%
    :alt: Content Inheritance - Site tree showing home folder section defaults
    :align: center

.. image:: /_static/images/developer/content-inheritance/site-tree-section-default-articles.webp
    :width: 30%
    :alt: Content Inheritance - Site tree showing articles folder section default
    :align: center

|

As you preview the pages under **Home**, (*Style*, *Health*, *Techonology*, *Entertainment*, *Search Results*)
you'll notice that the header and left-rail displayed is the same for all the pages. Once you preview pages,
under **articles**, we can see the left-rail displayed is now different. This shows us how the Section
Defaults under **articles** has overridden the Section Defaults under **Home**.

Here's the Section Defaults under **Home**

.. code-block:: xml
    :linenos:
    :caption: *Section Defaults under Home*
    :emphasize-lines: 20

    <component>
      <content-type>/component/level-descriptor</content-type>	<display-template/>
      <merge-strategy>inherit-levels</merge-strategy>
      <placeInNav>false</placeInNav>
      <file-name>crafter-level-descriptor.level.xml</file-name>
      <objectGroupId>0a68</objectGroupId>
      <objectId>0a68e8ad-77d8-0a58-e7bf-09a71fb3077b</objectId>
      <folder-name/>
      <header_o>	<item>	<key>/site/components/headers/header.xml</key>
      <value>Header</value>
      <include>/site/components/headers/header.xml</include>
      <disableFlattening>false</disableFlattening>
      </item></header_o>
      <createdDate>2021-3-13T20:26:50.000Z</createdDate>
      <createdDate_dt>2021-3-13T20:26:50.000Z</createdDate_dt>
      <lastModifiedDate>2021-5-18T15:38:58.000Z</lastModifiedDate>
      <lastModifiedDate_dt>2021-5-18T15:38:58.000Z</lastModifiedDate_dt>
      <left-rail_o>	<item>	<key>/site/components/left-rails/left-rail-with-no-articles.xml</key>
      <value>Left Rail with No Articles</value>
      <include>/site/components/left-rails/left-rail-with-no-articles.xml</include>
      <disableFlattening>false</disableFlattening>
      </item></left-rail_o>
    </component>

|

Here's the Section Defaults under **articles**

.. code-block:: xml
    :linenos:
    :caption: *Section Defaults under Home*
    :emphasize-lines: 11

    <component>
      <content-type>/component/level-descriptor</content-type>	<display-template/>
      <merge-strategy>inherit-levels</merge-strategy>
      <objectGroupId>d210</objectGroupId>
      <objectId>d210349e-3f77-95c1-37b3-cab10816347f</objectId>
      <file-name>crafter-level-descriptor.level.xml</file-name>
      <folder-name/>
      <header/>
      <left-rail_o>	<item>	<key>/site/components/left-rails/left-rail-with-related-articles.xml</key>
      <value>Left Rail with Related Articles</value>
      <include>/site/components/left-rails/left-rail-with-related-articles.xml</include>
      <disableFlattening>false</disableFlattening>
      </item></left-rail_o>
      <createdDate>2021-3-17T18:56:59.000Z</createdDate>
      <createdDate_dt>2021-3-17T18:56:59.000Z</createdDate_dt>
      <lastModifiedDate>2021-5-18T15:38:1.000Z</lastModifiedDate>
      <lastModifiedDate_dt>2021-5-18T15:38:1.000Z</lastModifiedDate_dt>
    </component>

|

As we can see from above, the left-rail component used for the Section Defaults under **Home** is different
compared to the left-rail component used for the Section Defaults under **articles**.
If a new article page is created under ``articles/2019/10/27``, it will inherit the Section Defaults under
**articles**.

--------------------------------
Create Your Own Level Descriptor
--------------------------------

Let’s take a look at how to add another level descriptor to the Website Editorial blueprint.

Create a new content type with name such as ``custom-level-descriptor``:

.. image:: /_static/images/developer/content-inheritance/new_level_descriptor_content_type.webp
    :width: 30%
    :alt: Content Inheritance - New level descriptor content type
    :align: center

|

Add ``file-name`` with default value ``crafter-level-descriptor.level`` and check ``Readonly`` option.
You can also add more fields to the content type as needed. In this example, a *Custom Headline* text is added.

.. image:: /_static/images/developer/content-inheritance/new_level_descriptor_form.webp
    :width: 100%
    :alt: Content Inheritance - New level descriptor content type form
    :align: center

|

Go to the Sidebar, create a new folder ``articles2`` under ``Home``, then copy some articles to this new location:

.. image:: /_static/images/developer/content-inheritance/new_level_descriptor_create_folder.webp
    :width: 30%
    :alt: Content Inheritance - New level descriptor create folder
    :align: center

|

Create a new content with type ``/component/custom-level-descriptor`` under ``articles2``:

.. image:: /_static/images/developer/content-inheritance/new_level_descriptor_content.webp
    :width: 100%
    :alt: Content Inheritance - New level descriptor create folder
    :align: center

|

Update ``article.ftl`` template to include a new variable:

.. code-block:: text
    :linenos:

    <#if contentModel.customHeadline_s??>
        <h1>${contentModel.customHeadline_s}</h1>
    </#if>

|

.. image:: /_static/images/developer/content-inheritance/new_level_descriptor_update_template.webp
    :width: 100%
    :alt: Content Inheritance - New level descriptor update template
    :align: center

|

Click to preview article from ``article2`` folder, confirm that new variable from the level descriptor
has been included:

.. image:: /_static/images/developer/content-inheritance/new_level_descriptor_preview.webp
    :width: 100%
    :alt: Content Inheritance - New level descriptor preview
    :align: center

|

.. .. TODO: Describe how to add your own merge strategy. Describe merge cues.


